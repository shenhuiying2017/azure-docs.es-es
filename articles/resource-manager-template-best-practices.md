<properties
	pageTitle="Procedimientos recomendados para la plantilla de Resource Manager | Microsoft Azure"
	description="Instrucciones para simplificar las plantillas del Azure Resource Manager."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="tomfitz"/>

# Procedimientos recomendados para crear plantillas de Azure Resource Manager

Las instrucciones siguientes lo ayudarán a crear plantillas de Resource Manager confiables y fáciles de usar. El único propósito de estas instrucciones es servir de sugerencias, no como requisitos absolutos. El escenario puede requerir variaciones con respecto a estas instrucciones.

## Nombres de recurso

Por lo general, trabajará con tres tipos de nombres de recurso:

1. Nombres de recurso que deben ser únicos.
2. Nombres de recurso que no tienen que ser únicos, pero cuyo nombre debe ayudar a identificar el contexto.
3. Nombres de recurso que pueden ser genéricos.

Para obtener ayuda en el establecimiento de una convención de nomenclatura, consulte [Infrastructure naming guidelines](./virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md) (Instrucciones para la nomenclatura de las infraestructuras). Para obtener información sobre las restricciones de los nombres de recurso, consulte [Recommended naming conventions for Azure resources](./guidance/guidance-naming-conventions.md) (Convenciones de nomenclatura recomendadas para los recursos de Azure).

### Nombres de recurso únicos

Debe dar un nombre de recurso único para cualquier tipo de recurso que tenga un punto de conexión de acceso a datos. Entre los tipos comunes que requieren un nombre único se incluyen los siguientes:

- Cuenta de almacenamiento
- Sitio web
- SQL Server
- Almacén de claves
- Caché de Redis
- Cuenta de Lote
- Administrador de tráfico
- Servicio de búsqueda
- Clúster de HDInsight

Además, los nombres de las cuentas de almacenamiento deben estar en minúsculas, tener como máximo veinticuatro caracteres y no incluir guiones.

En lugar de dar un parámetro para estos nombres de recurso y al tratar de idear un nombre único durante la implementación, puede crear una variable que use la función [uniqueString()](resource-group-template-functions.md#uniquestring) para generar un nombre. Con frecuencia, también deberá agregar un prefijo o sufijo al resultado **uniqueString** para determinar el tipo de recurso con mayor facilidad con solo mirar el nombre. Por ejemplo, puede generar un nombre único para una cuenta de almacenamiento con la siguiente variable.

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

Las cuentas de almacenamiento con un prefijo de uniqueString no se agruparán en clústeres en los mismos bastidores.

### Nombres de recurso para la identificación

En los tipos de recursos que a los debe dar un nombre, pero este no tiene que ser único, basta con dar un nombre que identifica su contexto y el tipo recurso. Deberá dar un nombre descriptivo que lo ayude a reconocerlo en una lista de nombres de recurso. Si tiene que variar el nombre del recurso durante las implementaciones, utilice un parámetro para el nombre:

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

Si no tiene que pasar ningún nombre durante la implementación, utilice una variable:

    "variables": {
        "vmName": "demoLinuxVM"
    }

O bien un valor codificado de forma rígida:

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### Nombres de recurso genéricos

En los tipos de recurso a los que se accede mayoritariamente por medio de otro recurso, puede utilizar un nombre genérico que esté codificado de forma rígida en la plantilla. Por ejemplo, es probable que no desee dar un nombre personalizable a las reglas del firewall de un servidor SQL Server.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## Parámetros

1. Minimice los parámetros siempre que sea posible. Si puede utilizar una variable o un valor literal, hágalo. Proporcione parámetros únicamente en los casos siguientes:
 - Ajustes que desee variar en función del entorno (por ejemplo, SKU, tamaño o capacidad)
 - Nombres de recurso que desee especificar para facilitar su identificación
 - Valores que utilice con frecuencia para completar otras tareas (por ejemplo, el nombre de usuario del administrador)
 - Secretos (por ejemplo, las contraseñas)
 - El número o la matriz de valores que se usarán al crear varias instancias de un tipo de recurso

1. Los nombres de parámetro deben utilizar **mayúsculas intercaladas**.

1. Dé una descripción de los metadatos de cada parámetro.

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Defina los valores predeterminados de los parámetros (excepto en el caso de las contraseñas y claves SSH).

        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }

1. Utilice **securestring** en todas las contraseñas y todos los secretos.

        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
 
