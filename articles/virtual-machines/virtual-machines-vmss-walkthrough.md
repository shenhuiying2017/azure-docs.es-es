<properties
	pageTitle="Escalado automático de conjuntos de escalado de máquinas virtuales | Microsoft Azure"
	description="Empiece a crear y administrar sus primeros conjuntos de escalado de máquinas virtuales de Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


Este documento es una guía rápida para empezar a crear y administrar sus primeros conjuntos de escalado de máquinas virtuales de Azure durante la vista previa pública.

En la guía, se da por supuesto que ya conoce el uso de las plantillas del Administrador de recursos de Azure (ARM) para implementar recursos de Azure como máquinas virtuales y redes virtuales. De lo contrario, los tres primeros vínculos de la sección Recursos proporcionan una introducción al Administrador de recursos.

## ¿Qué son los conjuntos de escalado de máquinas virtuales y por qué usarlos?

Los conjuntos de escalado de máquinas virtuales son un recurso de procesos de Azure que puede usar para implementar y administrar un conjunto de máquinas virtuales idénticas.

Con todas las máquinas virtuales configuradas de la misma manera, los conjuntos de escalado de máquinas virtuales están diseñados para admitir el escalado automático verdadero (no es necesario aprovisionar las máquinas virtuales antes) y, por tanto, facilitan la creación de servicios a gran escala cuyo objetivo son las cargas de trabajo en contenedor, de macroproceso y macrodatos, y cualquier aplicación que necesite escalar los recursos de procesos, ya sea escalarlos o reducirlos horizontalmente. Las operaciones de escalado se equilibran implícitamente entre dominios de error y de actualización. Para obtener una introducción a los conjuntos de escalado de máquinas virtuales, consulte el reciente [anuncio en el blog de Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview).

## Creación de conjuntos de escalado de máquinas virtuales y trabajo con ellos

Los conjuntos de escalado de máquinas virtuales se pueden definir e implementar mediante plantillas JSON y llamadas REST igual que las máquinas virtuales individuales del Administrador de recursos de Azure. Por lo tanto, pueden usarse los métodos de implementación estándar del Administrador de recursos de Azure, y este documento lo guiará por algunos ejemplos.

Las áreas específicas de la integración de los conjuntos de escalado de máquinas virtuales, como la compatibilidad con comandos imperativos en lenguajes de programación y scripting, y la integración completa en el portal están en desarrollo y se presentarán en fases durante la vista previa.

## Implementación y administración de conjuntos de escalado de máquinas virtuales con el Portal de Azure

