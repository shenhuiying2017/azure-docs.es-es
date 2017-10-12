---
title: Procedimientos recomendados para crear plantillas de Resource Manager | Microsoft Docs
description: Instrucciones para simplificar las plantillas del Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Procedimientos recomendados para crear plantillas de Azure Resource Manager
Estas instrucciones pueden ayudarle a crear plantillas de Azure Resource Manager confiables y fáciles de usar. Estas instrucciones son solo sugerencias. No son requisitos, excepto si así se indica. El escenario podría requerir una variación de uno de los siguientes enfoques o ejemplos.

## <a name="resource-names"></a>Nombres de recurso
Por lo general, trabaja con tres tipos de nombres de recursos en Resource Manager:

* Nombres de recurso que deben ser únicos.
* Nombres de recursos que no deben ser únicos, pero elige proporcionar un nombre que pueda ayudarlo a identificar un recurso según el contexto.
* Nombres de recurso que pueden ser genéricos.

 Para obtener información sobre las restricciones de los nombres de recurso, consulte [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md)(Convenciones de nomenclatura recomendadas para los recursos de Azure).

### <a name="unique-resource-names"></a>Nombres de recurso únicos
Debe dar un nombre de recurso único para cualquier tipo de recurso que tenga un punto de conexión de acceso a datos. Entre los tipos de recursos comunes que requieren un nombre único se incluyen los siguientes:

* Azure Storage<sup>1</sup> 
* Característica Web Apps de Azure App Service
* SQL Server
* Almacén de claves de Azure
* Caché en Redis de Azure
* Azure Batch
* Administrador de tráfico de Azure
* Búsqueda de Azure
* HDInsight de Azure

<sup>1</sup> Los nombres de las cuentas de almacenamiento deben estar en minúsculas, tener 24 caracteres o menos y no incluir guiones.