1. Siempre que sea posible, evite utilizar un parámetro para especificar el campo **location**. En lugar de ello, utilice la propiedad de ubicación del grupo de recursos. Si utiliza la expresión **resourceGroup () .location** en todos los recursos, los recursos de la plantilla se implementarán en la misma ubicación que el grupo de recursos.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
  
     Si un tipo de recurso solo se admite en un número limitado de ubicaciones, considere la posibilidad de especificar una ubicación válida directamente en la plantilla. Si debe utilizar un parámetro de ubicación, comparta el valor de dicho parámetro al máximo con los recursos que probablemente estén en la misma ubicación. Este método minimiza las probabilidades de que los usuarios tengan que dar ubicaciones para todos los tipos de recurso.

1. Evite usar un parámetro o una variable en la versión de API de un tipo de recurso. Las propiedades y los valores de los recursos pueden variar en función del número de la versión. La función IntelliSense de los editores de código no podrá determinar el esquema correcto si la versión de API se establece como un parámetro o una variable. En lugar de ello, debe codificar de forma rígida la versión de la API en la plantilla.

## Variables 

1. Use las variables para los valores que deba utilizar más de una vez en una plantilla. Si un valor se usa solo una vez, codificarlo de forma rígida hará que la plantilla resulte más fácil de leer.