En un principio, no se puede crear un conjunto de escalado de máquinas virtuales desde cero en el Portal de Azure. Se está trabajando en la compatibilidad con el portal y, en la primera fase de este trabajo, solo se pueden ver en el portal los conjuntos de escalado que ya creó. Además, tendrá que adoptar un enfoque de plantilla o script para crearlos. En todos los casos, se da por supuesto que "portal" se refiere al nuevo Portal de Azure: [https://portal.azure.com/](https://portal.azure.com/).

Sin embargo, puede emplear la capacidad del portal de implementar una plantilla para implementar cualquier recurso, incluidos los conjuntos de escalado. Una manera fácil de implementar un conjunto de escalado sencillo es usar un vínculo como este:

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Se puede encontrar un conjunto de plantillas de ejemplo para conjuntos de escalado de máquinas virtuales en el repositorio GitHub de plantillas de inicio rápido de Azure aquí: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Busque las plantillas con _vmss_ en el título.

En el archivo Léame de estas plantillas, verá un botón como este que se vincula con la característica de implementación con el portal.

Para implementar el conjunto de escalado, haga clic en el botón y después rellene los parámetros que son necesarios en el portal. Nota: Si no está seguro de si un recurso admite mayúsculas o la mezcla de mayúsculas y minúsculas, es más seguro usar siempre los valores de parámetro en minúsculas.

**Supervisión del recurso de conjunto de escalado de máquinas virtuales en el portal o el Explorador de recursos**

Una vez implementado un conjunto de escalado, se mostrará una vista básica de él en el portal pero en un principio, durante la vista previa, no se mostrarán las propiedades detalladas ni se permitirá profundizar en las máquinas virtuales que se ejecutan en el conjunto de escalado de máquinas virtuales.

Hasta que se aplique la integración completa en el portal, se recomienda usar el **Explorador de recursos de Azure**: [https://resources.azure.com](https://resources.azure.com) para ver los conjuntos de escalado de máquinas virtuales. Los conjuntos de escalado de máquinas virtuales son un recurso en Microsoft.Compute, por lo que en este sitio puede verlos si expande los vínculos siguientes:

Suscripciones -> su suscripción -> resourceGroups -> proveedores -> Microsoft.Compute -> virtualMachineScaleSets -> su conjunto de escalado de máquinas virtuales -> etc.

## Implementación y administración de conjuntos de escalado de máquinas virtuales mediante PowerShell

Puede implementar plantillas de conjunto de escalado de máquinas virtuales y recursos de consulta con cualquier versión actual de Azure PowerShell.

**Nota importante:** Los ejemplos que se muestran a continuación son para [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/), que introdujo el prefijo _AzureRm_ para muchos comandos. Si está usando una versión anterior de PowerShell, como 0.9.8 o anterior, quite "**Rm**" de los comandos de ejemplo. En los ejemplos también se supone que ya estableció una conexión de inicio de sesión a Azure en su entorno de PowerShell (_Login-AzureRmAccount_).

Ejemplos:

&#35; **Crear un grupo de recursos**

New-AzureRmResourcegroup -name myrg -location 'Sudeste de Asia'

&#35; **Crear un conjunto de escalado de máquinas virtuales con 2 máquinas virtuales**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Se le pedirá cualquier parámetro que falte (por ejemplo, la ubicación, el nombre de usuario o la contraseña en este ejemplo).

&#35; **Obtener datos del conjunto de escalado de máquinas virtuales**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; o para obtener más detalles, canalícelo a través de | ConvertTo-Json -Depth 10

&#35; o para tener aún más detalles, agregue –debug al comando.

& #35; Escalado o reducción horizontales

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

& #35; **Quitar un conjunto de escalado**

& #35; Fácil: quite el grupo de recursos completo y todo lo que contiene:

Remove-AzureRmResourceGroup -Name myrg

& #35; Preciso: quite un recurso específico:

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Comandos de PowerShell imperativos para conjuntos de escalado

Una próxima compilación de Azure PowerShell incluirá un conjunto de comandos imperativos para crear y administrar conjuntos de escalado de máquinas virtuales sin usar plantillas. Esta compilación está disponible en la vista previa aquí: [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Aquí se pueden encontrar ejemplos de cómo usar la API imperativa:

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Implementación y administración de conjuntos de escalado de máquinas virtuales mediante la CLI de Azure

Puede implementar plantillas de conjuntos de escalado de máquinas virtuales y recursos de consulta con cualquier versión actual de la CLI de Azure.

La manera más fácil de instalar la CLI es desde un contenedor de Docker. Para instalarla, consulte: [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/).

Para usar la CLI, consulte: [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/).

### Ejemplos de CLI de conjunto de escalado de máquinas virtuales

&#35; **crear un grupo de recursos**

azure group create myrg "Sudeste de Asia"

&#35; **crear un conjunto de escalado de máquinas virtuales**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

& #35; esto debería pedir parámetros dinámicamente o podría especificarlos como argumentos

&#35; **obtener datos del conjunto de escalado**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; o para obtener más detalles:

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

Una compilación de la CLI de Azure próxima incluirá comandos imperativos para implementar y administrar conjuntos de escalado de máquinas virtuales directamente sin plantillas y realizar operaciones en máquinas virtuales en conjuntos de escalado de máquinas virtuales. Puede realizar un seguimiento de esta compilación aquí: [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/).

## Plantillas de conjunto de escalado de máquinas virtuales

Esta sección lo guía por una plantilla simple de Azure para crear un conjunto de escalado de máquinas virtuales y la contrasta con las plantillas que se usan para crear máquinas virtuales de instancia única. También hay una práctica explicación en vídeo de una plantilla de conjunto de escalado de máquinas virtuales aquí: [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player).

### Anatomía de una plantilla

Empecemos con una plantilla simple que crea un conjunto de escalado de máquinas virtuales con máquinas virtuales de Ubuntu y desglosemos sus componentes. Este ejemplo también crea los recursos de los que depende un conjunto de escalado de máquinas virtuales, como la red virtual, la cuenta de almacenamiento, etc. Este ejemplo se puede encontrar aquí: [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json). Tenga en cuenta que, para este ejemplo de tipo "Hola a todos", no habrá una manera directa de conectarse a las máquinas virtuales en el conjunto de escalado de máquinas virtuales desde fuera de la red virtual, ya que las máquinas virtuales tienen asignadas direcciones IP internas. Consulte la sección de escenarios y ejemplos de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) para ver formas de conectarse desde fuera mediante equilibradores de carga o equipos de inicio de sesión seguro.

### Encabezado de la plantilla

Especifique el esquema y la versión del contenido:

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Parámetros

Como con cualquier plantilla del Administrador de recursos de Azure (ARM), en esta sección se definen los parámetros que se especifican durante la implementación, lo que en este ejemplo incluye el nombre del conjunto de escalado de máquinas virtuales, el número de instancias de máquina virtual con las que empezar y una cadena única que se usará al crear cuentas de almacenamiento (use siempre minúsculas al escribir valores para objetos como cuentas de almacenamiento a menos que sepa si se acepta algo diferente).

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Variables

Un componente estándar de la plantilla del ARM que define las variables a las que se hará referencia más adelante en la plantilla. En este ejemplo vamos a usar variables para definir qué sistema operativo queremos, algunos detalles de la configuración de red, la configuración de almacenamiento y la ubicación. Para ubicación, usaremos la función _location()_, que la tomará del grupo de recursos donde se va a implementar.

Tenga en cuenta que se define una matriz de cadenas únicas para un prefijo de cuenta de almacenamiento. Consulte la sección Almacenamiento para obtener una explicación.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Recursos

En esta sección se define cada tipo de recurso.

````
   "resources": [
````


**Almacenamiento.** Cuando se crea un conjunto de escalado de máquinas virtuales, se especifica una matriz de cuentas de almacenamiento. Después, los discos de sistema operativo para las instancias de máquina virtual se distribuirán uniformemente entre cada cuenta. En el futuro, los conjuntos de escalado de máquinas virtuales pasarán a adoptar un enfoque de disco administrado donde no es necesario administrar cuentas de almacenamiento y, en su lugar, basta con describir las propiedades de almacenamiento como parte de la definición del conjunto de escalado. Por ahora, creará tantas cuentas de almacenamiento como necesite por adelantado. Recomendamos crear 5 cuentas de almacenamiento, que admitirán cómodamente hasta 100 máquinas virtuales en un conjunto de escalado (el máximo actual).

Cuando se crea un conjunto de cuentas de almacenamiento, se distribuyen entre las particiones en un sello de almacenamiento y el esquema de distribución depende de las primeras letras del nombre de la cuenta de almacenamiento. Por este motivo, para lograr un rendimiento óptimo, se recomienda que cada cuenta de almacenamiento que cree empiece por una letra diferente del alfabeto. Puede asignarles nombre manualmente o, para este ejemplo, use la función uniqueString() para proporcionar una distribución pseudoaleatoria:

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Red virtual.** Cree una red virtual. Tenga en cuenta que puede tener varios conjuntos de escalado, así como máquinas virtuales individuales, en la misma red virtual.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "properties": {

        "addressSpace": {

          "addressPrefixes": [

            "[variables('addressPrefix')]"

          ]

        },

        "subnets": [

          {

            "name": "[variables('subnetName')]",

            "properties": {

              "addressPrefix": "[variables('subnetPrefix')]"

            }

          }

        ]

      }

    },
````

### El recurso virtualMachineScaleSets

En muchos aspectos, el recurso _virtualMachineScaleSets_ es como un recurso _virtualMachines_. Ambos los proporciona el proveedor de recursos Microsoft.Compute y se encuentran en el mismo nivel. La principal diferencia es que se proporciona un valor _capacity_ que indica cuántas máquinas virtuales se están creando, valor que se define aquí para todas las máquinas virtuales en el conjunto de escalado de máquinas virtuales.

Observe cómo la sección _dependsOn_ hace referencia a las cuentas de almacenamiento y la red virtual creadas arriba y capacity hace referencia al parámetro _instanceCount_.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Propiedades**

Los conjuntos de escalado de máquinas virtuales poseen una configuración upgradePolicy. Las versiones futuras serán compatibles con las actualizaciones segmentadas (por ejemplo, cambiar la configuración del 20% de mis máquinas virtuales a la vez), pero por ahora se puede establecer upgradePolicy en manual o automática. Manual significa que, si cambia la plantilla y vuelve a implementarla, los cambios solo surtirán efecto cuando se creen nuevas máquinas virtuales o se actualicen las extensiones, por ejemplo, al aumentar _capacity_. Automática significa que se actualizan todas las máquinas virtuales en una "ráfaga" y se reinicia todo. La opción manual suele ser un enfoque más seguro. Puede eliminar máquinas virtuales individuales y volver a implementarlas según sea necesario para actualizarlas gradualmente.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Propiedades -> virtualMachineProfile**

Aquí hace referencia a las cuentas de almacenamiento creadas antes como contenedores para máquinas virtuales. No es necesario crear antes los contenedores en sí, solo las cuentas.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Propiedades -> osProfile**

Una diferencia entre los conjuntos de escalado de máquinas virtuales y las máquinas virtuales individuales es que el nombre del equipo es un prefijo. Las instancias de máquina virtual en el conjunto de escalado de máquinas virtuales se crearán con nombres como: myvm\_0, myvm\_1, etc.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Propiedades -> networkProfile**

Al definir el perfil de red para un conjunto de escalado de máquinas virtuales, recuerde que la configuración de NIC y la configuración IP tienen nombre. La configuración de NIC tiene una configuración "_primary_" y el id. de subred hace referencia al recurso de subred que se creó como parte de la red virtual arriba.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Propiedades -> extensionProfile**

El ejemplo anterior no requiere un perfil de extensión. Puede ver uno en acción en esta plantilla que implementa Apache y PHP con la extensión CustomScript: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale). Tenga en cuenta que en este ejemplo las propiedades de IP de red también hacen referencia a un equilibrador de carga. Los equilibradores de carga se describirán con más detalle en la sección Escenarios con conjuntos de escalado de máquinas virtuales.

## Escenarios con conjuntos de escalado de máquinas virtuales

En esta sección, se explican algunos escenarios típicos con conjuntos de escalado de máquinas virtuales y plantillas de ejemplo. Aunque por ahora requieren plantillas, algunos se integrarán en el portal en el futuro. Además, algunos servicios de Azure de niveles superiores (por ejemplo, Lote, Service Fabric, servicio Contenedor de Azure) usarán estos escenarios.

## RDP o SSH para instancias de conjunto de escalado de máquinas virtuales

Se crea un conjunto de escalado de máquinas virtuales dentro de una red virtual y a las máquinas virtuales individuales no se les asignan direcciones IP públicas. Esto es algo bueno, ya que normalmente no desea los costos ni la sobrecarga administrativa que supone la asignación de direcciones IP independientes a todos los recursos sin estado en la cuadrícula de procesos y puede conectarse fácilmente a estas máquinas virtuales desde otros recursos en la red virtual, incluidas aquellas con direcciones IP públicas, como los equilibradores de carga o las máquinas virtuales independientes.

Aquí se describen dos maneras sencillas de conectarse a máquinas virtuales de un conjunto de escalado de máquinas virtuales:

### Conexión a máquinas virtuales mediante reglas NAT

Puede crear una dirección IP pública, asignarla a un equilibrador de carga y definir reglas NAT entrantes que asignen un puerto de la dirección IP a un puerto de una máquina virtual en el conjunto de escalado de máquinas virtuales. Por ejemplo,

IP pública->Puerto 50000 -> vmss\_0->Puerto 22 IP pública->Puerto 50001 -> vmss\_1->Puerto 22 IP pública->Puerto 50002-> vmss\_2->Puerto 22

Aquí hay un ejemplo de creación de un conjunto de escalado de máquinas virtuales que usa reglas NAT para permitir la conexión SSH a cada máquina virtual de un conjunto de escalado mediante una sola dirección IP pública: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).

