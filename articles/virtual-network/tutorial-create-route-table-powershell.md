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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Enrutamiento del tráfico de red con una tabla de rutas mediante PowerShell

De forma predeterminada, Azure enruta automáticamente el tráfico entre todas las subredes de una red virtual. Sin embargo, puede crear sus propias rutas para invalidar las predeterminadas de Azure. La posibilidad de crear rutas personalizadas resulta de utilidad si, por ejemplo, quiere enrutar el tráfico entre subredes por medio de un firewall. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una tabla de rutas
> * Creación de una ruta
> * Asociar una tabla de rutas a una subred de red virtual
> * Probar el enrutamiento
> * Solucionar problemas de enrutamiento

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

De forma predeterminada, Azure enruta el tráfico entre todas las subredes de una red virtual. Para más información sobre las rutas predeterminadas de Azure, consulte el artículo sobre las [rutas del sistema](virtual-networks-udr-overview.md). Para invalidar el enrutamiento predeterminado de Azure, cree una tabla de rutas que contenga rutas y asóciela a una subred de la red virtual.

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

Una tabla de rutas puede contener varias rutas o ninguna. Cree una ruta mediante la recuperación del objeto de tabla de rutas con [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), cree una ruta con [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) y luego escriba la configuración de ruta para la tabla de rutas con [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

La ruta dirige todo el tráfico destinado al prefijo de dirección 10.0.1.0/24 por una aplicación virtual de red con la dirección IP 10.0.2.4. En pasos posteriores, se crean la aplicación virtual de red y la subred con el prefijo de dirección especificado. La ruta invalida el enrutamiento predeterminado de Azure, que enruta el tráfico directamente entre subredes. Cada ruta especifica un tipo de próximo salto. El tipo de próximo salto indica a Azure cómo enrutar el tráfico. En este ejemplo, el tipo de próximo salto es *VirtualAppliance*. Para más información sobre todos los tipos de próximo salto disponibles en Azure y cuándo usarlos, consulte [aquí](virtual-networks-udr-overview.md#custom-routes).

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

Los prefijos de dirección deben estar dentro del prefijo de dirección definido para la red virtual. Los prefijos de dirección de subred no pueden superponerse entre sí.

Escriba las configuraciones de subred en la red virtual con el cmdlet [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea las subredes de la red virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Puede asociar una tabla de rutas a varias subredes o a ninguna. Una subred puede tener una tabla de ruta asociada a ella o ninguna. El tráfico saliente de una subred se enruta en función de las rutas predeterminadas de Azure y de cualquier ruta que haya agregado a una tabla de rutas que asocie a una subred. Asocie la tabla de rutas *myRouteTablePublic* a la subred *Pública* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) y, luego, escriba la configuración de subred para la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Antes de implementar tablas de rutas para su uso en producción, se recomienda familiarizarse bien con el [enrutamiento en Azure](virtual-networks-udr-overview.md) y los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Probar el enrutamiento

Para probar el enrutamiento, creará una máquina virtual que actúe como aplicación virtual de red mediante la que se enruta la ruta que creó en un paso anterior. Después de crear la aplicación virtual de red, implementará una máquina virtual en las subredes *Pública* y *Privada*. A continuación, enrutará el tráfico de la subred *Pública* a la subred *Privada* mediante la aplicación virtual de red.

### <a name="create-a-network-virtual-appliance"></a>Creación de una aplicación virtual de red

Una máquina virtual que se ejecuta en una aplicación de red se conoce a menudo como aplicación virtual de red. Las aplicaciones virtuales de red normalmente reciben el tráfico de red, realizan alguna acción y luego reenvían o descartan dicho tráfico en función de la lógica configurada en la aplicación de red. 

#### <a name="create-a-network-interface"></a>Crear una interfaz de red

En un paso anterior, creó una ruta que especificaba una aplicación virtual de red como el tipo de próximo salto. Una máquina virtual que se ejecuta en una aplicación de red se conoce a menudo como aplicación virtual de red. En entornos de producción, la aplicación virtual de red se suele implementar en una máquina virtual configurada previamente. Hay varias aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En este artículo, se crea una máquina virtual básica.

Una máquina virtual tiene asociadas una o varias interfaces de red que permiten que la máquina virtual se comunique con la red. Para que una interfaz de red pueda reenviar el tráfico de red recibido, que no está destinado a su propia dirección IP, debe tener habilitado el reenvío IP. Antes de crear una interfaz de red, tiene que recuperar el identificador de red virtual con [Get AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) y luego el identificador de subred con [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Cree una interfaz de red con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) en la subred *DMZ* con el reenvío IP habilitado:

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

#### <a name="create-a-virtual-machine"></a>de una máquina virtual

Para crear una máquina virtual y asociarle una interfaz de red existente, primero debe crear una configuración de máquina virtual con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configuración incluye la interfaz de red que creó en el paso anterior. Cuando se le pida un nombre de usuario y una contraseña, seleccione el nombre de usuario y la contraseña con los que quiere iniciar sesión en la máquina virtual. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
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

Cree la máquina virtual mediante la configuración de máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual denominada *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

La opción `-AsJob` crea la máquina virtual en segundo plano para que pueda realizar el siguiente paso. Cuando se le solicite, escriba el nombre de usuario y la contraseña con los que quiere iniciar sesión en la máquina virtual. En entornos de producción, la aplicación virtual de red se suele implementar en una máquina virtual configurada previamente. Hay varias aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure asignó 10.0.2.4 como dirección IP privada de la máquina virtual, porque 10.0.2.4 es la primera dirección IP disponible en la subred *DMZ* de *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual para que pueda validar que el tráfico que procede de la subred *Pública* se enruta a la subred *Privada* mediante la aplicación virtual de red de un paso posterior. 

Cree una máquina virtual en la subred *Pública* con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual llamada *myVmWeb* en la subred *Public* de la red virtual *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure asignó 10.0.0.4 como dirección IP privada de la máquina virtual, porque 10.0.1.4 es la primera dirección IP disponible en la subred *Pública* de *myVirtualNetwork*.

Cree una máquina virtual en la subred *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

La creación de la máquina virtual tarda algunos minutos. Azure asignó 10.0.1.4 como dirección IP privada de la máquina virtual, porque 10.0.1.4 es la primera dirección IP disponible en la subred *Privada* de *myVirtualNetwork*. 

No continúe con el paso siguiente hasta que se cree la máquina virtual y Azure devuelva la salida a PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Redirigir el tráfico a través de una aplicación virtual de red

Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para permitir la conexión entrante de ICMP a las máquinas virtuales *myVmWeb* y *myVmMgmt* mediante el Firewall de Windows para usar tracert para probar la comunicación entre las máquinas virtuales en un paso posterior:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Los comandos anteriores pueden tardar unos minutos en completarse. No continúe con el paso siguiente hasta que los comandos hayan terminado de ejecutarse y se devuelva una salida a PowerShell. Aunque en este artículo se usa tracert para probar el enrutamiento, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones en producción.

Se puede conectar a la dirección IP pública de una máquina virtual desde Internet. Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de Escritorio remoto con la máquina virtual *myVmMgmt*. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

A continuación se crea, se descarga y se abre en el equipo un archivo de Protocolo de Escritorio remoto (.rdp). Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión. 

El reenvío IP se habilitó dentro de Azure para la interfaz de red de la máquina virtual en [Habilitación del reenvío IP](#enable-ip-forwarding). Dentro de la máquina virtual, el sistema operativo o una aplicación que se ejecuta dentro de la máquina virtual, también debe poderse reenviar el tráfico de red. Al implementar una aplicación virtual de red en un entorno de producción, la aplicación normalmente filtra, registra o realiza alguna otra función antes de reenviar el tráfico. Sin embargo, en este artículo el sistema operativo simplemente reenvía todo el tráfico que recibe. Habilite el reenvío IP en el sistema operativo de *myVmNva*:

En un símbolo del sistema de la máquina virtual *myVmMgmt*, establezca una conexión de Escritorio remoto a la máquina virtual *myVmNva*:

``` 
mstsc /v:myVmNva
```
    
Para habilitar el reenvío IP en el sistema operativo de la máquina virtual *myVmNva*, escriba el siguiente comando de PowerShell en la máquina virtual *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Reinicie la máquina virtual *myVmNva*, que también desconecta la sesión de Escritorio remoto y le deja en la sesión de Escritorio remoto que abrió a la máquina virtual *myVmMgmt*.

Después de que se reinicia la máquina virtual *myVmNva*, use el siguiente comando para probar el enrutamiento del tráfico de red a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt*.

```
tracert myvmweb
```

La respuesta es similar al siguiente ejemplo:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Puede ver que el tráfico se enruta directamente desde la máquina virtual *myVmMgmt* hasta la máquina virtual *myVmWeb*. Las rutas predeterminadas de Azure enrutan el tráfico entre las subredes.

Use el comando siguiente para conectarse a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt* mediante una conexión de Escritorio remoto:

``` 
mstsc /v:myVmWeb
```

Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*, escriba el siguiente comando en un símbolo del sistema:

```
tracert myvmmgmt
```

La respuesta es similar al siguiente ejemplo:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Puede ver que el primer salto es 10.0.2.4, que es la dirección IP privada de la aplicación virtual de red. El segundo salto es 10.0.1.4, la dirección IP privada de la máquina virtual *myVmMgmt*. La ruta agregada a la tabla de rutas *myRouteTablePublic* y asociada a la subred *Pública* hizo que Azure enrutara el tráfico mediante la NVA, en lugar de a la subred *Privada* directamente.

Cierre las sesiones de Escritorio remoto a ambas máquinas virtuales, *myVmWeb* y *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de enrutamiento

Como ha aprendido en pasos anteriores, Azure aplica las rutas predeterminadas, que puede, opcionalmente, invalidar con las suyas propias. En ocasiones, puede que el tráfico no se enrute de la forma esperada. Use [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) para habilitar una instancia de Network Watcher en la región de EastUS, si aún no tiene una en esa región:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Use [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) para determinar cómo se enruta el tráfico entre dos máquinas virtuales. Por ejemplo, el comando siguiente comprueba el enrutamiento del tráfico desde la máquina virtual *myVmWeb* (10.0.0.4) hasta la máquina virtual *myVmMgmt* (10.0.1.4):

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
Tras una breve espera, se devuelve la siguiente salida:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

La salida le informa que la dirección IP del próximo salto para el tráfico que va desde *myVmWeb* hasta *myVmMgmt* es 10.0.2.4 (la máquina virtual *myVmNva*), que el tipo de próximo salto es *VirtualAppliance* y que la tabla de rutas que da lugar al enrutamiento es *myRouteTablePublic*.

Las rutas eficaces para cada interfaz de red son una combinación de las rutas predeterminadas de Azure y las rutas que defina. Para ver todas las rutas eficaces para una interfaz de red en una máquina virtual, use [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Por ejemplo, para mostrar las rutas eficaces para la interfaz de red *myVmWeb* de la máquina virtual *myVmWeb*, escriba el siguiente comando:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Se devuelven todas las rutas predeterminadas y la ruta que agregó en un paso anterior.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contenga.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, creó una tabla de rutas y la asoció a una subred. Creó una aplicación virtual de red que enrutó el tráfico desde una subred pública hasta una subred privada. Aunque puede implementar muchos recursos de Azure en una red virtual, no es el caso de los recursos de algunos servicios de PaaS de Azure. Pero puede restringir el acceso a los recursos de algunos servicios de PaaS de Azure solo al tráfico que procede de una subred de una red virtual. Avance al siguiente artículo para aprender a restringir el acceso de red a los recursos de PaaS de Azure.

> [!div class="nextstepaction"]
> [Restringir el acceso de red a los recursos de PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)