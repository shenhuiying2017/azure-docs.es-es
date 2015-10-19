<properties
	pageTitle="DataStax en Ubuntu con una plantilla del Administrador de recursos | Microsoft Azure"
	description="Aprenda a implementar fácilmente un nuevo clúster de DataStax en máquinas virtuales de Ubuntu con Azure PowerShell o la CLI de Azure y una plantilla del Administrador de recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# DataStax en Ubuntu con una plantilla del Administrador de recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación del Administrador de recursos.

DataStax es un reconocido líder del sector en el desarrollo y la entrega de soluciones basadas en Apache Cassandra, la tecnología de base de datos distribuida NoSQL preparada para la empresa y admitida comercialmente, bien conocida por ser ágil, siempre activada y escalable de forma predecible a cualquier tamaño. DataStax ofrece las versiones Enterprise (DSE) y Community (DSC). También proporciona capacidades como informática en memoria, seguridad a nivel empresarial, análisis integrados rápidos y potentes, y búsqueda empresarial.

Ahora, además de lo que ya está disponible en Azure Marketplace, puede implementar fácilmente un nuevo clúster de DataStax en VM de Ubuntu con una plantilla del Administrador de recursos mediante [Azure PowerShell](../powershell-install-configure.md) o la [CLI de Azure](../xplat-cli-install.md).

Los clústeres recién implementados basados en esta plantilla tendrán la topología descrita en el siguiente diagrama, aunque se pueden lograr fácilmente otras topologías personalizando la plantilla presentada en este artículo:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Con el uso de parámetros, puede definir el número de nodos que se implementarán en el nuevo clúster de Apache Cassandra. También se implementará una instancia del servicio del centro de operaciones de DataStax en una máquina virtual independiente dentro de la misma red virtual, lo que proporciona la capacidad de supervisar el estado del clúster y todos los nodos individuales, agregar o quitar nodos y llevar a cabo todas las tareas administrativas relacionadas con ese clúster.

