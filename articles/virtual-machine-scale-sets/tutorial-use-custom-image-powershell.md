---
title: 'Tutorial: Uso de una imagen de máquina virtual personalizada en un conjunto de escalado con Azure PowerShell | Microsoft Docs'
description: Obtenga información sobre cómo usar Azure PowerShell para crear una imagen de máquina virtual personalizada que puede usar para implementar un conjunto de escalado de máquinas virtuales
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a6ff5ebdf678972c686c972fd03041b362c1ff81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Creación y uso de una imagen personalizada para conjuntos de escalado de máquinas virtuales con Azure PowerShell
Al crear el conjunto de escalado, se especifica la imagen que se usará cuando se implementen las instancias de máquina virtual. Para reducir el número de tareas después de implementar las instancias de máquina virtual, puede usar una imagen de máquina virtual personalizada. Esta imagen de máquina virtual personalizada incluye la instalación o configuración de las aplicaciones necesarias. Las instancias de máquina virtual creadas en el conjunto de escalado usan la imagen de máquina virtual personalizada y están listas para atender el tráfico de la aplicación. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada a partir de la máquina virtual de origen
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial se requiere la versión 5.6.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 


## <a name="create-and-configure-a-source-vm"></a>Creación y configuración de una máquina virtual de origen
Primero, cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) y, después, cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Esta máquina virtual se usará después como origen para la imagen de máquina virtual personalizada. En el ejemplo siguiente, se crea una máquina virtual llamada *myCustomVM* en el grupo de recursos llamado *myResourceGroup*. Cuando se le solicite, escriba el nombre de usuario y la contraseña que se usarán como credenciales de inicio de sesión para la máquina virtual:

```azurepowershell-interactive
# Create a resource a group
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Para conectarse a la máquina virtual, obtenga la dirección IP pública con [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), tal y como se indica a continuación:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Cree una conexión remota con la máquina virtual. Si se utiliza Azure Cloud Shell, realice este paso desde un símbolo del sistema local de PowerShell o un cliente de Escritorio remoto. Proporcione su propia dirección IP mediante el comando anterior. Cuando se le solicite, escriba las credenciales que usó al crear la máquina virtual en el primer paso:

```powershell
mstsc /v:<IpAddress>
```

Para personalizar la máquina virtual, vamos a instalar un servidor web básico. Cuando la instancia de máquina virtual del conjunto de escalado se implemente, tendrá todos los paquetes necesarios para ejecutar una aplicación web. Abra un símbolo de PowerShell local en la máquina virtual e instale el servidor web de IIS con [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) como se indica a continuación:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

El último paso para preparar la máquina virtual para su uso como una imagen personalizada es generalizar la máquina virtual. Sysprep quita todas las configuraciones y la información de la cuenta personal y restablece la máquina virtual a un estado limpio para futuras implementaciones. Para más información, consulte [Uso de Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx).

Para generalizar la máquina virtual, ejecute Sysprep y prepare la máquina virtual para poder usarla sin necesidad de configuraciones adicionales. Cuando termine, indique a Sysprep que apague la máquina virtual:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

La conexión remota a la máquina virtual se cierra automáticamente cuando termina el proceso de Sysprep y se apaga la máquina virtual.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Crear una imagen de máquina virtual personalizada a partir de la máquina virtual de origen
La máquina virtual de origen ahora está personalizada con el servidor web de IIS instalado. Vamos a crear la imagen de máquina virtual personalizada para usarla con un conjunto de escalado.

Para crear una imagen, es necesario desasignar la máquina virtual. Desasigne la máquina virtual con [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). A continuación, establezca el estado de la máquina virtual como generalizado con [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) para que la plataforma Azure sepa que la máquina virtual está preparada para usarla como imagen personalizada. Solo se puede crear una imagen de una máquina virtual generalizada:

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

La desasignación y generalización de la máquina virtual puede tardar unos minutos.

Ahora, cree una imagen de la máquina virtual mediante [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) y [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). En el ejemplo siguiente se crea una imagen llamada *myImage* a partir de la máquina virtual:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzureRmVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzureRmImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzureRmImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Creación de un conjunto de escalado a partir de la imagen de máquina virtual personalizada
Ahora, cree un conjunto de escalado con [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss), que usa el parámetro `-ImageName` para definir la imagen de máquina virtual personalizada creada en el paso anterior. Para distribuir el tráfico a las instancias individuales de VM, también se crea un equilibrador de carga. El equilibrador de carga incluye reglas para distribuir el tráfico en el puerto TCP 80, así como también permite el tráfico de Escritorio remoto en el puerto TCP 3389 y la conexión remota de PowerShell en el puerto TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -ImageName "myImage"
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.


## <a name="test-your-scale-set"></a>Prueba del conjunto de escalado
Para ver el conjunto de escalado en acción, obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) como se muestra a continuación:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Escriba la dirección IP pública en un explorador web. Se muestra la página web predeterminada de IIS, como en el ejemplo siguiente:

![IIS se ejecuta desde la imagen de máquina virtual personalizada](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Para quitar el conjunto de escalado y los recursos adicionales, elimine el grupo de recursos y todos sus recursos con [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). El parámetro `-Force` confirma que desea eliminar los recursos sin pedir confirmación adicional. El parámetro `-AsJob` devuelve el control a la petición de confirmación sin esperar a que finalice la operación.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a crear y utilizar una imagen de máquina virtual personalizada para los conjuntos de escalado con Azure PowerShell:

> [!div class="checklist"]
> * Crear y personalizar una máquina virtual
> * Desaprovisionar y generalizar la máquina virtual
> * Crear una imagen de máquina virtual personalizada
> * Implementar un conjunto de escalado que usa la imagen de máquina virtual personalizada

Vaya al siguiente tutorial para aprender cómo implementar aplicaciones en el conjunto de escalado.

> [!div class="nextstepaction"]
> [Implementación de aplicaciones en los conjuntos de escalado](tutorial-install-apps-powershell.md)