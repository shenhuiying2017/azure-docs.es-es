<properties
	pageTitle="Spark en la plantilla del Administrador de recursos de Ubuntu"
	description="Aprenda a implementar fácilmente un nuevo clúster de Spark en máquinas virtuales de Ubuntu con Azure PowerShell o CLI de Azure y una plantilla del Administrador de recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Spark en Ubuntu con una plantilla de Administrador de recursos

Apache Spark es un motor rápido para el procesamiento de datos a gran escala. Spark tiene un motor de ejecución DAG avanzado que admite el flujo de datos cíclico y capacidades de informática en memoria, y pueden acceder a diversos orígenes de datos incluyendo HDFS, Spark, HBase y S3.

Además de ejecutar en los administradores de clústeres de Mesos o YARN, Spark proporciona un modo de implementación independiente simple. Este tutorial le explicará cómo utilizar una plantilla de Administrador de recursos de Azure de ejemplo para implementar un clúster Spark en máquinas virtuales de Ubuntu a través de [Azure PowerShell](../powershell-install-configure.md) o [Azure CLI](../xplat-cli.md).

Esta plantilla implementa un clúster Spark en máquinas virtuales de Ubuntu. Esta plantilla proporciona también una cuenta de almacenamiento, red virtual, conjuntos de disponibilidad, las direcciones IP públicas y las interfaces de red requeridas por la instalación. El clúster Spark se crea detrás de una subred, de modo que no hay acceso de IP pública al clúster. Como parte de la implementación, puede implementar un “JumpBox” opcional. Este “JumpBox” es una máquina virtual de Ubuntu que también se implementa en la subred, pero que *expone* una dirección IP pública con un puerto SSH abierto al que se puede conectar. A continuación, desde el “JumpBox”, puede conectarse mediante SSH a todas las máquinas virtuales de Spark de la subred.

Esta plantilla usa el concepto de “tamaño de camiseta” para especificar una configuración del clúster de Spark de “pequeña”, “mediana” o “grande”. Cuando el idioma de la plantilla admite un tamaño de plantilla más dinámico, este puede cambiarse para especificar el número de nodos maestros del clúster de Spark, los nodos subordinados, el tamaño de máquina virtual, etc. Por el momento, puede ver el tamaño de la máquina virtual y el número de nodos maestros y subordinados definidos en el archivo azuredeploy.json en las variables **tshirtSizeS**, **tshirtSizeM** y **tshirtSizeL**.

- S: 1 maestro, 1 subordinado
- M: 1 maestro, 4 subordinados
- L: 1 maestro, 6 subordinados

Los clústeres recién implementados basados en esta plantilla tendrán la topología descrita en el siguiente diagrama, aunque se pueden lograr fácilmente otras topologías personalizando la plantilla presentada en este artículo:

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Como se muestra en la imagen anterior, la topología de implementación consta de los siguientes elementos:

-	Una nueva cuenta de almacenamiento que hospeda el disco del sistema operativo de las máquinas virtuales recién creadas.
-	Una red virtual con una rubred. Todas las máquinas virtuales creadas por la plantilla se aprovisionan dentro de esta red virtual.
-	Un conjunto de disponibilidad para los nodos maestros y secundarios.
-	Un nodo maestro en el conjunto de disponibilidad recién creado.
-	Cuatro nodos subordinados que se ejecutan en la misma subred virtual y en el conjunto de disponibilidad como nodo maestro.
-	Una máquina virtual JumpBox que se encuentra en la misma red virtual y la subred que puede utilizarse para acceder al clúster.

Spark versión 3.0.0 es la versión predeterminada y puede cambiarse a cualquier binario precompilado disponible en el repositorio de Spark. También hay una disposición en el script para quitar el comentario de la compilación en el código fuente. Se asignará una dirección IP estática a cada nodo maestro Spark: 10.0.0.10. Se asignará una dirección IP estática a cada nodo de esclavo Spark para solucionar la limitación actual de no poder crear dinámicamente una lista de direcciones IP desde dentro de la plantilla. (De forma predeterminada, al primer nodo se le asignará la dirección IP privada de 10.0.0.30, al segundo nodo se le asignará 10.0.0.31, y así sucesivamente). Para comprobar los errores de implementación, vaya al nuevo Portal de Azure y busque en **Grupo de recursos** > **Última implementación** > **Detalles de la operación de comprobación**.

Antes de entrar en más detalles relacionados con el Administrador de recursos de Azure y la plantilla que usaremos para esta implementación, asegúrese de que tiene Azure PowerShell o CLI de Azure configurados correctamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creación de un clúster Spark con una plantilla del Administrador de recursos

