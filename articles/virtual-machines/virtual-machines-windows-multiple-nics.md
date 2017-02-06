---
title: "Creación de una VM Windows con varias tarjetas de interfaz de red | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Windows con varias tarjetas de interfaz de red conectadas a ella mediante Azure PowerShell o las plantillas de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7167048a287bee7c26cfc08775dcb84f9e7c2eed
ms.openlocfilehash: 46156a3331585b47761432c13462dffeb0b7eeb5


---
# <a name="creating-a-windows-vm-with-multiple-nics"></a>Creación de una máquina virtual Windows con varias tarjetas de interfaz de red
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común sería tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo se proporcionan comandos rápidos para crear una máquina virtual que tiene conectadas varias NIC. Para más información, lo que incluye cómo crear varias NIC dentro de sus propios scripts de PowerShell, lea más sobre la [implementación de máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamaños de máquina virtual](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

> [!WARNING]
> Cuando crea una máquina virtual, debe asociar varias NIC; no es posible agregar NIC a una máquina virtual existente. También puede [crear una máquina virtual en función de los discos virtuales originales](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y crear varias NIC mientras implementa la máquina virtual.
> 
> 

## <a name="create-core-resources"></a>Creación de recursos principales
Asegúrese de que tiene la [Azure PowerShell más reciente instalada y configurada](/powershell/azureps-cmdlets-docs). Inicie sesión en una cuenta de Azure:

```powershell
Login-AzureRmAccount
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `myVM`.

En primer lugar, cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUs`:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Cree una cuenta de almacenamiento que contenga las máquinas virtuales: En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Creación de redes virtuales y subredes
Defina dos subredes de red virtual: una para el tráfico front-end y otra para el tráfico back-end. En el ejemplo siguiente se definen dos subredes, denominadas `mySubnetFrontEnd` y `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Cree las redes virtuales y las subredes. En el ejemplo siguiente se crea una red virtual denominada `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Creación de varias NIC
Cree dos NIC y asocie una de ellas a la subred front-end y la otra a la subred back-end. En el ejemplo siguiente se crean dos NIC, denominadas `myNic1` y `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Normalmente también se crea un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) o un [equilibrador de carga](../load-balancer/load-balancer-overview.md) que ayuden a administrar y distribuir el tráfico entre las distintas máquinas virtuales. En el [artículo más detallado sobre máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) se proporcionan los pasos para crear un grupo de seguridad de red y asignar NIC.

## <a name="create-the-virtual-machine"></a>Creación de la máquina virtual
Comience ahora a compilar la configuración de la máquina virtual. El tamaño de cada máquina virtual tiene un límite en cuanto al número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

En primer lugar, establezca las credenciales de la máquina virtual en la variable `$cred` como se indica a continuación:

```powershell
$cred = Get-Credential
```

En el ejemplo siguiente se define una máquina virtual denominada `myVM` y se usa un tamaño de máquina virtual que admite hasta dos tarjetas de interfaz de red (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Cree el resto de la configuración de la máquina virtual. En el ejemplo siguiente se crea una máquina virtual con Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Conecte las dos NIC que creó anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configure el almacenamiento y el disco virtual para la nueva máquina virtual:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Por último, cree una máquina virtual:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [introducción a Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copia*](../azure-resource-manager/resource-group-create-multiple.md). 

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `myNic1`, `MyNic2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Pasos siguientes
Asegúrese de revisar los [tamaños de máquina virtual Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual. 

Recuerde que no se pueden agregar NIC adicionales a una máquina virtual existente; debe crear todas las NIC al implementar la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que dispone de toda la conectividad de red necesaria desde el principio.




<!--HONumber=Jan17_HO1-->


