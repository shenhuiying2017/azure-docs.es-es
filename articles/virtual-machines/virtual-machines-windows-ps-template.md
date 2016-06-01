<properties
	pageTitle="Creación de una máquina virtual con la plantilla de Resource Manager | Microsoft Azure"
	description="Use una plantilla de Resource Manager y PowerShell para crear fácilmente una máquina virtual de Windows."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Creación de una máquina virtual Windows con una plantilla del Administrador de recursos

Este artículo presenta una plantilla de Azure Resource Manager y muestra cómo usar PowerShell para implementarlo. Esta plantilla implementa una sola máquina virtual que ejecuta Windows Server en una nueva red virtual con una sola subred.

Tardará unos 20 minutos en realizar los pasos de este artículo.

> [AZURE.IMPORTANT] Si desea que la máquina virtual forme parte de un conjunto de disponibilidad, agréguela al conjunto al crear la máquina virtual. Actualmente no es posible agregar una máquina virtual a un conjunto de disponibilidad una vez creada.

## Paso 1: Creación de un archivo de plantilla

Puede crear su propia plantilla usando la información que se encuentra en [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). También puede implementar las plantillas que se han creado para usted desde [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/). El ejemplo utilizado en este artículo es similar a la plantilla descrita en [Deploy a simple Windows VM in West US](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/) (Implementación de una máquina virtual de Windows simple en el oeste de EE. UU.).

1. Abra su editor de texto favorito y copie esta información de JSON a un archivo nuevo denominado *VirtualMachineTemplate.json*:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": {
              "type": "string",
              "metadata": {
                "Description": "The name of the storage account where the VM disk is stored."
              }
            },
            "adminUsername": {
              "type": "string",
              "metadata": {
                "Description": "The name of the administrator account on the VM."
              }
            },
            "adminPassword": {
              "type": "securestring",
              "metadata": {
                "Description": "The administrator account password on the VM."
              }
            },
            "dnsNameForPublicIP": {
              "type": "string",
              "metadata": {
                "Description": "The name of the public IP address used to access the VM."
              }
            }
          },
          "variables": {
            "location": "Central US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdisk1",
            "nicName": "nc1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "sn1",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "ip1",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "vm1",
            "vmSize": "Standard_A0",
            "virtualNetworkName": "vn1",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "apiVersion": "2015-06-15",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
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
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [
                  {
                    "name": "ipconfig1",
                    "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                      },
                      "subnet": {
                        "id": "[variables('subnetRef')]"
                      }
                    }
                  }
                ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computername": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku" : "[variables('windowsOSVersion')]",
                    "version":"latest"
                  },
                  "osDisk" : {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces": [
                    {
                      "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                    }
                  ]
                }
              }
            }
          ]
        }
        
    >[AZURE.NOTE] En este artículo se crea una máquina virtual que ejecuta una versión del sistema operativo Windows Server. Para obtener más información sobre la selección de otras imágenes, consulte [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o Powershell](virtual-machines-linux-cli-ps-findimage.md).
    
2. Guarde el archivo de plantilla.

## Paso 2: Creación del archivo de parámetros

Para especificar los valores de los parámetros del recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores y enviarlo al Administrador de recursos junto con la plantilla.

1. En el editor de texto, copie esta información de JSON a un archivo nuevo denominado *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

4. Guarde el archivo de parámetros.

## Paso 3: Instalación de Azure PowerShell

Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

## Paso 4: Creación de un grupo de recursos

Todos los recursos se deben implementar en un grupo de recursos. Para obtener más información, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.

	    Get-AzureLocation | sort Name | Select Name

2. Reemplace el valor de **$locName** por una ubicación de la lista, como **centro de EE. UU.** Cree la variable.

        $locName = "location name"
        
3. Reemplace el valor de **$rgName** por el nombre del nuevo grupo de recursos. Cree la variable y el grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Puede ver algo así:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

### Paso 7: Creación de recursos con la plantilla y los parámetros

1. Reemplace el valor de **$deployName** por el nombre de la implementación. Reemplace el valor de **$templatePath** por la ruta de acceso y el nombre del archivo de plantilla. Reemplace el valor de **$parameterFile** por la ruta de acceso y el nombre del archivo de parámetros. Cree las variables. 

        $deployName="deployment name"
        $templatePath = "template path"
        $parameterFile = "parameter file"

4. Implemente la plantilla.

        New-AzureRmResourceGroupDeployment -ResourceGroupName "davidmurg6" -TemplateFile $templatePath -TemplateParameterFile $parameterFile

    Verá algo parecido a lo siguiente:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            newStorageAccountName  String                     mytestsa1
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString
                            dnsNameForPublicIP  String                     mytestdns1

        Outputs           :

    >[AZURE.NOTE] También puede implementar las plantillas y los parámetros desde una cuenta de Almacenamiento de Azure. Para obtener más información, consulte [Usar Azure PowerShell con Almacenamiento de Azure](../storage-powershell-guide-full.md).

## Pasos siguientes

- Si hay problemas con la implementación, el paso siguiente sería mirar en [Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Aprenda a administrar la máquina virtual que acaba de crear. Para ello, consulte [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0518_2016-->