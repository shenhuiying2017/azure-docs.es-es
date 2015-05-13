<properties
	pageTitle="DataStax en la plantilla del Administrador de recursos de Ubuntu"
	description="Aprenda a implementar fácilmente un nuevo clúster de DataStax en máquinas virtuales de Ubuntu con Azure PowerShell o CLI de Azure y una plantilla del Administrador de recursos"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax en la plantilla del Administrador de recursos de Ubuntu

DataStax es un reconocido líder del sector en el desarrollo y la entrega de soluciones basadas en Apache Cassandra ™ comercialmente admitido y preparado para el ámbito empresarial, la tecnología de bases de datos distribuida NoSQL de código abierto ampliamente reconocida por ser ágil, siempre activada y escalable de forma predecible a cualquier tamaño. DataStax, disponible en los modelos Enterprise \(DSE\) y Community \(DSC\), proporciona capacidades como informática en memoria, seguridad a nivel empresarial, análisis integrados rápidos y potentes, y búsqueda empresarial.

Ahora, además de lo que ya estaba disponible en Azure Marketplace, puede implementar fácilmente un nuevo clúster de DataStax en máquinas virtuales de Ubuntu mediante Azure PowerShell o CLI de Azure y una plantilla del Administrador de recursos.

Los clústeres recién implementados basados en esta plantilla tendrán la topología descrita en el siguiente diagrama, aunque se pueden lograr fácilmente otras topologías personalizando el enfoque presentado:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Básicamente, a través de un parámetro puede definir el número de nodos que se implementarán en el nuevo clúster de Apache Cassandra y, además, también se implementará una instancia del servicio del centro de operaciones de DataStax en una máquina virtual independiente dentro de la misma red virtual, lo que proporciona la capacidad de supervisar el estado del clúster y todos los nodos individuales, agregar o quitar nodos y llevar a cabo todas las tareas administrativas relacionadas con ese clúster.

