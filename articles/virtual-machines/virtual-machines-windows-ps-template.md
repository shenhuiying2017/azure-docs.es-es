---
title: Creación de una máquina virtual con la plantilla de Resource Manager | Microsoft Docs
description: Use una plantilla de Resource Manager y PowerShell para crear fácilmente una máquina virtual de Windows.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu

---
# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Creación de una máquina virtual Windows con una plantilla del Administrador de recursos
Este artículo presenta una plantilla de Azure Resource Manager y muestra cómo usar PowerShell para implementarla. Esta plantilla implementa una sola máquina virtual que ejecuta Windows Server en una nueva red virtual con una sola subred.

Tardará unos 20 minutos en realizar los pasos de este artículo.

> [!IMPORTANT]
> Si quiere que la máquina virtual forme parte de un conjunto de disponibilidad, agréguela al conjunto al crear la máquina virtual. Actualmente no es posible agregar una máquina virtual a un conjunto de disponibilidad una vez creada.
> 
> 

## <a name="step-1:-create-the-template-file"></a>Paso 1: Creación de un archivo de plantilla
Puede crear su propia plantilla usando la información que se encuentra en [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). También puede implementar las plantillas que se han creado para usted desde [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

1. Abra el editor de texto que prefiera y agregue el elemento de esquema y el elemento contentVersion necesarios:
   
        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. No siempre se necesitan [parámetros](../resource-group-authoring-templates.md#parameters), pero proporcionan una manera de introducir valores al implementar la plantilla. Agregue el elemento parameters y sus elementos secundarios después del elemento contentVersion:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
3. [variables](../resource-group-authoring-templates.md#variables) en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro. Agregue el elemento variables después de la sección parameters:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
4. [recursos](../resource-group-authoring-templates.md#resources) , como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen a continuación en la plantilla. Agregue la sección resources después de la sección variables:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
   
   > [!NOTE]
   > En este artículo se crea una máquina virtual que ejecuta una versión del sistema operativo Windows Server. Para más información sobre cómo seleccionar otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md).  
   > 
   > 
5. Guarde el archivo de plantilla como *VirtualMachineTemplate.json*.

## <a name="step-2:-create-the-parameters-file"></a>Paso 2: Creación del archivo de parámetros
Para especificar los valores de los parámetros de recurso que se han definido en la plantilla, debe crear un archivo de parámetros que contenga los valores que se han usado al implementar la plantilla.

1. En el editor de texto, copie este contenido JSON a un archivo nuevo denominado *Parameters.json*:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > Obtenga más información acerca de los [requisitos de usuario y la contraseña](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
   > 
   > 
2. Guarde el archivo de parámetros.

## <a name="step-3:-install-azure-powershell"></a>Paso 3: Instalación de Azure PowerShell
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

## <a name="step-4:-create-a-resource-group"></a>Paso 4: Creación de un grupo de recursos
Todos los recursos se deben implementar en un [grupo de recursos](../resource-group-overview.md).

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.
   
        Get-AzureRmLocation | sort DisplayName | Select DisplayName
2. Reemplace el valor de **$locName** por una ubicación de la lista, por ejemplo **Centro de EE. UU.**. Cree la variable.
   
        $locName = "location name"
3. Reemplace el valor de **$rgName** por el nombre del nuevo grupo de recursos. Cree la variable y el grupo de recursos.
   
        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Puede ver algo parecido a este ejemplo:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5:-create-the-resources-with-the-template-and-parameters"></a>Paso 5: Creación de recursos con la plantilla y los parámetros
Reemplace el valor de **$templateFile** por la ruta de acceso y el nombre del archivo de plantilla. Reemplace el valor de **$parameterFile** por la ruta de acceso y el nombre del archivo de parámetros. Cree las variables y después implemente la plantilla. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

> [!NOTE]
> También puede implementar las plantillas y los parámetros desde una cuenta de Almacenamiento de Azure. Para obtener más información, consulte [Usar Azure PowerShell con Azure Storage](../storage/storage-powershell-guide-full.md).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* Si se han producido problemas durante la implementación, el paso siguiente será consultar [Solución de problemas de implementaciones de grupo de recursos con Azure Portal](../resource-manager-troubleshoot-deployments-portal.md)
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md).

<!--HONumber=Oct16_HO2-->


