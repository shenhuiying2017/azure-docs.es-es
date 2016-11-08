---
title: Configuración de varias NIC en una máquina virtual Windows | Microsoft Docs
description: Aprenda a crear una máquina virtual con varias NIC conectadas a ella mediante Azure PowerShell o plantillas de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2016
ms.author: iainfou

---
# Creación de una máquina virtual con varias NIC
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común sería tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo se proporcionan comandos rápidos para crear una máquina virtual que tiene conectadas varias NIC. Para más información, lo que incluye cómo crear varias NIC dentro de sus propios scripts de PowerShell, lea más sobre la [implementación de máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamaños de máquina virtual](virtual-machines-windows-sizes.md) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

> [!WARNING]
> Cuando crea una máquina virtual, debe asociar varias NIC; no es posible agregar NIC a una máquina virtual existente. También puede [crear una nueva máquina virtual en función de los discos virtuales originales](virtual-machines-windows-specialized-image.md) y crear varias NIC mientras implementa la máquina virtual.
> 
> 

## Creación de recursos principales
Asegúrese de que tiene la [ Azure PowerShell más reciente instalada y configurada](../powershell-install-configure.md).

En primer lugar, crea un grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

Cree una cuenta de almacenamiento que contenga las máquinas virtuales:

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## Creación de redes virtuales y subredes
Defina dos subredes de red virtual: una para el tráfico front-end y otra para el tráfico back-end. Cree la red virtual con estas subredes:

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## Creación de varias NIC
Cree dos NIC y asocie una de ellas a la subred front-end y la otra a la subred back-end:

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

Normalmente también crearía un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) o un [equilibrador de carga](../load-balancer/load-balancer-overview.md) para administrar y distribuir el tráfico entre las máquinas virtuales. En el [artículo más detallado sobre máquinas virtuales con varias NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) se proporcionan los pasos para crear un grupo de seguridad de red y asignar NIC.

## Creación de la máquina virtual
Comience ahora a compilar la configuración de la máquina virtual. El tamaño de cada máquina virtual tiene un límite en cuanto al número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Windows](virtual-machines-windows-sizes.md). En el ejemplo siguiente se utiliza un tamaño de máquina virtual que admite hasta dos NIC (`Standard_DS2_v2`):

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

Conecte las dos NIC que creó anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

Configure el almacenamiento y el disco virtual para la nueva máquina virtual:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

Por último, cree una máquina virtual:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [información general de Azure Resource Manager](../resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copy*](../resource-group-create-multiple.md).

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `NIC1`, `NIC2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Pasos siguientes
Asegúrese de revisar los [tamaños de máquina virtual Windows](virtual-machines-windows-sizes.md) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual.

Recuerde que no se pueden agregar NIC adicionales a una máquina virtual existente; debe crear todas las NIC al implementar la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que dispone de toda la conectividad de red necesaria desde el principio.

<!---HONumber=AcomDC_0817_2016-->