Este es un ejemplo que hace lo mismo con RDP y Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat).

### Conexión a máquinas virtuales mediante un "equipo de inicio de sesión seguro"

Si crea un conjunto de escalado de máquinas virtuales y una máquina virtual independiente en la misma red virtual, la máquina virtual independiente y las máquinas virtuales del conjunto de escalado de máquinas virtuales pueden conectarse entre sí mediante sus direcciones IP internas como se define en la red virtual y la subred.

Si crea una dirección IP pública y la asigna a la máquina virtual independiente, puede conectarse mediante RDP o SSH a la máquina virtual independiente y después conectarse desde esa máquina a las instancias del conjunto de escalado de máquinas virtuales. En este punto, es posible que observe que un simple conjunto de escalado de máquinas virtuales es de forma inherente más seguro que una simple máquina virtual independiente con una dirección IP pública en su configuración predeterminada.

Para obtener un ejemplo de este enfoque, esta plantilla crea un clúster sencillo de Mesos compuesto por una máquina virtual maestra independiente que administra un clúster de máquinas virtuales basado en un conjunto de escalado de máquinas virtuales: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json).

## Equilibrio de carga round robin en instancias de conjunto de escalado de máquinas virtuales

Si desea entregar trabajo a un clúster de proceso de máquinas virtuales con un enfoque "round robin", puede configurar un equilibrador de carga de Azure con reglas de equilibrio de carga según corresponda. También puede definir sondeos para comprobar que la aplicación se esté ejecutando; estos hacen ping a puertos con un protocolo, un intervalo y una ruta de acceso de solicitud especificados.

