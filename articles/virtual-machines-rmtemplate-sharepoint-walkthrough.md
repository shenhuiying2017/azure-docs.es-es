<properties 
	pageTitle="Plantilla de Administrador de recursos de la granja de servidores de SharePoint de alta disponibilidad" 
	description="Siga los pasos de la estructura de la plantilla del Administrador de recursos de Azure para la granja de servidores de SharePoint de alta disponibilidad." 
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

# Plantilla de Administrador de recursos de la granja de servidores de SharePoint de alta disponibilidad

Este tema le guiará por la estructura del archivo de plantilla azuredeploy.json para la granja de servidores de SharePoint de alta disponibilidad.

## Sección "parameters"

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## Sección "resources"

La sección **"resources"** especifica la información necesaria para implementar los recursos de la granja de servidores de SharePoint en un grupo de recursos. Se pueden definir un máximo de 250 recursos y las dependencias de recursos pueden definirse con 5 niveles de profundidad.

Esta sección contiene las siguientes subsecciones:

### Microsoft.Storage/storageAccounts  

En esta sección se crea una nueva cuenta de almacenamiento para todos los recursos de disco y VHD para la granja de servidores. Este es el código JSON para la cuenta de almacenamiento:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

En estas secciones se crea un conjunto de direcciones IP públicas a través de las cuales se pueden alcanzar las máquinas virtuales. Este es un ejemplo:

	{
		"apiVersion": "2014-12-01-preview",
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

Estas secciones crean cuatro conjuntos de disponibilidad, uno para cada capa de la implementación:

- Controladores de dominio de Active Directory
- Clúster de SQL Server
- Servidores de capa de aplicación
- Servidores de capa web

Este es un ejemplo:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

En esta sección se crea una red virtual solo en la nube con cuatro subredes (una para cada capa de la implementación) en la que se colocan las máquinas virtuales. Este es el código JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

En estas secciones se crean instancias del equilibrador de carga para cada máquina virtual que proporcione NAT y filtrado de tráfico para el tráfico entrante desde Internet. Para cada equilibrador de carga, configure los ajustes de las reglas NAT de front-end, back-end y de entrada. Por ejemplo, hay reglas de tráfico de escritorio remoto para cada máquina virtual y una regla para permitir el tráfico de web entrante (puerto 80 de TCP) desde Internet para los servidores de capa web. Este es el ejemplo para el servidor de capa de web:

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

En estas secciones se crea una interfaz de red para cada máquina virtual y se configuran las direcciones IP estáticas para los controladores de dominio. Este es el ejemplo para la interfaz de red para el controlador de dominio principal:

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

En estas secciones se crean y configuran las nueve máquinas virtuales en la implementación.

Las dos primeras secciones principales crean y configuran los controladores de dominio en la implementación. Cada sección:

- Especifica la cuenta de almacenamiento, el conjunto de disponibilidad, la interfaz de red y la instancia de equilibrador de carga para cada máquina virtual del controlador de dominio.
- Agrega un disco adicional para cada máquina virtual del controlador de dominio.
- Ejecuta el script de PowerShell para configurar las máquinas virtuales como controladores de dominio.

Este es el ejemplo del controlador de dominio principal:

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


Una sección adicional después de cada controlador de dominio que comienza por **"nombre": "UpdateVNetDNS"** configura los servidores DNS de la red virtual para que usen las direcciones IP estáticas de los dos controladores de dominio.

Las tres secciones siguientes **"type": "Microsoft.Compute/virtualMachines"** crean las máquinas virtuales del clúster de SQL Server en la implementación. Cada sección:

- Especifica la cuenta de almacenamiento, el conjunto de disponibilidad, el equilibrador de carga, la red virtual y la interfaz de red en cada máquina virtual
- Agrega dos discos adicionales a cada servidor de SQL Server

Las secciones **"Microsoft.Compute/virtualMachines/extensions"** adicionales llaman al script de PowerShell para configurar las máquinas virtuales del clúster de SQL Server, el clúster de SQL Server y habilitar grupos de disponibilidad AlwaysOn.

Las cuatro secciones siguientes **"type": "Microsoft.Compute/virtualMachines"** crean las máquinas virtuales de la granja de servidores de SharePoint en la implementación. Cada sección especifica la cuenta de almacenamiento, el conjunto de disponibilidad, el equilibrador de carga, la red virtual y la interfaz de red en cada máquina virtual.

Las secciones **"Microsoft.Compute/virtualMachines/extensions"** adicionales llaman a scripts de PowerShell para configurar los servidores de SharePoint como una granja de servidores de SharePoint.

Tenga en cuenta la organización general de las subsecciones de la sección **"resources"** del archivo JSON:

1.	Cree los elementos de infraestructura de Azure que son necesarios para admitir varias máquinas virtuales (una cuenta de almacenamiento, direcciones IP públicas, conjuntos de disponibilidad, una red virtual, interfaces de red, instancias de equilibrador de carga).
2.	Cree las máquinas virtuales del controlador de dominio, que utilizan los elementos específicos y comunes de infraestructura de Azure previamente creados, agregue los discos de datos y ejecute scripts de PowerShell. Además, actualice la red virtual para usar las direcciones IP estáticas de los controladores de dominio.
3.	Cree las máquinas virtuales de clúster de SQL Server, que utilizan los elementos específicos y comunes de infraestructura de Azure previamente creados para los controladores de dominio, agregue discos de datos y ejecute scripts de PowerShell para configurar el clúster y grupos de disponibilidad AlwaysOn de SQL Server.
4.	Cree las máquinas virtuales del servidor de SharePoint, que utilizan los elementos específicos y comunes de infraestructura de Azure previamente creados, agregue los discos de datos y ejecute scripts de PowerShell para configurar la granja de servidores de SharePoint.

Su propia plantilla JSON para construir una infraestructura de múltiples capas en Azure debe seguir los mismos pasos:

1.	Cree los elementos comunes (cuenta de almacenamiento, red virtual), específicos de la capa (conjuntos de disponibilidad) y específicos de la máquina virtual (direcciones IP públicas, conjuntos de disponibilidad, interfaces de red e instancias de equilibrador de carga) de la infraestructura de Azure necesarios para su implementación.
2.	Para cada capa de la aplicación (por ejemplo, autenticación, base de datos, web), cree y configure los servidores de esa capa mediante los elementos comunes (cuenta de almacenamiento, red virtual), específicos de la capa (conjuntos de disponibilidad) y específicos de la máquina virtual (direcciones IP públicas, conjuntos de disponibilidad, interfaces de red e instancias de equilibrador de carga).

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](resource-group-overview.md)

[Creación de plantillas de Administrador de recursos de Azure](resource-group-authoring-templates.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
