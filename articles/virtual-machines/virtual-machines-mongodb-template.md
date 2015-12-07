<properties
  pageTitle="Creación de un clúster de MongoDB en Ubuntu | Microsoft Azure"
  description="Crear un clúster de MongoDB en Ubuntu mediante una plantilla de Administrador de recursos de Azure a través de Azure PowerShell o CLI de Azure"
  services="virtual-machines"
  documentationCenter=""
  authors="scoriani"
  manager="timlt"
  editor="tysonn"
  tags="azure-resource-manager"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="scoriani"/>

# Crear un clúster de MongoDB en Ubuntu mediante una plantilla del Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.


MongoDB es una base de datos de documentos de código abierto que ofrece alto rendimiento, alta disponibilidad y escalado automático. MongoDB puede instalarse como una base de datos independiente o en un clúster, aprovechando las capacidades integradas de réplica. En algunos casos, puede usar la replicación para aumentar la capacidad de lectura. Los clientes tienen la capacidad de enviar operaciones de lectura y escritura a diferentes servidores. También es posible mantener copias en diferentes centros de datos para aumentar la localidad y la disponibilidad de los datos de las aplicaciones distribuidas. Con MongoDB, la replicación también proporciona redundancia y aumenta la disponibilidad de los datos. Con varias copias de los datos en servidores de bases de datos diferentes, la replicación protege una base de datos de la pérdida de un único servidor. La replicación también permite la recuperación tras errores de hardware e interrupciones del servicio. Con las copias adicionales de los datos, puede dedicar una recuperación ante desastres, creación de informes o copia de seguridad.

Ahora, además de los diversos modelos que ya estaban disponibles en Azure Marketplace, puede implementar fácilmente un nuevo clúster de MongoDB en VM de Ubuntu con una plantilla del Administrador de recursos de Azure mediante [Azure PowerShell](../powershell-install-configure.md) o [CLI de Azure](../xplat-cli-install.md).

Los clústeres recién implementados basados en esta plantilla tendrán la topología descrita en el siguiente diagrama, aunque se pueden lograr fácilmente otras topologías personalizando la plantilla presentada en este artículo.

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