Si proporciona un parámetro para un nombre de recurso, debe proporcionar un nombre único cuando implementa el recurso. De manera opcional, puede crear una variable que usa la función [uniqueString()](resource-group-template-functions-string.md#uniquestring) para generar un nombre. 

También puede que quiera agregar un prefijo o sufijo al resultado **uniqueString**. Modificar el nombre único podría ayudarlo a identificar más fácilmente el tipo de recurso a partir del nombre. Por ejemplo, puede generar un nombre único para una cuenta de almacenamiento si usa la variable siguiente:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nombres de recurso para la identificación
Algunos tipos de recursos a los que podría querer asignar un nombre, pero no es necesario que los nombres sean únicos. En el caso de estos tipos de recursos, puede proporcionar un nombre que identifique tanto el contexto del recurso como el tipo de recurso. Proporcione un nombre descriptivo que ayude a identificar el recurso en una lista de recursos. Si necesita usar un nombre de recurso distinto para implementaciones diferentes, puede usar un parámetro para el nombre:

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

Si no tiene que pasar ningún nombre durante la implementación, puede usar una variable: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

También puede usar un valor codificado de forma rígida:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>Nombres de recurso genéricos
En los tipos de recurso a los que tiene acceso mayoritariamente por medio de otro recurso, puede usar un nombre genérico que esté codificado de forma rígida en la plantilla. Por ejemplo, puede establecer un nombre genérico estándar para reglas de firewall en un servidor SQL:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>Parámetros
La información siguiente puede ser útil cuando se trabaja con parámetros:

* Minimice el uso de los parámetros. Siempre que sea posible, use una variable o un valor literal. Solo use parámetros en estos escenarios:
   
   * La configuración que desea para usar variaciones de la misma según el entorno (SKU, tamaño, capacidad).
   * Nombres de recurso que desea especificar para facilitar la identificación.
   * Valores que usa con frecuencia para completar otras tareas (como un nombre de usuario administrador).
   * Secretos (como contraseñas).
   * El número o matriz de valores que se usarán cuando cree varias instancias de un tipo de recurso.
* Use una mezcla de mayúsculas y minúsculas para los nombres de parámetro.
* Proporcione una descripción de cada parámetro en los metadatos:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina los valores predeterminados de los parámetros (excepto en el caso de contraseñas y claves SSH):
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Use **SecureString** para todas las contraseñas y secretos: 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Siempre que sea posible, no use un parámetro para especificar la ubicación. En lugar de ello, use la propiedad **location** del grupo de recursos. Si usa la expresión **resourceGroup().location** en todos los recursos, los recursos de la plantilla se implementan en la misma ubicación que el grupo de recursos:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Si un tipo de recurso solo se admite en un número limitado de ubicaciones, puede que quiera especificar una ubicación válida directamente en la plantilla. Si debe usar un parámetro **location**, comparta el valor de ese parámetro al máximo con los recursos que probablemente estén en la misma ubicación. Con esto se minimiza la cantidad de veces que se les pide a los usuarios que proporcionen información de ubicación.
* Evite usar un parámetro o una variable en la versión de API de un tipo de recurso. Las propiedades y los valores de los recursos pueden variar en función del número de la versión. La función IntelliSense en un editor de código no puede determinar el esquema correcto si la versión de API se establece como un parámetro o una variable. En lugar de ello, debe codificar de forma rígida la versión de la API en la plantilla.

## <a name="variables"></a>variables
La información siguiente puede ser útil cuando se trabaja con variables:

* Use las variables para los valores que deba utilizar más de una vez en una plantilla. Si un valor se usa solo una vez, codificarlo de forma rígida hace que la plantilla resulte más fácil de leer.
* No se puede usar la función [reference](resource-group-template-functions-resource.md#reference) en la sección **variables** de la plantilla. La función **reference** deriva su valor desde el estado de tiempo de ejecución del recurso. Sin embargo, las variables se resuelven durante el análisis inicial de la plantilla. Construya valores que requieran la función **reference** directamente en las secciones **resources** u **outputs** de la plantilla.
* Incluya variables para los nombres de recurso que deben ser únicos, como se describe en [Nombres de recurso](#resource-names).
* Puede agrupar las variables en objetos complejos. Use el formato **variable.subentry** para hacer referencia un valor desde un objeto complejo. Agrupar las variables puede ayudarlo a hacer seguimiento de variables relacionadas. También mejora la legibilidad de la plantilla. Este es un ejemplo:
   
   ```json
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
   ```
   
   > [!NOTE]
   > Los objetos complejos no pueden contener una expresión que haga referencia a un valor de un objeto complejo. Defina una variable independiente para tal fin.
   > 
   > 
   
     Para ejemplos avanzados de cómo usar objetos complejos como variables, consulte [Uso compartido de estado en plantillas de Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>Recursos
La información siguiente puede ser útil cuando se trabaja con recursos:

* Para ayudar a otros colaboradores a comprender el propósito del recurso, especifique **comments** para cada recurso de la plantilla:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Puede usar etiquetas para agregar metadatos a los recursos. Use metadatos para agregar información sobre los recursos. Por ejemplo, puede agregar metadatos para registrar los detalles de facturación de un recurso. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).
* Si usa un *punto de conexión público* en la plantilla (como un punto de conexión público de Azure Blob Storage), *no codifique de forma rígida* el espacio de nombres. Use la función **reference** para recuperar dinámicamente el espacio de nombres. Puede usar este enfoque para implementar la plantilla en diversos entornos de espacios de nombres públicos sin cambiar manualmente el punto de conexión de la plantilla. Establezca la versión de API en la misma versión que usa para la cuenta de almacenamiento de la plantilla:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si la cuenta de almacenamiento se implementa en la misma plantilla que se está creando, no necesita especificar el espacio de nombres del proveedor cuando haga referencia al recurso. La siguiente es la sintaxis simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si tiene otros valores en la plantilla que estén configurados para usar un espacio de nombres público, cámbielos para que reflejen la misma función **reference**. Por ejemplo, puede establecer la propiedad **storageUri** del perfil de diagnóstico de la máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   También puede hacer referencia a una cuenta de almacenamiento existente que se encuentra en un grupo de recursos distinto:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Asigne direcciones IP públicas a una máquina virtual solo cuando lo requiera una aplicación. Para conectarse a una máquina virtual (VM) para la depuración o con fines administrativos, use reglas NAT de entrada, una puerta de enlace de red o Jumpbox.
   
     Para obtener más información sobre cómo conectarse a máquinas virtuales, consulte:
   
   * [Ejecución de máquinas virtuales para una arquitectura de n niveles en Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configuración de acceso a WinRM para máquinas virtuales en Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Habilitación del acceso externo a la máquina virtual mediante Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Habilitación del acceso externo a la máquina virtual mediante PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Habilitación del acceso externo a la máquina virtual Linux mediante la CLI de Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* La propiedad **domainNameLabel** para las direcciones IP públicas debe ser única. El valor **domainNameLabel** debe contener entre 3 y 63 caracteres y seguir las reglas especificadas por esta expresión regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como la función **uniqueString** genera una cadena de 13 caracteres, el parámetro **dnsPrefixString** se limita a no más de 50 caracteres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Cuando agrega una contraseña a una extensión de script personalizada, use la propiedad **commandToExecute** en la propiedad **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantizar que los secretos se cifran cuando se transmiten como parámetros a máquinas virtuales y extensiones, use la propiedad **protectedSettings** de las extensiones pertinentes.
   > 
   > 

## <a name="outputs"></a>Salidas
Si usa una plantilla para crear direcciones IP públicas incluyen una sección **outputs** que devuelve detalles de la dirección IP y el nombre de dominio completo (FQDN). Puede usar valores de salida para recuperar fácilmente los detalles sobre las direcciones IP públicas y los nombres FQDN después de la implementación. Cuando haga referencia al recurso, use la versión de API que usó para crearlo: 

```json
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

## <a name="single-template-vs-nested-templates"></a>Comparación entre plantilla única y plantillas anidadas
Para implementar la solución, puede utilizar una sola plantilla o una plantilla principal con varias plantillas anidadas. Las plantillas anidadas son comunes en escenarios más avanzados. Usar una plantilla anidada le ofrece las siguientes ventajas:

* Puede descomponer una solución en componentes específicos.
* Puede volver a usar plantillas anidadas con distintas plantillas principales.

Si elige usar plantillas anidadas, las siguientes instrucciones pueden ayudarlo a estandarizar el diseño de plantilla. Estas instrucciones se basan en [patrones para diseñar plantillas de Azure Resource Manager](best-practices-resource-manager-design-templates.md). Se recomienda un diseño con las siguientes plantillas:

* **Plantilla principal** (azuredeploy.json). Úsela para los parámetros de entrada.
* **Plantilla de recursos compartidos**. Úsela para implementar los recursos compartidos que todos los recursos usan (por ejemplo, conjuntos de disponibilidad y red virtual). Use la expresión **dependsOn** para asegurarse de que esta plantilla se implemente antes que otras.
* **Plantilla de recursos opcionales**. Úsela para implementar condicionalmente recursos basados en un parámetro (por ejemplo, un jumpbox).
* **Plantilla de recursos de miembros**. Cada tipo de instancia dentro de una capa de aplicación tiene su propia configuración. Dentro de un nivel, puede definir los distintos tipos de instancia. (Por ejemplo, la primera instancia crea un clúster y se agregan instancias adicionales al clúster existente). Cada tipo de instancia tiene su propia plantilla de implementación.
* **Scripts**. Se pueden aplicar scripts ampliamente reutilizables a cada tipo de instancia (por ejemplo, para inicializar y formatear discos adicionales). Los scripts personalizados que crea con un fin de personalización específico son distintos, basados en el tipo de instancia.

![Plantilla anidada](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Para más información, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Vinculación condicional a plantillas anidadas
Puede usar un parámetro para vincularse condicionalmente a plantillas anidadas. El parámetro pasa a formar parte del identificador URI de la plantilla:

```json
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
```

## <a name="template-format"></a>Formato de plantilla
Es conveniente transferir la plantilla a través de un validador JSON. Un validador puede ayudarle a quitar comas, paréntesis y corchetes extraños que pueden provocar un error durante la implementación. Pruebe [JSONLint](http://jsonlint.com/) o un paquete de linter para el entorno de edición de su preferencia (Visual Studio Code, Atom, Sublime Text, Visual Studio).

También es conveniente dar formato al JSON para mejorar la legibilidad. Puede utilizar un paquete de formateador de JSON para el editor local. En Visual Studio, presione **Ctrl+K, Ctrl+D** para dar formato al documento. En Visual Studio Code, presione **Alt+Mayús+F**. Si el editor local no puede dar formato al documento, puede utilizar un [formateador en línea](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Pasos siguientes
* Para instrucciones sobre cómo diseñar la solución para las máquinas virtuales, consulte [Ejecución de una máquina virtual Windows en Azure](../guidance/guidance-compute-single-vm.md) y [Ejecución de una máquina virtual Linux en Azure](../guidance/guidance-compute-single-vm-linux.md).
* Si desea obtener instrucciones sobre cómo configurar una cuenta de almacenamiento, consulte [Lista de comprobación de rendimiento y escalabilidad de Azure Storage](../storage/common/storage-performance-checklist.md).
* Para información sobre cómo una empresa puede usar Resource Manager para administrar suscripciones de manera eficaz, consulte [Scaffold empresarial de Azure: gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