1. No se puede utilizar la función [reference](resource-group-template-functions.md#reference) en la sección de variables. La función reference deriva su valor del estado en tiempo de ejecución del recurso, pero las variables se resuelven durante el análisis inicial de la plantilla. En lugar de ello, debe construir valores que requieran la función **reference** directamente en las secciones de **recursos** o de **salidas** de la plantilla.

1. Incluya variables en los nombres de recurso que deban ser únicos, como se muestra en [Nombres de recurso](#resource-names).

1. Puede agrupar las variables en objetos complejos. Puede hacer referencia a un valor de un objeto complejo en el formato **variable.subentry**. Agrupar las variables lo ayudará a realizar un seguimiento de las variables relacionadas y mejorará la legibilidad de la plantilla.

        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
 
     > [AZURE.NOTE] Los objetos complejos no pueden contener una expresión que haga referencia a un valor de un objeto complejo. Defina una variable independiente para tal fin.

     Para obtener ejemplos más avanzados del uso de objetos complejos como variables, consulte [Uso compartido del estado en las plantillas de Azure Resource Manager](best-practices-resource-manager-state.md).

## Recursos

1. Especifique **comments** en cada recurso de la plantilla para ayudar a otros colaboradores a comprender el propósito del recurso.

        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]

1. Utilice etiquetas para agregar a los recursos metadatos, que le permiten incorporar información adicional sobre los recursos correspondientes. Por ejemplo, puede agregar metadatos a un recurso con el fin de agregar datos de facturación. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

1. Si utiliza un **punto de conexión público** en la plantilla (por ejemplo, un punto de conexión público de almacenamiento de blobs), **no codifique de forma rígida** el espacio de nombres. Utilice la función **reference** para recuperar el espacio de nombres de forma dinámica. De este modo, podrá implementar la plantilla en diversos entornos de espacios de nombres públicos, sin cambiar manualmente el punto de conexión de la plantilla. Establezca el valor apiVersion en la misma versión que utiliza para el valor storageAccount de la plantilla.

        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Si la cuenta de almacenamiento se implementa en la misma plantilla, no tendrá que especificar el espacio de nombres del proveedor al hacer referencia al recurso. La sintaxis simplificada es la siguiente:
     
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }

     Si tiene otros valores en la plantilla configurada con un espacio de nombres público, cámbielos para reflejar la misma función reference. Por ejemplo, la propiedad storageUri de diagnosticsProfile de la máquina virtual.

        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
 
     También puede utilizar la función **reference** para hacer referencia a una cuenta de almacenamiento existente en otro grupo de recursos.


        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. Asigne publicIPAddresses a una máquina virtual solo cuando lo requiera una aplicación. Si desea conectarse para fines de depuración, administración o administrativos, use inboundNatRules, virtualNetworkGateways o un jumpbox.

     Para obtener más información sobre cómo conectarse a máquinas virtuales, consulte:
     - [Running VMs for an N-tier architecture on Azure](./guidance/guidance-compute-3-tier-vm.md) (Ejecución de máquinas virtuales para una arquitectura de n niveles en Azure)
     - [Configuración de acceso a WinRM para máquinas virtuales en Azure Resource Manager](./virtual-machines/virtual-machines-windows-winrm.md)
     - [Habilitación del acceso externo a la máquina virtual mediante el Portal de Azure](./virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
     - [Habilitación del acceso externo a la máquina virtual mediante PowerShell](./virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
     - [Apertura de puertos y puntos de conexión](./virtual-machines/virtual-machines-linux-nsg-quickstart.md)

1. La propiedad **domainNameLabel** de publicIPAddresses públicas debe ser única. domainNameLabel debe contener entre tres y sesenta y tres caracteres, y seguir las reglas especificadas por esta expresión regular `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Dado que la función uniqueString generará una cadena de trece caracteres de largo, en el ejemplo siguiente se asume que se ha comprobado la cadena de prefijo dnsPrefixString para que no tenga más de cincuenta caracteres y cumpla estas reglas.

        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }

1. Al agregar una contraseña a una **customScriptExtension**, utilice la propiedad **commandToExecute** en protectedSettings.

        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }

     > [AZURE.NOTE] Para garantizar que se cifran los secretos que se pasan como parámetros a virtualMachines/extensions, se debe usar la propiedad protectedSettings de las extensiones pertinentes.

## Salidas

Si una plantilla de cualquier crea cualquier **publicIPAddresses** nueva, deberá tener una sección de **salida** que dé detalles de la dirección IP y el dominio completo creados de forma que estos detalles se puedan recuperar fácilmente tras la implementación. Cuando haga referencia a un recurso, utilice la versión de API usada para crearlo.

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## Plantilla única o plantillas anidadas

Para implementar la solución, puede utilizar una sola plantilla o una plantilla principal con varias plantillas anidadas. Las plantillas anidadas son comunes en escenarios más avanzados. Las plantillas anidadas aportan las ventajas siguientes:

1. Permiten descomponer la solución en componentes específicos.
2. Permiten volver a usar las plantillas anidadas con distintas plantillas principales.

Si decide descomponer el diseño de la plantilla en varias plantillas anidadas, las instrucciones siguientes lo ayudarán a estandarizar el diseño. Estas instrucciones se basan en el documento [Prácticas recomendadas para diseñar plantillas de Azure Resource Manager](best-practices-resource-manager-design-templates.md). El diseño recomendado consta de las siguientes plantillas.

+ **Plantilla principal** (azuredeploy.json). Se utiliza para los parámetros de entrada.
+ **Plantilla de recursos compartidos**. Implementa los recursos compartidos que usan todos los demás recursos (por ejemplo, la red virtual y los conjuntos de disponibilidad). La expresión dependsOn exige que esta plantilla se implemente antes que las demás plantillas.
+ **Plantilla de recursos opcionales**. Implementa recursos condicionalmente a partir de un parámetro (por ejemplo, un jumpbox).
+ **Plantilla de recursos de miembros**. Cada tipo de instancia dentro de una capa de aplicación tiene su propia configuración. Dentro de un nivel, se pueden definir diversos tipos de instancia (por ejemplo: la primera instancia crea un nuevo clúster y se agregan instancias adicionales al clúster existente). Cada tipo de instancia tendrá su propia plantilla de implementación.
+ **Scripts**. Se pueden aplicar scripts ampliamente reutilizables a cada tipo de instancia (por ejemplo, para inicializar y formatear discos adicionales). Los scripts personalizados creados para un fin de personalización específico son distintos en función del tipo de instancia.

![plantilla anidada](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Para más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

## Vinculación condicional a una plantilla anidada

Puede realizar vinculaciones condicionales a las plantillas anidadas mediante un parámetro que pasa a formar parte del identificador URI de la plantilla.

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## Formato de plantilla

1. Es conveniente pasarle a la plantilla un validador de JSON para quitar paréntesis, corchetes y comas extraños que pueden producir un error durante la implementación. Pruebe [JSONlint](http://jsonlint.com/) o un paquete de linter para su entorno de edición favorito (Visual Studio Code, Atom, Sublime Text, Visual Studio, etc.).
1. También es conveniente dar formato al JSON para mejorar la legibilidad. Puede utilizar un paquete de formateador de JSON para el editor local. En Visual Studio, puede dar formato al documento con **CTRL+K, CTRL+D**. En el código de VS, utilice **Alt+Mayús+ F**. Si el editor local no puede dar formato al documento, puede utilizar un [formateador en línea](https://www.bing.com/search?q=json+formatter).

## Pasos siguientes

1. Si desea obtener una guía para la creación de la arquitectura de su solución para máquinas virtuales, consulte [Running a Windows VM on Azure](./guidance/guidance-compute-single-vm.md) (Ejecución de una máquina virtual Windows en Azure) y [Running a Linux VM on Azure](./guidance/guidance-compute-single-vm-linux.md) (Ejecución de una máquina virtual Linux en Azure).
2. Si desea obtener una guía para la configuración de una cuenta de almacenamiento, consulte [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](./storage/storage-performance-checklist.md).
3. Para obtener ayuda con las redes virtuales, consulte [Networking infrastructure guidelines](./virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md) (Instrucciones para la infraestructura de redes).

<!---HONumber=AcomDC_0720_2016-->