A través de un parámetro, puede definir el número de nodos que se implementarán en el nuevo clúster de MongoDB y, mediante otro parámetro, también puede implementar dentro de la misma red virtual una instancia de máquina virtual (JumpBox) con una dirección IP pública, lo que proporciona la capacidad de conectarse al clúster desde Internet pública y realizar todo tipo de tareas administrativas relacionadas con ese clúster. Otra opción disponible como parámetro es la capacidad de agregar un nodo árbitro al conjunto de réplicas, que suele sugerirse cuando tiene un número par de miembros. Para obtener más información sobre las topologías de replicación de MongoDB y otros detalles, consulte el sitio oficial de [Documentación de MongoDB](http://docs.mongodb.org/manual/core/replication-introduction/).

Una vez completada la implementación, puede tener acceso a JumpBox con la dirección DNS configurada en el puerto SSH 22.

Antes de entrar en más detalles relacionados con el Administrador de recursos de Azure y la plantilla que usaremos para esta implementación, asegúrese de que tiene Azure PowerShell o CLI de Azure configurados correctamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Crear un clúster de MongoDB con una plantilla del Administrador de recursos

Siga estos pasos para crear un clúster de MongoDB mediante una plantilla del Administrador de recursos desde el repositorio de plantillas de GitHub. Cada paso incluye instrucciones para Azure PowerShell y CLI de Azure.

### Paso 1-a: Descargar los archivos de plantilla con PowerShell

Cree una carpeta local para la plantilla JSON y otros archivos asociados (por ejemplo, C:\\Azure\\Templates\\MongoDB).

En el siguiente ejemplo, sustituya el nombre de carpeta de la carpeta local y ejecute los comandos:

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Paso 1-b: Descargar los archivos de plantilla con CLI de Azure

El siguiente ejemplo muestra como clonar todo el repositorio de plantillas mediante un cliente GIT de su elección:

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Cuando termine, busque la carpeta mongodb-high-availability en el directorio C:\\Azure\\Templates.

### Paso 2 (opcional): Comprender los parámetros de plantilla

Al implementar soluciones no triviales, como un clúster de MongoDB, debe especificar un conjunto de parámetros de configuración para resolver algunos valores necesarios. Al declarar estos parámetros en la definición de la plantilla, es posible especificar valores durante la ejecución de la implementación mediante un archivo externo o en la línea de comandos.

En la sección parameters en la parte superior del archivo azuredeploy.json, encontrará el conjunto de parámetros que requiere la plantilla para configurar un clúster de MongoDB. En el siguiente ejemplo se muestra la sección parameters de este archivo de plantilla azuredeploy.json:

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the storage account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
          }
        },
        "nodeAddressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.1",
          "metadata": {
            "Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
          }
        },
        "jumpbox": {
          "type": "string",
          "defaultValue": "Disabled",
          "allowedValues": [
          "Enabled",
          "Disabled"
          ],
          "metadata": {
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

Cada parámetro incluye detalles como el tipo de datos y los valores permitidos. Esto permite la validación de parámetros pasados durante la ejecución de la plantilla en un modo interactivo (por ejemplo, Azure PowerShell o CLI de Azure), así como una interfaz de usuario de detección automática que se podría compilar dinámicamente mediante el análisis de la lista de parámetros necesarios y sus descripciones.

### Paso 3-a: Implementar un clúster de MongoDB con una plantilla mediante PowerShell

Prepare un archivo de parámetros para la implementación creando un archivo JSON que contenga los valores de tiempo de ejecución para todos los parámetros. Este archivo se pasará como una sola entidad al comando de implementación. Si no incluye un archivo de parámetros, PowerShell usará los valores predeterminados especificados en la plantilla y le pedirá que rellene los valores restantes.

A continuación se muestra un conjunto de parámetros de ejemplo del archivo azuredeploy-parameters.json:

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
      },
      "addressPrefix": {
          "value": "10.0.0.0/16"
      },
      "subnetPrefix": {
          "value": "10.0.0.0/24"
      },
      "nodeAddressPrefix": {
          "value": "10.0.0.1"
      },
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure y la carpeta del archivo de implementación JSON guardado. Luego, ejecute los siguientes comandos:

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se extraerán los valores de parámetros desde el archivo de parámetros JSON y se empezará a ejecutar la plantilla según corresponda. Al definir y usar varios archivos de parámetros con sus distintos entornos (por ejemplo, prueba, producción, etc.), se promueve la reutilización de plantillas y se simplifican las soluciones complejas de entornos múltiples.

Durante la implementación, recuerde que debe crearse una nueva cuenta de almacenamiento de Azure para que el nombre proporcionado como parámetro de cuenta de almacenamiento sea único y cumpla todos los requisitos de una cuenta de almacenamiento de Azure (solo letras minúsculas y números).

Durante la implementación y después de ella, puede comprobar todas las solicitudes realizadas durante el aprovisionamiento, incluidos todos los errores producidos.

Para ello, vaya al [Portal de vista previa de Azure](https://portal.azure.com) y haga lo siguiente:

- Haga clic en **Examinar** en la barra de navegación de la izquierda, desplácese hacia abajo y haga clic en **Grupos de recursos**.
- Después de hacer clic en el grupo de recursos que acaba de crear, aparecerá la hoja Grupo de recursos.
- Al hacer clic en el gráfico de barras "Eventos" en la parte "Supervisión" de la hoja Grupo de recursos, puede ver los eventos para la implementación. Al hacer clic en un evento individual, puede profundizar más en los detalles de cada operación individual que se realiza en nombre de la plantilla.

Después de las pruebas, si desea quitar este grupo de recursos y todos sus recursos (cuenta de almacenamiento, máquina virtual y red virtual), use el siguiente comando.

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Paso 3-b: Implementar un clúster de MongoDB con una plantilla mediante CLI de Azure

Para implementar un clúster de MongoDB a través de CLI de Azure, primero debe crear un grupo de recursos especificando un nombre y una ubicación con el siguiente comando:

    azure group create mdbc "West US"

Pase este nombre del grupo de recursos, la ubicación del archivo de plantilla JSON y la ubicación del archivo de parámetros (consulte la sección anterior de PowerShell para obtener detalles) en el siguiente comando:

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Puede comprobar el estado de las implementaciones de recurso individuales con el comando siguiente.

    azure group deployment list mdbc

## Recorrido por la estructura de plantilla y la organización de archivos de MongoDB

Si desea diseñar una plantilla de Administrador de recursos de Azure que sea sólida y reutilizable, tiene que pensar aún más en cómo organizar la serie de tareas complejas e interrelacionadas necesarias durante la implementación de una solución compleja como MongoDB. Al aprovechar la *vinculación de plantillas* y los *bucles de recursos* de Administrador de recursos de Azure, además de la ejecución de scripts mediante extensiones relacionadas, es posible implementar un enfoque modular que se puede reusar con prácticamente cualquier implementación compleja basada en plantillas.

En el siguiente diagrama se describen las relaciones entre todos los archivos descargados de GitHub para esta implementación:

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

Esta sección le guía a través de la estructura del archivo azuredeploy.json para el clúster de MongoDB.

### Sección parameters

La sección parameters de azuredeploy.json especifica parámetros modificables que se usan en esta plantilla. El archivo azuredeploy-parameters.json descrito anteriormente en este artículo se usa para pasar valores a la sección parameters de azuredeploy.json durante la ejecución de la plantilla.

### Sección variables

La sección variables especifica las variables que se pueden usar en esta plantilla. Contiene cierto número de campos (tipos de datos o fragmentos JSON) que se establecerán en constantes o valores calculados en tiempo de ejecución. En el ejemplo siguiente se muestra la sección variables para esta plantilla MongoDB.

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

En el ejemplo anterior, se pueden ver dos enfoques diferentes. En este primer fragmento, la variable “osFamilyUbuntu” se establecerá en un elemento JSON que contiene 6 pares clave-valor:

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

En este segundo fragmento, la variable “vmScripts” se asigna a una matriz JSON donde se calcularán elementos únicos en tiempo de ejecución mediante una función de lenguaje de plantilla (concat) y el valor de otra variable, además de constantes de cadenas.

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Un concepto importante en esta plantilla es la forma diferente en que se definen los “tamaños de camiseta” de los clústeres de MongoDB. Si examina una de estas variables “tshirtSizeXXXX”, observará que describe características importantes relacionadas con la implementación de un clúster. En el siguiente ejemplo, echemos un vistazo al tamaño mediano.

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Un clúster de MongoDB “Medium” usará D2 como tamaño de máquina virtual para los tres nodos de MongoDB que hospedan datos, además de una cuarta máquina virtual A1 que se usará como árbitro para fines de replicación. La subplantilla correspondiente que se invoca para implementar nodos de datos será `member-resources-D2.json` y los archivos de datos (250 GB cada uno) se almacenarán en dos cuentas de almacenamiento. Estas variables se usarán en la sección resources para organizar las implementaciones de nodo y otras tareas.

### Sección resources

La sección resources es donde ocurre la mayor parte de la acción. Al analizar detenidamente esta sección, puede identificar de inmediato dos casos distintos: el primero es un elemento definido de tipo `Microsoft.Resources/deployments` que básicamente significa la invocación de una implementación anidada dentro de la principal. Mediante el elemento “templateLink” (y la propiedad de versión relacionada), es posible especificar un archivo de plantilla vinculado que se invocará pasando un conjunto de parámetros como entrada, tal como se muestra en el siguiente ejemplo.

    {
      "name": "shared-resources",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('sharedTemplateUrl')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": "[variables('storageSettings')]"
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          }
        }
      }
    },

