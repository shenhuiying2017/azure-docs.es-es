---
title: Enrutamiento del tráfico de red en PowerShell | Microsoft Docs
description: Aprenda a enrutar el tráfico de red con una tabla de rutas mediante PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 49c7b6158beee9d47ecd224e6a0750310d2b68c0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Enrutamiento del tráfico de red con una tabla de rutas mediante PowerShell

De forma predeterminada, Azure enruta automáticamente el tráfico entre todas las subredes de una red virtual. Sin embargo, puede crear sus propias rutas para invalidar las predeterminadas de Azure. La posibilidad de crear rutas personalizadas resulta de utilidad si, por ejemplo, quiere enrutar el tráfico entre subredes por medio de una aplicación virtual de red. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una tabla de rutas
> * Creación de una ruta
> * Creación de una red virtual con varias subredes
> * Asociación de una tabla de rutas a una subred
> * Creación de una aplicación virtual de red para enrutar el tráfico
> * Implementación de máquinas virtuales en subredes diferentes
> * Enrutamiento del tráfico desde una subred a otra a través de una aplicación virtual de red

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

Antes de poder crear una tabla de rutas, debe crear un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente se crea un grupo de recursos denominado *myResourceGroup* para todos los recursos creados en este artículo. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Cree una tabla de rutas con [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). En el ejemplo siguiente se crea una tabla de rutas denominada *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Creación de una ruta

Cree una ruta mediante la recuperación del objeto de tabla de rutas con [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), cree una ruta con [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) y luego escriba la configuración de ruta para la tabla de rutas con [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Asociación de una tabla de rutas a una subred

Para poder asociar una tabla de rutas a una subred, debe crear una red virtual y una subred. Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork* con el prefijo de dirección *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Cree tres subredes mediante la creación de tres configuraciones de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). En el ejemplo siguiente se crean tres configuraciones de subred para las subredes *Pública*, *Privada* y *DMZ*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Escriba las configuraciones de subred en la red virtual con el cmdlet [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea las subredes de la red virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Asocie la tabla de rutas *myRouteTablePublic* a la subred *Pública* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) y, luego, escriba la configuración de subred para la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Creación de una aplicación virtual de red

Una aplicación virtual de red es una máquina virtual que realiza una función de red, como el enrutamiento, el firewall o la optimización de la WAN.

Antes de crear una máquina virtual, cree una interfaz de red.

### <a name="create-a-network-interface"></a>Crear una interfaz de red

Antes de crear una interfaz de red, tiene que recuperar el identificador de red virtual con [Get AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) y luego el identificador de subred con [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Cree una interfaz de red con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) en la subred *DMZ* con el reenvío IP habilitado:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Crear una VM

Para crear una máquina virtual y asociarle una interfaz de red existente, primero debe crear una configuración de máquina virtual con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configuración incluye la interfaz de red que creó en el paso anterior. Cuando se le pida un nombre de usuario y una contraseña, seleccione el nombre de usuario y la contraseña con los que quiere iniciar sesión en la máquina virtual. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Cree la máquina virtual con la configuración de la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual denominada *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

La opción `-AsJob` crea la máquina virtual en segundo plano, así que puede continuar con el siguiente paso.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual para que pueda validar que el tráfico que procede de la subred *Pública* se enruta a la subred *Privada* mediante la aplicación virtual de red de un paso posterior. 

Cree una máquina virtual en la subred *Pública* con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual llamada *myVmPublic* en la subred *Public* de la red virtual *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Cree una máquina virtual en la subred *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

La máquina virtual tarda en crearse unos minutos. No continúe con el paso siguiente hasta que se cree la máquina virtual y Azure devuelva la salida a PowerShell.

## <a name="route-traffic-through-an-nva"></a>Enrutamiento del tráfico a través de una aplicación virtual de red

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de la máquina virtual *myVmPrivate*. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVmPrivate*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de Escritorio remoto con la máquina virtual *myVmPrivate*. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.

Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión. 

En el último paso, se usa el comando tracert.exe para probar el enrutamiento. Tracert usa el Protocolo de mensajes de control de Internet (ICMP), que se deniega a través del Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Aunque en este artículo se usa tracert para probar el enrutamiento, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones en producción.

Habilite el reenvío IP dentro del sistema operativo de la máquina virtual *myVmNva* siguiendo estos pasos en la máquina virtual *myVmPrivate*:

Realice una conexión de Escritorio remoto a la máquina virtual *myVmNva* con el siguiente comando desde PowerShell:

``` 
mstsc /v:myvmnva
```
    
Para habilitar el reenvío IP dentro del sistema operativo, escriba el siguiente comando en PowerShell:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Reinicie la máquina virtual, que también desconecta la sesión de Escritorio remoto.

Mientras permanece conectado a la máquina virtual *myVmPrivate*, después de que se reinicia la máquina virtual *myVmNva*, cree una sesión de Escritorio remoto a la máquina virtual *myVmPublic* con el siguiente comando:

``` 
mstsc /v:myVmPublic
```
    
Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmPrivate* de la máquina virtual *myVmPublic*, escriba el siguiente comando de PowerShell:

```
tracert myVmPrivate
```

La respuesta es similar al siguiente ejemplo:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Puede ver que el primer salto es 10.0.2.4, que es la dirección IP privada de la aplicación virtual de red. El segundo salto es 10.0.1.4, la dirección IP privada de la máquina virtual *myVmPrivate*. La ruta agregada a la tabla de rutas *myRouteTablePublic* y asociada a la subred *Pública* hizo que Azure enrutara el tráfico mediante la NVA, en lugar de a la subred *Privada* directamente.

Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPublic*, que aún le deja conectado a la máquina virtual *myVmPrivate*.
Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmPublic* de la máquina virtual *myVmPrivate*, escriba el siguiente comando desde un símbolo del sistema:

```
tracert myVmPublic
```

La respuesta es similar al siguiente ejemplo:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
    
Trace complete.
```

Puede ver si el tráfico se enruta directamente de la máquina virtual *myVmPrivate* a la máquina virtual *myVmPublic*. De forma predeterminada, Azure enruta el tráfico directamente entre subredes.

Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contenga.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, creó una tabla de rutas y la asoció a una subred. Creó una aplicación virtual de red sencilla que enrutó el tráfico desde una subred pública hasta una subred privada. Implemente una variedad de aplicaciones virtuales de red que realicen funciones de red, como firewall y optimización de la WAN, desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Antes de implementar tablas de rutas para su uso en producción, se recomienda familiarizarse bien con el [enrutamiento en Azure](virtual-networks-udr-overview.md), la [administración de tablas de rutas](manage-route-table.md) y los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Aunque puede implementar muchos recursos de Azure en una red virtual, no es el caso de los recursos de algunos servicios de PaaS de Azure. Pero puede restringir el acceso a los recursos de algunos servicios de PaaS de Azure solo al tráfico que procede de una subred de una red virtual. Avance al siguiente tutorial para aprender a restringir el acceso de red a los recursos de PaaS de Azure.

> [!div class="nextstepaction"]
> [Restringir el acceso de red a los recursos de PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)