Una vez completada la implementación, puede tener acceso a la instancia de máquina virtual del centro de operaciones de Datastax con la dirección DNS configurada. La máquina virtual del centro de operaciones tiene el puerto SSH 22 habilitado, así como el puerto 8443 para HTTPS. La dirección DNS para el centro de operaciones incluirá el dnsName y la región especificados como parámetros al crear una implementación basada en esta plantilla con el formato `{dnsName}.{region}.cloudapp.azure.com`. Si ha creado una implementación con el parámetro `dnsName` establecido en "datastax" en la región "West US", podría tener acceso a la máquina virtual del centro de operaciones de Datastax para la implementación en `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]El certificado utilizado en la implementación es un certificado autofirmado que creará una advertencia de explorador. Puede seguir el proceso en el sitio web de [Datastax](http://www.datastax.com/) para reemplazar el certificado por su propio certificado SSL.

Antes de entrar en más detalles relacionados con el Administrador de recursos de Azure y la plantilla utilizada para esta implementación, asegúrese de que tiene Azure, PowerShell y CLI de Azure configurados y listos para usar.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Creación de un clúster de Cassandra basado en DataStax con una plantilla del Administrador de recursos y Azure PowerShell

Siga estos pasos para crear un clúster de Apache Cassandra basado en DataStax, mediante una plantilla del Administrador de recursos en el repositorio de plantillas de Github con Azure PowerShell.

### Paso 1: Descarga del archivo JSON para la plantilla y otros archivos.

Designe una carpeta local como ubicación de la plantilla JSON y otros archivos y créela \(por ejemplo, C:\\Azure\\Templates\\DataStax\).

Rellene el nombre de la carpeta y ejecute estos comandos:

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

Como alternativa, también puede clonar el repositorio de plantillas mediante un cliente Git de su elección, por ejemplo:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Cuando haya finalizado, busque la carpeta datastax-on-ubuntu en C:\\Azure\\Templates.

### Paso 2: \(opcional\) Familiarización con los parámetros de la plantilla.

Al implementar soluciones no triviales, como un clúster de Apache Cassandra basado en DataStax, debe especificar un conjunto de parámetros de configuración para resolver algunos valores necesarios. Al declarar estos parámetros en la definición de la plantilla, es posible especificar valores durante la ejecución de la implementación mediante un archivo externo o en la línea de comandos.

Si busca la sección "parameters" en la parte superior del archivo azuredeploy.json, encontrará el conjunto de parámetros que requiere la plantilla para configurar un clúster de DataStax. A continuación se muestra un ejemplo de esa sección en la plantilla azuredeploy.json:

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
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
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

Con la descripción de los parámetros necesarios, incluidos detalles como tipos de datos, valores permitidos, etc. es evidente que esta sección es realmente útil para cualquier tarea de validación relacionada con valores de parámetros pasados en la ejecución de la plantilla en modo interactivo \(por ejemplo, PowerShell o CLI de Azure\), así como con cualquier interfaz de usuario de detección automática que puede crearse dinámicamente mediante el análisis de la lista de parámetros necesarios y su descripción.

### Paso 3: Implementación del nuevo clúster de DataStax con la plantilla.

Preparar un archivo de parámetros para su implementación significa crear un archivo JSON que contiene los valores de tiempo de ejecución para todos los parámetros, que se pasarán luego como una sola entidad al comando de implementación.

A continuación se muestra un ejemplo que puede encontrar en el archivo azuredeploy-parameters.json:

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

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure, la carpeta para el archivo JSON guardado y, a continuación, ejecute estos comandos:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se extraerán los valores de parámetros desde el archivo JSON y se empezará a ejecutar la plantilla según corresponda. Al definir y utilizar varios archivos de parámetros con sus distintos entornos \(por ejemplo, prueba, producción, etc.\), se promoverá la reutilización de plantillas y se simplificarán las soluciones complejas de entornos múltiples.

Durante la implementación, recuerde que debe crearse una nueva cuenta de almacenamiento de Azure para que el nombre proporcionado como parámetro de cuenta de almacenamiento sea único y cumpla todos los requisitos de una cuenta de almacenamiento de Azure.

Durante la implementación y después de ella, puede comprobar todas las solicitudes realizadas durante el aprovisionamiento, incluidos todos los errores producidos.

Para ello, vaya al [Portal de Azure](https://portal.azure.com) y realice lo siguiente:

- Haga clic en "Examinar" en la barra de navegación de la izquierda, desplácese hacia abajo y haga clic en "Grupos de recursos".  
- Después de hacer clic en el grupo de recursos que acaba de crear, aparecerá la hoja "Grupo de recursos".  
- Al hacer clic en la barra de gráficos "Eventos" en la parte "Supervisión" de la hoja "Grupo de recursos", puede ver los eventos para la implementación:
- Al hacer clic en eventos individuales, puede profundizar más en los detalles de cada operación individual que se realiza en nombre de la plantilla

Después de las pruebas, si desea quitar este grupo de recursos y todos sus recursos \(cuenta de almacenamiento, máquina virtual y red virtual\), utilice este comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Paso 3-b: Implementación de un clúster de DataStax con una plantilla del Administrador de recursos mediante CLI de Azure

Equivalente, desde un punto de vista funcional, al enfoque de PowerShell enumerado anteriormente, la implementación de un clúster de Apache Cassandra con CLI de Azure requiere crear primero un grupo de recursos especificando el nombre y la ubicación:

	azure group create dsc "West US"

Y, a continuación, invocar la creación de una implementación y pasar el nombre del grupo de recursos, el archivo de parámetros y la plantilla real tal como se muestra a continuación:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

También es posible comprobar el estado de las implementaciones individuales invocando el comando siguiente:

	azure group deployment list dsc

## Paseo por la estructura de plantillas y la organización de archivos creadas para implementar DataStax en Ubuntu

Si desea crear un enfoque sólido y reutilizable para el diseño de plantillas del Administrador de recursos, hay que pensar aún más en cómo organizar la serie de tareas complejas e interrelacionadas necesarias durante la implementación de una solución compleja como DataStax. Al aprovechar las capacidades de **vinculación de plantillas** de ARM, los **bucles de recursos** así como la ejecución de scripts mediante extensiones relacionadas, es posible implementar un enfoque modular que se puede reutilizar con prácticamente cualquier implementación compleja basada en plantillas.

En este diagrama se describen las relaciones entre todos los archivos descargados de GitHub para esta implementación:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

Ya se ha mencionado el rol de **azuredeploy-parameters.json**, que se utilizará para pasar un conjunto determinado de valores de parámetros durante la ejecución de la plantilla, pero el núcleo de este enfoque de implementación se encuentra en **azuredeploy.json**. Tras omitir la sección "parameters", tal como se ha descrito anteriormente en este documento, la sección siguiente está representada por **"variables"**. Básicamente, contiene cierto número de campos \(tipos de datos o fragmentos JSON\) que se establecerán en constantes o valores calculados en tiempo de ejecución, tal como se puede ver en el ejemplo siguiente:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
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

Al explorar en profundidad este ejemplo, puede ver dos enfoques diferentes. En este primer fragmento, la variable "osSettings" se establecerá en un elemento JSON anidado que contiene 4 pares clave-valor:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
En este segundo fragmento de código, la variable "scripts" es una matriz JSON donde se calcularán elementos únicos en tiempo de ejecución mediante una función de lenguaje de plantilla \(concat\) y el valor de otra variable, además de constantes de cadenas:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

La sección **"resources"** es donde ocurre la mayor parte de la acción. Al analizar detenidamente esta sección, puede identificar de inmediato dos casos distintos: el primero es un elemento definido de tipo `Microsoft.Resources/deployments` que básicamente significa la invocación de una implementación anidada dentro de la principal. Mediante el elemento `templateLink` \(y la propiedad de versión relacionada\), es posible especificar un archivo de plantilla vinculado que se invocará pasando un conjunto de parámetros como entrada, tal como se puede observar en este fragmento:

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

A partir de este primer ejemplo está claro que **azuredeploy.json** se ha organizado en este escenario como un mecanismo de coordinación, al invocar cierto número de otros archivos de plantillas, cada uno de los cuales es responsable de una parte de las actividades de implementación necesarias.

En particular, las siguientes plantillas vinculadas se utilizarán para esta implementación:

-	**shared-resource.json**: contiene la definición de todos los recursos que se van a compartir en toda la implementación. Algunos ejemplos son las cuentas de almacenamiento que se usan para almacenar discos del sistema operativo de la máquina virtual o redes virtuales.
-	**opscenter-resources.json**: implementa una máquina virtual de OpsCenter y todos los recursos relacionados, como la interfaz de red, la dirección IP pública, etc.
-	**opscenter-install-resources.json**: implementa la extensión de máquina virtual de OpsCenter \(script personalizado para Linux\) que invocará el archivo de comandos Bash específico \(\*\*opscenter.sh\*\*\) necesario para configurar el servicio de OpsCenter dentro de esa máquina virtual.
-	**ephemeral-nodes-resources.json**: implementa todos los nodos del clúster de máquinas virtuales y los recursos conectados \(por ejemplo, tarjetas de red, IP privadas, etc.\). Esta plantilla también implementará extensiones de máquinas virtuales \(scripts personalizados para Linux\) e invocará un script Bash \(\*\*dsenode.sh\*\*\) para instalar físicamente los bits de Apache Cassandra en cada nodo.

Analicemos esta última plantilla, ya que es una de las más interesantes desde el punto de vista del desarrollo de una plantilla. Un concepto importante que se debe resaltar es cómo puede una sola plantilla implementar varias copias de un tipo de recurso único y puede establecer para cada instancia valores únicos para la configuración requerida. Este concepto se conoce como **bucle de recursos**.

Cuando se invoca **ephemeral-nodes-resources.json** desde el archivo principal **azuredeploy.json**, se proporciona un parámetro llamado **nodeCount** como parte de la lista de parámetros. En la plantilla secundaria, se usará ese parámetro \(número de nodos que se debe implementar en el clúster\) dentro del elemento **"copy"** de cada recurso que debe implementarse en varias copias, tal como se presenta en el siguiente fragmento. Para todas las configuraciones donde sea necesario especificar valores únicos entre diferentes instancias del recurso implementado, se puede utilizar la función **copyindex\(\)** para obtener un valor numérico que indica el índice actual en esa creación de bucle de recurso en particular. En el fragmento siguiente se puede ver este concepto aplicado a la creación de varias máquinas virtuales para los nodos del clúster:

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

Otro concepto importante en la creación de recursos es la capacidad de especificar las dependencias y prioridades entre los recursos, tal como se puede observar en la matriz JSON **dependsOn**. En esta plantilla en particular, cada nodo tendrá también un disco de 1 TB conectado \(consulte `dataDisks`\) que se puede utilizar para hospedar copias de seguridad e instantáneas de la instancia de Apache Cassandra.

Se da formato a los discos conectados como parte de las actividades de preparación del nodo que desencadena la ejecución del archivo de script **dsenode.sh**. De hecho, la primera fila de ese script invoca otro script:

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh forma parte de la carpeta **shared\_scripts\\ubuntu**, en el repositorio de Github azure-quickstart-tempates y contiene funciones muy útiles para el montaje, formato y desmontaje de discos que puede volver a utilizar cada vez que necesite ejecutar tareas similares como parte de la creación de plantillas.

Otro fragmento interesante es el relacionado con las extensiones de máquinas virtuales de CustomScriptForLinux. Estas se instalan como un tipo independiente de recurso, con una dependencia en cada nodo del clúster \(y en la instancia de OpsCenter\), aprovechando el mismo mecanismo de bucles de recursos descritos para las máquinas virtuales:

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

Todos los demás archivos de plantillas requeridos para esta implementación crean instancias únicas de todos los recursos necesarios, por lo que pueden considerarse una versión simple de este archivo **ephemeral-nodes-resources.json**.

Al familiarizarse con los demás archivos incluidos en esta implementación, podrá entender todos los detalles y los procedimientos recomendados necesarios para organizar y coordinar las complejas estrategias de implementación de las soluciones de múltiples nodos, basadas en cualquier tecnología, aprovechando las plantillas del Administrador de recursos de Azure. Aunque no es obligatorio, un enfoque recomendado consiste en estructurar los archivos de plantillas, tal como se muestra en el diagrama siguiente:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

Básicamente, se sugiere este enfoque para:

-	Definir el archivo de plantilla principal como punto central de coordinación para todas las actividades de implementación específicas y aprovechar la vinculación de las plantillas para invocar ejecuciones de subplantillas
-	Crear archivos de plantillas específicas que implementarán todos los recursos compartidos en todas las demás tareas de implementación específicas \(por ejemplo, cuentas de almacenamiento, configuración de red virtual, etc.\). Esto se puede reutilizar en gran medida entre las implementaciones que tienen requisitos similares en cuanto a una infraestructura común.
-	Incluir plantillas de recursos opcionales para identificar requisitos específicos de un recurso determinado
-	Para los miembros idénticos de un grupo de recursos \(nodos de un clúster, etc.\), crear plantillas específicas que aprovechen bucles de recursos a fin de implementar varias instancias con propiedades únicas
-	Para todas las tareas de implementación posteriores \(p. ej. instalación del producto, configuraciones, etc.\), aprovechar las extensiones de implementación de scripts y crear scripts específicos de cada tecnología

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=52-->
