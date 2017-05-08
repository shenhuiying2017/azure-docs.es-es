---
title: "Creación de un conjunto de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Cree e implemente un conjunto de escalado de máquinas virtuales Linux o Windows de Azure con la CLI de Azure, PowerShell, una plantilla o Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/30/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 736918ea310f276d961fa396f719b2b7809f0c0f
ms.lasthandoff: 04/27/2017

---

# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Creación e implementación de un conjunto de escalado de máquinas virtuales
Los conjuntos de escalado de máquinas virtuales facilitan la implementación y administración de máquinas virtuales idénticas como conjunto. Los conjuntos de escala proporcionan una capa de proceso altamente escalable y personalizable para aplicaciones de gran escala y admiten imágenes de la plataforma Windows, imágenes de la plataforma Linux, imágenes personalizadas y extensiones. Para más información sobre los conjuntos de escalado, consulte [Conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-overview.md).

En este tutorial se muestra cómo puede crear un conjunto de escalado de máquinas virtuales **sin** usar Azure Portal. Para más información sobre cómo usar Azure Portal, consulte [Procedimiento para crear un conjunto de escalado de máquinas virtuales con Azure Portal](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Para más información sobre los recursos de Azure Resource Manager, consulte [Implementación mediante Azure Resource Manager frente al modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Si usa la CLI de Azure 2.0 o Azure PowerShell para crear un conjunto de escalado, primero debe iniciar sesión en la suscripción.

Para más información sobre cómo instalar y configurar Azure e iniciar sesión en Azure con la CLI de Azure o PowerShell, consulte [Introducción a la CLI de Azure 2.0](/cli/azure/get-started-with-azure-cli.md) o [Introducción a los cmdlets de Azure PowerShell](/powershell/azure/overview).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

En primer lugar, debe crear un grupo de recursos con el que esté asociado el conjunto de escalado de máquinas virtuales.

```azurecli
az group create --location westus2 --name vmss-test-1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name vmss-test-1
```

## <a name="create-from-azure-cli"></a>Creación desde la CLI de Azure

Con la CLI de Azure, puede crear un conjunto de escalado de máquinas virtuales con un mínimo de esfuerzo. Si omite los valores predeterminados, se los proporcionaremos. Por ejemplo, si no especifica información de ninguna red virtual, se creará una. Si se omiten las partes siguientes, se crean automáticamente: 
- Un equilibrador de carga
- Una red virtual
- Una dirección IP pública

Cuando elija la imagen de máquina virtual que desea usar en el conjunto de escalado de máquinas virtuales, tendrá algunas opciones:

- URN  
Identificador de un recurso:  
**Win2012R2Datacenter**

- Alias de URN  
Nombre descriptivo de un URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- Identificador de recurso personalizado  
Ruta de acceso a un recurso de Azure:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- Recurso web  
Ruta a un URI de HTTP:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>Puede encontrar una lista de las imágenes disponibles con `az vm image list`.

Para crear un conjunto de escalado de máquinas virtuales, se debe especificar lo siguiente:

- Grupos de recursos 
- Nombre
- Imagen del sistema operativo
- Información de autenticación 
 
En el ejemplo siguiente se crea un conjunto de escalado de máquinas virtuales básico (este paso puede demorar unos minutos).

```azurecli
az vmss create --resource-group vmss-test-1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

Cuando finaliza el comando, se habrá creado el conjunto de escalado de máquinas virtuales. Debe obtener la dirección IP de la máquina virtual para que pueda conectarse a ella. Puede obtener información variada acerca de la máquina virtual (incluyendo la dirección IP) con el siguiente comando. 

```azurecli
az vmss list-instance-connection-info --resource-group vmss-test-1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>Creación desde PowerShell

PowerShell es más complicado de usar que la CLI de Azure. Mientras que la CLI de Azure proporciona valores predeterminados para los recursos relacionados con las redes (como equilibradores de carga, direcciones IP y redes virtuales), PowerShell no lo hace. Además, hacer referencia a una imagen con PowerShell también es un poco más complicado. Puede usar los siguientes cmdlets para obtener imágenes:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

Los cmdlets se pueden canalizar en secuencia. A continuación le mostramos un ejemplo de cómo obtener todas las imágenes para la región **Oeste de EE. UU. 2** con un publicador que lleva el nombre **microsoft**.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

El flujo de trabajo para crear un conjunto de escalado de máquinas virtuales es el siguiente:

1. Cree un objeto de configuración que incluya información sobre el conjunto de escalado.
2. Haga referencia a la imagen de SO base.
3. Configure el sistema operativo: autenticación, prefijo del nombre de la máquina virtual, usuario y contraseña.
4. Configure las redes.
5. Cree el conjunto de escalado.

En este ejemplo se crean un conjunto de escalado básico de dos instancias con Windows Server 2016 instalado.

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location WestUS2 -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName "vmss-test-1" -Location "westus2" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ip-address" -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName vmss-test-1 -Name my-scale-set -VirtualMachineScaleSet $vmssConfig
```

## <a name="create-from-a-template"></a>Creación desde una plantilla

Puede implementar un conjunto de escalado de máquinas virtuales con una plantilla de Azure Resource Manager. Puede crear una plantilla o usar una del [repositorio de plantillas](https://azure.microsoft.com/resources/templates/?term=vmss). Puede implementar estas plantillas directamente en la suscripción de Azure.

>[!NOTE]
>Para crear su propia plantilla, debe crear un archivo de texto JSON. Para información general sobre cómo crear y personalizar una plantilla, consulte [Plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

[GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set) tiene una plantilla de ejemplo a su disposición. Para más información sobre cómo crear y usar ese ejemplo, consulte [Conjunto de escalado mínimo viable](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>Creación desde Visual Studio

Con Visual Studio, puede crear un proyecto de grupo de recursos de Azure y agregarle una plantilla de conjunto de escalado de máquinas virtuales. Puede elegir si van a importar desde GitHub o desde la galería de aplicaciones web de Azure. También se genera un script de PowerShell de implementación. Para más información, consulte [Procedimiento para crear un conjunto de escalado de máquinas virtuales con Visual Studio](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Creación desde Azure Portal

Azure Portal proporciona una forma cómoda de crear rápidamente un conjunto de escalado. Para más información, consulte [Procedimiento para crear un conjunto de escalado de máquinas virtuales con Azure Portal](virtual-machine-scale-sets-portal-create.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los [discos de datos](virtual-machine-scale-sets-attached-disks.md).

Aprenda a [administrar las aplicaciones](virtual-machine-scale-sets-deploy-app.md).