Siga estos pasos para crear un clúster de Spark, mediante una plantilla del Administrador de recursos, desde el repositorio de plantillas de GitHub usando Azure PowerShell o la CLI de Azure.

### Paso 1-a: Descargar los archivos de plantilla mediante Azure PowerShell

Cree una carpeta local para la plantilla JSON y otros archivos asociados (por ejemplo, C:\\Azure\\Templates\\Spark).

Sustituya el nombre de carpeta de la carpeta local y ejecute estos comandos:

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Paso 1-b: Descargar los archivos de plantilla con CLI de Azure

Clone todo el repositorio de plantillas mediante un cliente Git de su elección, por ejemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Cuando haya finalizado la clonación, busque la carpeta **spark-on-ubuntu** en el directorio C:\\Azure\\Templates.

### Paso 2 (opcional): Comprender los parámetros de plantilla

Al crear un clúster de Spark con una plantilla, debe especificar un conjunto de parámetros de configuración para resolver algunos valores necesarios. Al declarar estos parámetros en la definición de la plantilla, es posible especificar valores durante la ejecución de la implementación mediante un archivo externo o en la línea de comandos.

En la sección “parameters” en la parte superior del archivo azuredeploy.json, encontrará el conjunto de parámetros que requiere la plantilla para configurar un clúster de Spark. Este es un ejemplo de la sección “parameters” de este archivo de plantilla azuredeploy.json:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

Cada parámetro incluye detalles como el tipo de datos y los valores permitidos. Esto permite la validación de parámetros pasados durante la ejecución de la plantilla en un modo interactivo (por ejemplo, Azure PowerShell o CLI de Azure), así como una interfaz de usuario de detección automática que se podría compilar dinámicamente mediante el análisis de la lista de parámetros necesarios y sus descripciones.

### Paso 3-a: Implementar un clúster de Spark con una plantilla mediante Azure PowerShell

Prepare un archivo de parámetros para la implementación creando un archivo JSON que contenga los valores de tiempo de ejecución para todos los parámetros. Este archivo se pasará como una sola entidad al comando de implementación. Si no incluye un archivo de parámetros, Azure PowerShell usará los valores predeterminados especificados en la plantilla y le pedirá que rellene los valores restantes.

A continuación se muestra un conjunto de parámetros de ejemplo del archivo azuredeploy-parameters.json. Tenga en cuenta que deberá proporcionar valores válidos para los parámetros **storageAccountName**, **adminUsername** y **adminPassword**, además de las personalizaciones realizadas en los demás parámetros:

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure y la carpeta del archivo de implementación JSON guardado. A continuación, ejecute estos comandos:

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** debe ser único dentro de su suscripción.

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se extraerán los valores de parámetros desde el archivo de parámetros JSON y se empezará a ejecutar la plantilla según corresponda. Al definir y usar varios archivos de parámetros con sus distintos entornos (por ejemplo, prueba, producción, etc.), se promoverá la reutilización de plantillas y se simplificarán las soluciones complejas de entornos múltiples.

Durante la implementación, recuerde que debe crearse una nueva cuenta de almacenamiento de Azure para que el nombre proporcionado como parámetro de cuenta de almacenamiento sea único y cumpla todos los requisitos de una cuenta de almacenamiento de Azure (solo letras minúsculas y números).

Durante la implementación, verá algo parecido a esto:

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Durante la implementación y después de ella, puede comprobar todas las solicitudes realizadas durante el aprovisionamiento, incluidos todos los errores producidos.