Una vez completada la implementación, puede tener acceso a la instancia de máquina virtual del centro de operaciones de DataStax con la dirección DNS configurada. La máquina virtual de OpsCenter tiene el puerto SSH 22 habilitado, así como el puerto 8443 para HTTPS. La dirección DNS del centro de operaciones incluirá el *dnsName* y la *región* especificados como parámetros, lo que da como resultado el formato `{dnsName}.{region}.cloudapp.azure.com`. Si creó una implementación con el parámetro *dnsName* establecido en "datastax" en la región "Oeste de EE. UU.", podría tener acceso a la máquina virtual del centro de operaciones de DataStax para la implementación en `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]El certificado usado en la implementación es un certificado autofirmado que creará una advertencia de explorador. Puede seguir el proceso en el sitio web de [DataStax](http://www.datastax.com/) para reemplazar el certificado por su propio certificado SSL.

Antes de entrar en más detalles relacionados con el Administrador de recursos de Azure y la plantilla que usaremos para esta implementación, asegúrese de que tiene Azure PowerShell o CLI de Azure configurados correctamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creación de un clúster de Cassandra basado en DataStax con una plantilla del Administrador de recursos

Siga estos pasos para crear un clúster de Apache Cassandra basado en DataStax mediante una plantilla del Administrador de recursos en el repositorio de plantillas de GitHub. Cada paso incluye instrucciones para Azure PowerShell y CLI de Azure.

### Paso 1-a: Descargar los archivos de plantilla mediante Azure PowerShell

Cree una carpeta local para la plantilla JSON y otros archivos asociados (por ejemplo, C:\\Azure\\Templates\\DataStax).

Sustituya el nombre de carpeta de la carpeta local y ejecute estos comandos:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Paso 1-b: Descargar los archivos de plantilla con CLI de Azure

Clone todo el repositorio de plantillas mediante un cliente Git de su elección, por ejemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Cuando haya finalizado la clonación, busque la carpeta **datastax-on-ubuntu** en el directorio C:\\Azure\\Templates.

### Paso 2 (opcional): Comprender los parámetros de plantilla

Al implementar soluciones no triviales, como un clúster de Apache Cassandra basado en DataStax, debe especificar un conjunto de parámetros de configuración para resolver algunos valores necesarios. Al declarar estos parámetros en la definición de la plantilla, es posible especificar valores durante la ejecución de la implementación mediante un archivo externo o en la línea de comandos.

En la sección "parameters" en la parte superior del archivo azuredeploy.json, encontrará el conjunto de parámetros que requiere la plantilla para configurar un clúster de DataStax. Este es un ejemplo de la sección “parameters” de este archivo de plantilla azuredeploy.json:

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
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
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

Cada parámetro incluye detalles como el tipo de datos y los valores permitidos. Esto permite la validación de parámetros pasados durante la ejecución de la plantilla en un modo interactivo (por ejemplo, Azure PowerShell o CLI de Azure), así como una interfaz de usuario de detección automática que se podría compilar dinámicamente mediante el análisis de la lista de parámetros necesarios y sus descripciones.

### Paso 3-a: Implementar un clúster de DataStax con una plantilla mediante Azure PowerShell

Prepare un archivo de parámetros para la implementación creando un archivo JSON que contenga los valores de tiempo de ejecución de todos los parámetros. Este archivo se pasará como una sola entidad al comando de implementación. Si no incluye un archivo de parámetros, Azure PowerShell usará los valores predeterminados especificados en la plantilla y le pedirá que rellene los valores restantes.

A continuación se muestra un conjunto de parámetros de ejemplo del archivo azuredeploy-parameters.json.

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure y la carpeta del archivo de implementación JSON guardado. A continuación, ejecute estos comandos:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se extraerán los valores de parámetros desde el archivo de parámetros JSON y se empezará a ejecutar la plantilla según corresponda. Al definir y usar varios archivos de parámetros con sus distintos entornos (por ejemplo, prueba, producción, etc.), se promoverá la reutilización de plantillas y se simplificarán las soluciones complejas de entornos múltiples.

Durante la implementación, recuerde que debe crearse una nueva cuenta de almacenamiento de Azure para que el nombre proporcionado como parámetro de cuenta de almacenamiento sea único y cumpla todos los requisitos de una cuenta de almacenamiento de Azure (solo letras minúsculas y números).

Durante la implementación y después de ella, puede comprobar todas las solicitudes realizadas durante el aprovisionamiento, incluidos todos los errores producidos.

Para ello, vaya al [Portal de Azure](https://portal.azure.com) y haga lo siguiente:

- Haga clic en **Examinar** en la barra de navegación de la izquierda y luego desplácese hacia abajo y haga clic en **Grupos de recursos**.  
- Haga clic en el grupo de recursos que acaba de crear para abrir la hoja "Grupo de recursos".  
- Al hacer clic en el gráfico de barras "Eventos" en la parte **Supervisión** de la hoja "Grupo de recursos", puede ver los eventos para la implementación.
- Al hacer clic en eventos individuales, puede profundizar más en los detalles de cada operación que se realiza en nombre de la plantilla.

Después de las pruebas, si desea quitar este grupo de recursos y todos sus recursos (cuenta de almacenamiento, máquina virtual y red virtual), use este único comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Paso 3-b: Implementar un clúster de DataStax con una plantilla mediante la CLI de Azure

Para implementar un clúster de DataStax a través de la CLI de Azure, primero debe crear un grupo de recursos especificando un nombre y una ubicación:

	azure group create dsc "West US"

Pase este nombre del grupo de recursos, la ubicación del archivo de plantilla JSON y la ubicación del archivo de parámetros (consulte la sección anterior de Azure PowerShell para obtener detalles) en el siguiente comando:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Puede comprobar el estado de las implementaciones de recursos individuales con el comando siguiente:

	azure group deployment list dsc

## Recorrido por la estructura de plantilla y la organización de archivos de DataStax

Si desea diseñar una plantilla del Administrador de recursos sólida y reutilizable, hay que pensar aún más en cómo organizar la serie de tareas complejas e interrelacionadas necesarias durante la implementación de una solución compleja como DataStax. Al aprovechar la vinculación de plantillas del Administrador de recursos, los bucles de recursos y la ejecución de scripts mediante extensiones relacionadas, es posible implementar un enfoque modular que se puede reutilizar con prácticamente cualquier implementación compleja basada en plantillas.

Esta sección le guía a través de la estructura del archivo azuredeploy.json para el clúster de Datastax.

### Sección “parameters”

La sección “parameters” de azuredeploy.json especifica parámetros modificables que se usan en esta plantilla. El archivo azuredeploy-parameters.json mencionado anteriormente se usa para pasar valores a la sección “parameters” de azuredeploy.json durante la ejecución de la plantilla.

### Sección "variables"

La sección “variables” especifica las variables que se pueden usar en esta plantilla. Contiene cierto número de campos (tipos de datos o fragmentos JSON) que se establecerán en constantes o valores calculados en tiempo de ejecución. Aquí está la sección "variables" de esta plantilla de DataStax:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

Al explorar en profundidad este ejemplo, puede ver dos enfoques diferentes. En este primer fragmento, la variable **osSettings** es un elemento JSON anidado que contiene cuatro pares clave-valor:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
En este segundo fragmento, la variable **scripts** es una matriz JSON donde cada elemento se calculará en tiempo de ejecución mediante una función de lenguaje de plantilla (concat) y el valor de otra variable, además de constantes de cadenas:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### Sección "resources"

La sección “resources” es donde ocurre la mayor parte de la acción. Analizando detenidamente esta sección, puede identificar inmediatamente dos casos diferentes. El primero es un elemento definido de tipo `Microsoft.Resources/deployments` que básicamente implica la invocación de una implementación anidada dentro de la principal. Mediante el elemento **templateLink** (y la propiedad de versión relacionada), es posible especificar un archivo de plantilla vinculado que se invocará pasando un conjunto de parámetros como entrada, tal como se puede observar en este fragmento:

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

A partir de este primer ejemplo está claro que azuredeploy.json se organizó en este escenario como un mecanismo de coordinación, al invocar cierto número de otros archivos de plantillas. Cada archivo es responsable de una parte de las actividades de implementación necesarias.

En particular, las siguientes plantillas vinculadas se usarán para esta implementación:

-	**shared-resource.json**: contiene la definición de todos los recursos que se van a compartir en la implementación. Algunos ejemplos son las cuentas de almacenamiento que se usan para almacenar discos del sistema operativo de la máquina virtual y las redes virtuales.
-	**opscenter-resources.json**: implementa una VM OpsCenter y todos los recursos relacionados, incluida la interfaz de red y la dirección IP pública.
-	**opscenter-install-resources.json**: implementa la extensión de VM de OpsCenter (script personalizado para Linux) que invocará el archivo de script Bash específico (opscenter.sh) necesario para configurar el servicio de OpsCenter dentro de esa VM.
-	**ephemeral-nodes-resources.json**: implementa todas las máquinas virtuales de nodo del clúster y los recursos conectados (tarjetas de red, IP privadas, etc.). Esta plantilla también implementará extensiones de máquinas virtuales (scripts personalizados para Linux) e invocará un script Bash (dsenode.sh) para instalar físicamente los bits de Apache Cassandra en cada nodo.

Analicemos cómo se usa esta última plantilla, ya que es una de las más interesantes desde el punto de vista del desarrollo de una plantilla. Un concepto importante que se debe resaltar es cómo puede una sola plantilla implementar varias copias de un tipo de recurso único y puede establecer para cada instancia valores únicos para la configuración requerida. Este concepto se conoce como **bucle de recursos**.

Cuando se invoca ephemeral-nodes-resources.json desde el archivo principal azuredeploy.json, se proporciona un parámetro llamado *nodeCount* como parte de la lista de parámetros. En la plantilla secundaria, se usará *nodeCount* (el número de nodos que se debe implementar en el clúster) dentro del elemento **copy** de cada recurso que debe implementarse en varias copias, tal como se presenta en el siguiente fragmento. Para todas las configuraciones donde sean necesarios valores únicos para diferentes instancias del recurso implementado, se puede usar la función **copyindex()** para obtener un valor numérico que indica el índice actual en esa creación de bucle de recurso en particular. En el fragmento siguiente se puede ver este concepto aplicado a la creación de varias máquinas virtuales para los nodos del clúster de DataStax:

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Otro concepto importante en la creación de recursos es la capacidad de especificar las dependencias y prioridades entre los recursos, tal como se puede observar en la matriz JSON **dependsOn**. En esta plantilla en particular, cada nodo tendrá también un disco de 1 TB conectado (consulte “dataDisks”) que se puede usar para hospedar copias de seguridad e instantáneas de la instancia de Apache Cassandra.

Se da formato a los discos conectados como parte de las actividades de preparación del nodo que desencadena la ejecución del archivo de script dsenode.sh. La primera fila de ese script invoca otro script:

	bash vm-disk-utils-0.1.sh

El archivo vm-disk-utils-0.1.sh forma parte de la carpeta **shared\_scripts\\ubuntu**, en el repositorio de GitHub azure-quickstart-templates y contiene funciones muy útiles para el montaje, el formato y el desmontaje de discos. Estas funciones se pueden usar en todas las plantillas del repositorio.

Otro fragmento interesante es el relacionado con las extensiones de máquinas virtuales de CustomScriptForLinux. Se instalan como un tipo de recurso independiente, con una dependencia en cada nodo del clúster (y la instancia OpsCenter). Aprovechan el mismo mecanismo de bucle de recursos descrito para las máquinas virtuales:

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

Al familiarizarse con los demás archivos incluidos en esta implementación, podrá entender todos los detalles y los procedimientos recomendados necesarios para organizar y coordinar las complejas estrategias de implementación de las soluciones de múltiples nodos, basadas en cualquier tecnología, aprovechando las plantillas del Administrador de recursos de Azure. Aunque no es obligatorio, un enfoque recomendado consiste en estructurar los archivos de plantillas, tal como se muestra en el diagrama siguiente:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Básicamente, se sugiere este enfoque para:

-	Definir el archivo de plantilla principal como punto central de coordinación para todas las actividades de implementación específicas y aprovechar la vinculación de las plantillas para invocar ejecuciones de subplantillas.
-	Crear archivos de plantillas específicas que implementarán todos los recursos compartidos en todas las demás tareas de implementación específicas (cuentas de almacenamiento, configuración de red virtual, etc.). Esto se puede reutilizar en gran medida entre las implementaciones que tienen requisitos similares en cuanto a una infraestructura común.
-	Incluir plantillas de recursos opcionales para identificar requisitos específicos de un recurso determinado.
-	Para los miembros idénticos de un grupo de recursos (nodos de un clúster, etc.), crear plantillas específicas que aprovechen bucles de recursos a fin de implementar varias instancias con propiedades únicas.
-	Para todas las tareas de implementación posteriores (instalación del producto, configuraciones, etc.), aprovechar las extensiones de implementación de scripts y crear scripts específicos de cada tecnología.

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

<!---HONumber=Oct15_HO2-->