Este es un ejemplo que crea un conjunto de escalado de máquinas virtuales con máquinas virtuales que ejecutan un servidor web de IIS y usa un equilibrador de carga para equilibrar la carga que recibe cada máquina virtual. También usa el protocolo HTTP para hacer ping a una dirección URL específica en cada máquina virtual: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). Examine el tipo de recurso Microsoft.Network/loadBalancers, networkProfile y extensionProfile en virtualMachineScaleSet.

## Instancias de conjunto de escalado de máquinas virtuales con Escalado automático de Azure

Si desea variar el número de instancias (_capacity_) del conjunto de escalado de máquinas virtuales según el uso de CPU, memoria o disco, u otros eventos, existe un amplio conjunto de configuraciones de escalado automático en el proveedor de recursos Microsoft.Insights. Puede leer sobre las configuraciones disponibles en la documentación de REST de Insights: [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

El escalado automático de Insights se integra directamente con los conjuntos de escalado de máquinas virtuales. Para configurarlo, se define un tipo de recurso Microsoft.Insights/autoscaleSettings que tiene un _targetResourceUri_ y un _metricResourceUri_ que hacen referencia al conjunto de escalado. Cuando se define el conjunto de escalado, se incluye un _extensionProfile_ que instala el agente de Diagnósticos de Azure (WAD) en Windows o la extensión de diagnósticos de Linux (LDE) en Linux. Aquí hay un ejemplo de Linux que agrega máquinas virtuales hasta un límite predefinido cuando el promedio de uso de la CPU es >50% con una granularidad de tiempo de 1 minuto durante un período de muestreo de 5 minutos:

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

En el futuro, el escalado automático con conjuntos de escalado de máquinas virtuales también se integrará directamente con el Portal de Azure.

## Implementación de un conjunto de escalado de máquinas virtuales como un clúster de proceso en un administrador de clústeres PaaS

A veces, los conjuntos de escalado de máquinas virtuales se describen como un rol de trabajo de próxima generación. Es una descripción válida pero también existe el riesgo de confundir las características del conjunto de escalado con las del rol de trabajo v1 de PaaS.

En cierto sentido, los conjuntos de escalado de máquinas virtuales proporcionan un verdadero "rol de trabajo" o recurso de trabajo, pues suministran un recurso de proceso generalizado que no depende de la plataforma ni del tiempo de ejecución, es personalizable y se integra en IaaS del Administrador de recursos de Azure.

Un rol de trabajo de PaaS v1, aunque está limitado en cuanto a la compatibilidad con la plataforma y el tiempo de ejecución (solo imágenes de la plataforma Windows), incluye servicios (como intercambio de VIP, parámetros de actualización configurables o una configuración específica para la implementación de aplicaciones y para tiempo de ejecución) que no están _todavía_ disponibles en los conjuntos de escalado de máquinas virtuales o que se ofrecerán con otros servicios de PaaS de nivel superior como Service Fabric. Teniendo esto en cuenta, puede considerar los conjuntos de escalado de máquinas virtuales como una infraestructura que admite PaaS, es decir, las soluciones de PaaS como Service Fabric o los administradores de clústeres como Mesos pueden partir de los conjuntos de escalado de máquinas virtuales como una capa de proceso escalable.

Este es un ejemplo de un clúster de Mesos que implementa un conjunto de escalado de máquinas virtuales en la misma red virtual que una máquina virtual independiente. La máquina virtual independiente es maestra en Mesos y el conjunto de escalado de máquinas virtuales representa un conjunto de nodos subordinados: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Las versiones futuras del [servicio Contenedor de Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) implementarán versiones más complejas o reforzadas de este escenario basado en conjuntos de escalado de máquinas virtuales.

## Escalado y reducción verticales de un conjunto de escalado de máquinas virtuales

Para aumentar o disminuir el número de máquinas virtuales en un conjunto de escalado de máquinas virtuales, basta con cambiar la propiedad _capacity_ y volver a implementar la plantilla. Esta sencillez facilita la escritura de su propia capa de escalado personalizada si quiere definir eventos personalizados de escalado que no sean compatibles con el escalado automático de Azure.

Si va a volver a implementar una plantilla para modificar la capacidad, puede definir una plantilla mucho más pequeña que solo incluya la SKU y la capacidad actualizada. Aquí se muestra un ejemplo de esto: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## Guía sobre el escalado y el rendimiento del conjunto de escalado de máquinas virtuales

- Durante la vista previa pública, no cree más de 500 máquinas virtuales en varios conjuntos de escalado de máquinas virtuales a la vez. Este límite aumentará en el futuro.
- No planee más de 40 máquinas virtuales por cuenta de almacenamiento.
- Distancie las primeras letras de los nombres de las cuentas de almacenamiento lo más posible. Las plantillas de conjunto de escalado de máquinas virtuales de ejemplo en la página de [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/) muestran cómo hacerlo.
- Si usa máquinas virtuales personalizadas, no planee más de 40 máquinas virtuales por conjunto de escalado de máquinas virtuales en una única cuenta de almacenamiento. Necesitará copiar antes la imagen en la cuenta de almacenamiento para poder comenzar la implementación del conjunto de escalado de máquinas virtuales. Consulte las preguntas más frecuentes para obtener más información.
- No planee más de 2048 máquinas virtuales por red virtual. Este límite aumentará en el futuro.
- El número de máquinas virtuales que se pueden crear está limitado por la cuota de núcleos de proceso en cualquier región. Puede que necesite ponerse en contacto con soporte al cliente para aumentar el límite de cuota de proceso incluso si ya tiene un límite alto de núcleos para su uso con los servicios en la nube o IaaS v1. Para consultar la cuota, puede ejecutar el siguiente comando en la CLI de Azure: _azure vm list-usage_, y el siguiente comando de PowerShell: _Get-AzureRmVMUsage_ (si usa una versión de PowerShell anterior a 1.0, use _Get-AzureVMUsage_).


## Preguntas más frecuentes sobre los conjuntos de escalado de máquinas virtuales

**P. ¿Cuántas máquinas virtuales puede tener en un conjunto de escalado de máquinas virtuales?**

R: 100 si usa imágenes de plataforma que se puedan distribuir entre varias cuentas de almacenamiento. Si usa imágenes personalizadas, hasta 40, porque las imágenes personalizadas se limitan a una única cuenta de almacenamiento durante la vista previa.

**¿Qué otros límites de recursos existen para los conjuntos de escalado de máquinas virtuales?**

R: Se aplican los límites de servicio de Azure existentes: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).