Para ello, vaya al [Portal de Azure](https://portal.azure.com) y haga lo siguiente:

- Haga clic en **Examinar** en la barra de navegación de la izquierda y, a continuación, desplácese hacia abajo y haga clic en **Grupos de recursos**.
- Haga clic en el grupo de recursos que acaba de crear para abrir la hoja “Grupo de recursos”.
- Al hacer clic en el gráfico de barras **Eventos** en la parte **Supervisión** de la hoja “Grupo de recursos”, puede ver los eventos para la implementación.
- Al hacer clic en eventos individuales, puede profundizar más en los detalles de cada operación individual que se realiza en nombre de la plantilla

![portal-events](media/virtual-machines-spark-template/portal-events.png)

Después de las pruebas, si desea quitar este grupo de recursos y todos sus recursos (cuenta de almacenamiento, máquina virtual y red virtual), use este único comando:

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Paso 3-b: Implementar un clúster de Spark con una plantilla mediante CLI de Azure

Para implementar un clúster de Spark a través de CLI de Azure, primero debe crear un grupo de recursos especificando un nombre y una ubicación:

	azure group create SparkResourceGroup "West US"

Pase este nombre del grupo de recursos, la ubicación del archivo de plantilla JSON y la ubicación del archivo de parámetros (consulte la sección anterior de Azure PowerShell para obtener detalles) en el siguiente comando:

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Puede comprobar el estado de las implementaciones de recursos individuales con el comando siguiente:

	azure group deployment list SparkResourceGroup

## Recorrido por la estructura de plantilla y la organización de archivos de Spark

Si desea diseñar una plantilla del Administrador de recursos sólida y reutilizable, hay que pensar aún más en cómo organizar la serie de tareas complejas e interrelacionadas necesarias durante la implementación de una solución compleja como Spark. Al aprovechar la vinculación de plantillas de Administración de recursos, los bucles de recursos y la ejecución de scripts mediante extensiones relacionadas, es posible implementar un enfoque modular que se puede reutilizar con prácticamente cualquier implementación compleja basada en plantillas.

En este diagrama se describen las relaciones entre todos los archivos descargados de GitHub para esta implementación:

![spark-files](media/virtual-machines-spark-template/spark-files.png)

Esta sección le guía a través de la estructura del archivo azuredeploy.json para el clúster de Spark.

Si no ha descargado todavía una copia del archivo de plantilla, designe una carpeta local como ubicación del archivo y créela (por ejemplo, C:\\Azure\\Templates\\Spark). Rellene el nombre de la carpeta y ejecute estos comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Abra la plantilla azuredeploy.json en un editor de texto o una herramienta de su elección. En la siguiente información se describe la estructura del archivo de plantilla y el propósito de cada sección. Como alternativa, puede ver el contenido de esta plantilla en el explorador desde [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json).

### Sección “parameters”

La sección “parameters” de azuredeploy.json especifica parámetros modificables que se usan en esta plantilla. El archivo azuredeploy-parameters.json mencionado anteriormente se usa para pasar valores a la sección “parameters” de azuredeploy.json durante la ejecución de la plantilla.

Este es un ejemplo de un parámetro para el “tamaño de camiseta”:

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]Observe que se puede especificar un **defaultValue**, así como **allowedValues**.

### Sección "variables"

La sección “variables” especifica las variables que se pueden usar en esta plantilla. Contiene cierto número de campos (tipos de datos o fragmentos JSON) que se establecerán en constantes o valores calculados en tiempo de ejecución. Estos son algunos ejemplos, del más sencillo al más complejo:

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName** es un ejemplo de un nombre/variable de nombre sencillo. **vnetID** es un ejemplo de una variable que se calcula en tiempo de ejecución mediante las funciones **resourceId** y **parameters**. El valor de las variables **numberOfMasterInstances** y **vmSize** se calculan en tiempo de ejecución mediante las funciones **concat**, **variables** y **parameters**.

Si desea personalizar el tamaño de la implementación del clúster de Spark, puede cambiar las propiedades de las variables **tshirtSizeS**, **tshirtSizeM** y **tshirtSizeL** en la plantilla azuredeploy.json.

Encontrará más información sobre el idioma de la plantilla en MSDN en [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).


### Sección "resources"

La sección “resources” es donde ocurre la mayor parte de la acción. Analizando detenidamente esta sección, puede identificar inmediatamente dos casos diferentes. El primero es un elemento definido de tipo `Microsoft.Resources/deployments` que básicamente invoca una implementación anidada dentro de la principal. El segundo es la propiedad **templateLink** (y la propiedad **contentVersion** relacionada) que hace posible especificar un archivo de plantilla vinculada que se invocará, pasando un conjunto de parámetros como entrada. Pueden verse en este fragmento de plantilla:

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

A partir de este primer ejemplo está claro que azuredeploy.json se organizó en este escenario como un mecanismo de coordinación, al invocar cierto número de otros archivos de plantillas. Cada archivo es responsable de una parte de las actividades de implementación necesarias.

En particular, las siguientes plantillas vinculadas se usarán para esta implementación:

-	**shared-resource.json**: contiene la definición de todos los recursos que se van a compartir en toda la implementación. Algunos ejemplos son las cuentas de almacenamiento que se usan para almacenar discos del sistema operativo de la máquina virtual y redes virtuales.
-	**jumpbox-resources-enabled.json**: implementa la máquina virtual de “JumpBox” y todos los recursos relacionados, como la interfaz de red, la dirección IP pública y el extremo de entrada usado para conectarse mediante SSH al entorno.