A partir del ejemplo anterior está claro que azuredeploy.json se organizó en este escenario como un mecanismo de coordinación, al invocar cierto número de otros archivos de plantillas, cada uno de los cuales es responsable de una parte de las actividades de implementación necesarias.

En particular, las siguientes plantillas vinculadas se usarán para esta implementación:

-	**shared-resource.json**: contiene la definición de todos los recursos que se van a compartir en toda la implementación. Algunos ejemplos son las cuentas de almacenamiento que se usan para almacenar discos del sistema operativo de la máquina virtual y redes virtuales.
-	**Jumpbox-resources.json**: cuando se habilita, es responsable de la implementación de todos los recursos relacionados con la máquina virtual de JumpBox, tiene una dirección IP pública que puede usarse para acceder al clúster de MongoDB desde la red pública.
-	**Arbiter-resources.json**: cuando se habilita, esta plantilla implementa un miembro de árbitro en el clúster de MongoDB. Un árbitro no contiene datos, pero se usa cuando un conjunto de réplicas contiene un número par de nodos para administrar las elecciones principales.
-	**Member-resources-Dx.json**: especifica las plantillas de recursos que implementan eficazmente los nodos de MongoDB. Se usará un archivo específico según la definición del “tamaño de camiseta” seleccionado, donde cada archivo solo diferirá en el número de discos conectados para cada nodo.
-	**Mongodb-ubuntu-install.sh**: un archivo de script Bash invocado por la extensión CustomScriptForLinux en todos los nodos del clúster. Es responsable de montar y dar formato a los discos de datos y de instalar los bits de MongoDB en el nodo.