También existe un límite de creación de no más de 500 máquinas virtuales en varios conjuntos de escalado por región durante el período de vista previa.

**P. ¿Se admiten discos de datos en los conjuntos de escalado de máquinas virtuales?**

R: No en la versión inicial. Las opciones para almacenar datos son:

- Archivos de Azure (unidades compartidas SMB)

- Unidad de sistema operativo

- Unidad temporal (local, no respaldada por almacenamiento de Azure)

- Servicio de datos externo (por ejemplo, base de datos remota, tablas de Azure, blobs de Azure)

**P. ¿Qué regiones de Azure admiten conjuntos de escalado de máquinas virtuales?**

R: Cualquier región que admita el Administrador de recursos de Azure admite conjuntos de escalado de máquinas virtuales.

**P. ¿Cómo se crea un conjunto de escalado de máquinas virtuales con una imagen personalizada?**

R: Deje la propiedad vhdContainers en blanco; por ejemplo:

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**P. Si reduzco la capacidad de mi conjunto de escalado de máquinas virtuales de 20 a 15, ¿qué máquinas virtuales se quitarán?**

R: Se quitarán las últimas 5 (índices más grandes).

**P. ¿Y si después aumento la capacidad de 15 a 18?**

Si la aumenta a 18, se crearán máquinas virtuales con los índices 15, 16 y 17. En ambos casos, las máquinas virtuales se equilibran entre dominios de error y de actualización.

