<properties 
	pageTitle="Plantilla del Administrador de recursos de la granja de servidores SharePoint de tres servidores" 
	description="Siga los pasos de la estructura de la plantilla del Administrador de recursos de Azure para la granja de servidores SharePoint de tres servidores." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Plantilla del Administrador de recursos de la granja de servidores SharePoint de tres servidores

Este tema le guiará por la estructura del archivo de plantilla azuredeploy.json para la granja de servidores SharePoint de tres servidores. Puede ver el contenido de esta plantilla en el explorador desde [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json).

Como alternativa, para examinar una copia local del archivo azuredeploy.json, designe una carpeta local como ubicación del archivo y créela (por ejemplo, C:\Azure\Templates\SharePointFarm). Rellene el nombre de la carpeta y ejecute estos comandos en el símbolo del sistema de Azure PowerShell.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"	
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Abra la plantilla azuredeploy.json en un editor de texto o una herramienta de su elección. A continuación se describe la estructura del archivo de plantilla y el propósito de cada sección.

## Sección “parameters”

La sección "parameters" especifica los parámetros que se utilizan para introducir datos en esta plantilla. Se puede definir un máximo de 50 parámetros. Este es un ejemplo de un parámetro para la ubicación de Azure:

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## Sección "variables"

La sección "variables" especifica las variables que se pueden utilizar en esta plantilla. Se puede definir un máximo de 100 variables. Estos son algunos ejemplos:

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## Sección "resources"

La sección **"resources"** especifica la información necesaria para implementar los recursos de la granja de servidores de SharePoint en un grupo de recursos. Se pueden definir un máximo de 250 recursos y las dependencias de recursos pueden definirse con 5 niveles de profundidad.

Esta sección contiene las siguientes subsecciones:

### Microsoft.Storage/storageAccounts  

En esta sección se crea una nueva cuenta de almacenamiento para todos los recursos de disco y VHD para la granja de servidores. Este es el código JSON para la cuenta de almacenamiento:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

En estas secciones se crea un conjunto de direcciones IP públicas a través de las cuales se pueden alcanzar las máquinas virtuales. Este es un ejemplo:

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

Estas secciones crean tres conjuntos de disponibilidad, uno para cada capa de la implementación:

- Controladores de dominio de Active Directory
- Clúster de SQL Server
- Servidores de SharePoint

Aquí tiene un ejemplo:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

En esta sección se crea una red virtual solo en la nube con tres subredes (una para cada capa de la implementación) en la que se colocan las máquinas virtuales. Este es el código JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

En estas secciones se crean instancias del equilibrador de carga para cada máquina virtual que proporcione NAT y filtrado de tráfico para el tráfico entrante desde Internet. Para cada equilibrador de carga, configure los ajustes de las reglas NAT de front-end, back-end y de entrada. Por ejemplo, hay reglas de tráfico de escritorio remoto para cada máquina virtual y, para el servidor SharePoint, una regla para permitir el tráfico de web entrante (puerto 80 de TCP) desde Internet. Este es el ejemplo para el servidor SharePoint:


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

En estas secciones se crea una interfaz de red para cada máquina virtual y se configuran una dirección IP estática para el controlador de dominio. Este es el ejemplo para la interfaz de red del controlador de dominio:

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

En estas secciones se crean y configuran las tres máquinas virtuales en la implementación.

En la primera sección se crea y configura el controlador de dominio que:

- Especifica la cuenta de almacenamiento, el conjunto de disponibilidad, la interfaz de red y la instancia de equilibrador de carga
- Agrega un disco adicional
- Ejecuta un script de PowerShell para configurar el controlador de dominio

Este es el código JSON:

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

Una sección adicional para cada controlador de dominio que comienza por **"name": "UpdateVNetDNS"** configura los servidores DNS de la red virtual para que usen la dirección IP estática del controlador de dominio.

En la sección siguiente **"type": "Microsoft.Compute/virtualMachines"** se crean las máquinas virtuales de SQL Server en la implementación y:

- Especifica la cuenta de almacenamiento, el conjunto de disponibilidad, el equilibrador de carga, la red virtual y la interfaz de red
- Agrega un disco adicional

Las secciones **"Microsoft.Compute/virtualMachines/extensions"** adicionales llaman al script de PowerShell para configurar SQL Server.

En la sección siguiente **"type": "Microsoft.Compute/virtualMachines"** se crea la máquina virtual de SharePoint en la implementación, al especificar la cuenta de almacenamiento, el conjunto de disponibilidad, el equilibrador de carga, la red virtual y la interfaz de red. Las secciones **"Microsoft.Compute/virtualMachines/extensions"** adicionales llaman al script de PowerShell para configurar la granja de servidores SharePoint.

Tenga en cuenta la organización general de las subsecciones de la sección **"resources"** del archivo JSON:

1.	Cree los elementos de infraestructura de Azure que son necesarios para admitir varias máquinas virtuales (una cuenta de almacenamiento, direcciones IP públicas, conjuntos de disponibilidad, una red virtual, interfaces de red, instancias de equilibrador de carga).
2.	Cree la máquina virtual del controlador de dominio, que utiliza los elementos específicos y comunes de la infraestructura de Azure previamente creados, agrega un disco de datos y ejecuta un script de PowerShell. Además, actualice la red virtual para usar la dirección IP estática del controlador de dominio.
3.	Cree la máquina virtual de SQL Server, que utiliza los elementos específicos y comunes de la infraestructura de Azure previamente creados para el controlador de dominio, agrega discos de datos y ejecuta un script de PowerShell para configurar SQL Server.
4.	Cree la máquina virtual del servidor SharePoint, que utiliza los elementos específicos y comunes de la infraestructura de Azure previamente creados y ejecuta un script de PowerShell para configurar la granja de servidores SharePoint.

Su propia plantilla JSON para construir una infraestructura de múltiples capas en Azure debe seguir los mismos pasos:

1.	Cree los elementos comunes (cuenta de almacenamiento, red virtual), específicos de la capa (conjuntos de disponibilidad) y específicos de la máquina virtual (direcciones IP públicas, conjuntos de disponibilidad, interfaces de red e instancias de equilibrador de carga) de la infraestructura de Azure necesarios para su implementación.
2.	Para cada capa de la aplicación (por ejemplo, autenticación, base de datos, web), cree y configure los servidores de esa capa mediante los elementos comunes (cuenta de almacenamiento, red virtual), específicos de la capa (conjuntos de disponibilidad) y específicos de la máquina virtual (direcciones IP públicas, conjuntos de disponibilidad, interfaces de red e instancias de equilibrador de carga).

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](../resource-group-overview.md)

[Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

 

<!---HONumber=58_postMigration-->