Para implementar un clúster de MongoDB, es necesaria una lógica específica para poder configurar correctamente un conjunto de réplicas. El siguiente ejemplo muestra la secuencia específica que se debe usar durante la implementación.

IMPLEMENTAR MIEMBROS DE DATOS (en paralelo) => IMPLEMENTAR ÚLTIMO MIEMBRO DE DATOS => (opcional) IMPLEMENTAR ÁRBITRO

En esta secuencia, la implementación de varios nodos de datos se realizará en paralelo, excepto el último nodo. Aquí es donde se formará el clúster y se implementará el nuevo conjunto de réplicas, por lo que todos los nodos anteriores deberán estar en funcionamiento antes de ese momento. El último paso es implementar el nodo de árbitro opcional (solo para los “tamaños de camiseta” en los que sea necesario).

Volvamos a nuestra plantilla principal (azuredeploy.json) para ver cómo se implementa esta lógica, empezando por todos los miembros de datos en el siguiente ejemplo.

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('clusterSpec').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Un concepto importante que se debe resaltar es cómo se pueden implementar varias copias de un tipo de recurso único y establecer para cada instancia valores únicos para la configuración requerida. Este concepto se conoce como *bucle de recursos*.

En el ejemplo anterior, se usará un parámetro (número de nodos que se deben implementar en el clúster) para establecer una variable (“numberOfMembers”) que se pasa al elemento **“copy”** para desencadenar un número (bucle) de implementaciones secundarias, cada una de las cuales producirá la creación de instancias de la plantilla para cada miembro del clúster. Para poder establecer todas las configuraciones donde sean necesarios valores únicos entre instancias, se puede usar la función **copyindex()** para obtener un valor numérico que indica el índice actual en esa creación de bucle de recurso en particular.

Otro concepto importante en la creación de recursos es la capacidad de especificar las dependencias y prioridades entre los recursos, tal como se puede observar en la matriz JSON **dependsOn**. En esta plantilla en particular, la implementación de cada nodo depende de la correcta implementación anterior de los **recursos compartidos**.

Se da formato a los discos conectados como parte de las actividades de preparación del nodo que desencadena la ejecución del archivo de script mongodb-ubuntu-install.sh. Dentro de ese archivo, encontrará una instancia de esta llamada:

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

vm-disk-utils-0.1.sh forma parte de la carpeta shared\_scripts\\ubuntu, en el repositorio de GitHub azure-quickstart-templates y contiene funciones muy útiles para el montaje, formato y creación de bandas en discos que puede volver a usar cada vez que necesite ejecutar tareas similares como parte de la creación de plantillas.

Otro fragmento interesante es el relacionado con las extensiones de máquinas virtuales de CustomScriptForLinux. Estas se instalan como un tipo de recurso independiente, con una dependencia en cada plantilla de implementación del nodo del clúster. Por ejemplo, vea el fragmento siguiente al final de cada archivo de recursos de miembro de Dx.json.

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

Al familiarizarse con los demás archivos incluidos en esta implementación, podrá entender todos los detalles y los procedimientos recomendados necesarios para organizar y coordinar las complejas estrategias de implementación de las soluciones de múltiples nodos, basadas en cualquier tecnología, aprovechando las plantillas del Administrador de recursos de Azure. Aunque no es obligatorio, un enfoque recomendado consiste en estructurar los archivos de plantillas, tal como se muestra en el diagrama siguiente.

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

Básicamente, este enfoque sugiere:

-	Definir el archivo de plantilla principal como punto central de coordinación para todas las actividades de implementación específicas y aprovechar la vinculación de las plantillas para invocar ejecuciones de subplantillas.
-	Crear archivos de plantillas específicas que implementarán todos los recursos compartidos en todas las demás tareas de implementación específicas (por ejemplo, cuentas de almacenamiento, configuración de red virtual, etc). Esto se puede reutilizar en gran medida entre las implementaciones que tienen requisitos similares en cuanto a una infraestructura común.
-	Incluir plantillas de recursos opcionales para identificar requisitos específicos de un recurso determinado.
-	Para los miembros idénticos de un grupo de recursos (nodos de un clúster, etc.), crear plantillas específicas que aprovechen bucles de recursos a fin de implementar varias instancias con propiedades únicas.
-	Para todas las tareas de implementación posteriores (por ejemplo, instalación del producto, configuraciones, etc.), aprovechar las extensiones de implementación de scripts y crear scripts específicos de cada tecnología.

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->