**P. Si uso varias extensiones en un conjunto de escalado de máquinas virtuales, ¿se puede aplicar una secuencia de ejecución?**

R: No directamente pero, para la extensión customScript, su script podría esperar a que se completara otra extensión (por ejemplo, supervisando el registro de extensiones; consulte [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**P. ¿Funcionan los conjuntos de escalado de máquinas virtuales con los conjuntos de disponibilidad de Azure?**

R: Sí. Un conjunto de escalado es de forma implícita un conjunto de disponibilidad con 3 dominios de error y 5 dominios de actualización. No es necesario configurar nada en virtualMachineProfile. En futuras versiones, es probable que los conjuntos de escalado abarquen varios inquilinos pero, por ahora, un conjunto de escalado es un único conjunto de disponibilidad.

## Pasos siguientes

Durante la vista previa para conjuntos de escalado de máquinas virtuales, se estará desarrollando la documentación y se comunicarán más características de integración como el portal y el escalado automático.

Sus comentarios son muy importantes para ayudarnos a crear un servicio que proporcione las características que necesita. Háganos saber qué funciona y qué no, además de qué se podría mejorar. Puede enviar sus comentarios a [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Recursos

| **Tema.** | **Vínculo** |
| --- | --- |
| Información general del Administrador de recursos de Azure | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Plantillas del Administrador de recursos de Azure | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Funciones de la plantilla del Administrador de recursos de Azure | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Plantillas de ejemplo (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Guía de API de REST de conjuntos de escalado de máquinas virtuales | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Vídeos sobre los conjuntos de escalado de máquinas virtuales | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Ajustes de escalado automático (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Solución de problemas de implementaciones de grupo de recursos en Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->