Después de invocar estas dos plantillas, azuredeploy.json aprovisiona todas las máquinas virtuales del nodo de clúster de Spark y los recursos conectados (adaptadores de red, IP privada, etc.). Esta plantilla también implementará extensiones de máquinas virtuales (scripts personalizados para Linux) e invocará un script Bash (spark-cluster-install.sh) para instalar físicamente y configurar Spark en cada nodo.

Analicemos *cómo* se usa esta última plantilla, azuredeploy.json, ya que es una de las más interesantes desde el punto de vista del desarrollo de una plantilla. Un concepto importante que se debe resaltar es cómo puede un solo archivo de plantilla implementar varias copias de un tipo de recurso único y puede establecer para cada instancia valores únicos para la configuración requerida. Este concepto se conoce como **bucle de recursos**.

Un recurso que usa el elemento **copy** "se copiará" a sí mismo el número de veces especificado en el parámetro **count** del elemento **copy**. Para todas las configuraciones donde sea necesario especificar valores únicos entre diferentes instancias del recurso implementado, se puede usar la función **copyindex()** para obtener un valor numérico que indica el índice actual en esa creación de bucle de recurso en particular. En el fragmento siguiente de azuredeploy.json se puede ver este concepto aplicado a la creación de varios adaptadores de red, máquinas virtuales y extensiones de máquinas virtuales para el clúster de Spark:

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Otro concepto importante en la creación de recursos es la capacidad de especificar las dependencias y prioridades entre los recursos, tal como se puede observar en la matriz JSON **dependsOn**. En esta plantilla en particular, puede ver que los nodos del clúster de Spark dependen de los recursos compartidos y de los recursos de **networkInterfaces** que se crean en primer lugar.

Otro fragmento interesante es el relacionado con las extensiones de máquinas virtuales de **CustomScriptForLinux**. Se instalan como un tipo de recurso independiente, con una dependencia en cada nodo del clúster. En este caso, esto se usa para instalar y configurar Spark en cada nodo de la máquina virtual. Echemos un vistazo a un fragmento de código de la plantilla azuredeploy.json que las usa:

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

Observe que la extensión de los recursos del nodo maestro y secundario ejecuta los comandos diferentes, definidos en la propiedad **commandToExecute**, como parte del proceso de aprovisionamiento.

Si observa el fragmento JSON de la última extensión de máquina virtual, puede ver que este recurso depende del recurso de máquina virtual y su interfaz de red. Esto indica que estos dos recursos ya deben estar implementados antes de aprovisionar y ejecutar esta extensión de máquina virtual. Tenga en cuenta también el uso de la función **copyindex()** para repetir este paso para cada máquina virtual esclava.

Al familiarizarse con los demás archivos incluidos en esta implementación, podrá entender todos los detalles y los procedimientos recomendados necesarios para organizar y coordinar las complejas estrategias de implementación de las soluciones de múltiples nodos, basadas en cualquier tecnología, aprovechando las plantillas del Administrador de recursos de Azure. Aunque no es obligatorio, un enfoque recomendado consiste en estructurar los archivos de plantillas, tal como se muestra en el diagrama siguiente:

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

Básicamente, se sugiere este enfoque para:

-	Definir el archivo de plantilla principal como punto central de coordinación para todas las actividades de implementación específicas y aprovechar la vinculación de las plantillas para invocar ejecuciones de subplantillas.
-	Crear archivos de plantillas específicas que implementarán todos los recursos compartidos en todas las demás tareas de implementación específicas (cuentas de almacenamiento, configuración de red virtual, etc.). Esto se puede reutilizar en gran medida entre las implementaciones que tienen requisitos similares en cuanto a una infraestructura común.
-	Incluir plantillas de recursos opcionales para identificar requisitos específicos de un recurso determinado.
-	Para los miembros idénticos de un grupo de recursos (nodos de un clúster, etc.), crear plantillas específicas que aprovechen bucles de recursos a fin de implementar varias instancias con propiedades únicas.
-	Para todas las tareas de implementación posteriores (instalación del producto, configuraciones, etc.), aprovechar las extensiones de implementación de scripts y crear scripts específicos de cada tecnología.

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

## Pasos siguientes

Obtenga más información sobre cómo [implementar una plantilla](../resource-group-template-deploy.md).

Descubra más [marcos de aplicaciones](virtual-machines-app-frameworks.md).

[Solucionar problemas de las implementaciones de plantillas](resource-group-deploy-debug.md).

<!---HONumber=August15_HO9-->