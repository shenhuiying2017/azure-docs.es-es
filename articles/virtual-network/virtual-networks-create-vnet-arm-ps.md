---
title: Creación de una red virtual de Azure con varias subredes en PowerShell | Microsoft Docs
description: Aprenda a crear una red virtual con varias subredes mediante PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Creación de una red virtual con varias subredes mediante PowerShell

Una red virtual permite que varios tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. La creación de varias subredes en una red virtual permite segmentar la red para filtrar o controlar el flujo de tráfico entre las subredes. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual
> * Creación de una subred
> * Probar la comunicación de red entre máquinas virtuales

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para este tutorial necesitará la versión 3.6 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-a-virtual-network"></a>Crear una red virtual

Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork* con el prefijo de dirección *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

El valor de **AddressPrefix** es especifica en notación de CIDR. El prefijo de dirección especificado incluye las direcciones IP 10.0.0.0-10.0.255.254.

## <a name="create-a-subnet"></a>Creación de una subred

Para crear una subred primero se crea una configuración de subred y luego se actualiza la red virtual con esta configuración. Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). En el ejemplo siguiente se crean dos configuraciones de subred para las subredes *Public* y *Private*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

El valor de **AddressPrefix** especificado para una subred debe estar dentro del prefijo de dirección definido para la red virtual. Azure DHCP asigna direcciones IP a partir de un prefijo de dirección de subred a los recursos implementados en una subred. Azure solo asigna las direcciones 10.0.0.4-10.0.0.254 a los recursos implementados en la subred **Pública**, porque Azure reserva las cuatro primeras direcciones (10.0.0.0-10.0.0.3 para la subred, en este ejemplo) y la última dirección (10.0.0.255 para la subred, en este ejemplo) en cada subred.

Escriba las configuraciones de subred en la red virtual con el cmdlet [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea las subredes de la red virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Antes de implementar las redes virtuales de Azure y las subredes para usarlas en producción, se recomienda que conozca todo lo que hay que tener en cuenta sobre el [espacio de direcciones](manage-virtual-network.md#create-a-virtual-network) y los [límites de red virtual](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Una vez implementados los recursos en las subredes, puede que algunos cambios en la red virtual y la subred, como el de los intervalos de direcciones, requieran la reimplementación de los recursos de Azure en las subredes.

## <a name="test-network-communication"></a>Prueba de la comunicación de red

Una red virtual permite que varios tipos de recursos de Azure se comuniquen en Internet y entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual de modo que pueda probar la comunicación de red entre ellas e Internet en un paso posterior. 

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual llamada *myVmWeb* en la subred *Public* de la red virtual *myVirtualNetwork*. La opción `-AsJob` crea la máquina virtual en segundo plano para que pueda realizar el siguiente paso. Cuando se le solicite, escriba el nombre de usuario y la contraseña con los que quiere iniciar sesión en la máquina virtual.

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

Azure asignó automáticamente 10.0.0.4 como dirección IP privada de la máquina virtual, porque 10.0.0.4 es la primera dirección IP disponible en la subred *Public*. 

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

La creación de la máquina virtual tarda algunos minutos. Aunque no se muestra en la salida devuelta, Azure asignó 10.0.1.4 como dirección IP privada de la máquina virtual, porque 10.0.1.4 es la primera dirección IP disponible en la subred *Private* de *myVirtualNetwork*. 

No continúe con los pasos restantes hasta que la máquina virtual se haya creado y PowerShell devuelva la salida.

Las máquinas virtuales creadas en este artículo tiene una [interfaz de red](virtual-network-network-interface.md) con una dirección IP que se asigna a dicha interfaz dinámicamente. Después de implementar la máquina virtual, puede [agregar varias direcciones IP públicas y privadas o cambiar el método de asignación de direcciones IP a estático](virtual-network-network-interface-addresses.md#add-ip-addresses). También puede [agregar interfaces de red](virtual-network-network-interface-vm.md#vm-add-nic), hasta el límite admitido por el [tamaño de máquina virtual](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seleccionó al crear una máquina virtual. Además, puede [habilitar la virtualización de E/S de raíz única (SR-IOV)](create-vm-accelerated-networking-powershell.md) en una máquina virtual, pero solo al crear una máquina virtual con un tamaño que admita la funcionalidad.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Comunicación entre máquinas virtuales y con Internet

Puede conectarse a la dirección IP pública de una máquina virtual desde Internet. Cuando Azure creó la máquina virtual *myVmMgmt*, también se creó una dirección IP pública llamada *myVmMgmt* y se asignó a la máquina virtual. Aunque no es necesario que una máquina virtual tenga asignada una dirección IP pública, Azure asigna una a cada máquina virtual creada, de forma predeterminada. Para comunicarse con una máquina virtual desde Internet, se debe asignar una dirección IP pública a la máquina virtual. Todas las máquinas virtuales pueden establecer una comunicación saliente con Internet, tanto si se les asigna una dirección IP pública como si no. Para más información sobre las conexiones a Internet salientes en Azure, consulte [Conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de Escritorio remoto con la máquina virtual *myVmMgmt*. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

A continuación se crea, se descarga y se abre en el equipo un archivo de Protocolo de Escritorio remoto (.rdp). Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, haga clic en **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión. 

En un paso posterior, se usa ping para comunicarse con la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*. Ping usa ICMP, que, de forma predeterminada, se deniega a través del Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde un símbolo del sistema:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Aunque en este artículo se usa ping, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones de producción.

Por motivos de seguridad, es habitual limitar el número de máquinas virtuales a las que se pueden conectar de forma remota en una red virtual. En este tutorial, la máquina virtual *myVmMgmt* se usa para administrar la máquina virtual *myVmWeb* en la red virtual. Use el comando siguiente para conectarse a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt* mediante una conexión de Escritorio remoto:

``` 
mstsc /v:myVmWeb
```

Para comunicarse con la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*, escriba el siguiente comando desde un símbolo del sistema:

```
ping myvmmgmt
```

Recibirá una salida similar al ejemplo siguiente:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Puede ver que la dirección de la máquina virtual *myVmMgmt* es 10.0.1.4. 10.0.1.4 era la primera dirección IP disponible en el intervalo de direcciones de la subred *Private* que implementó la máquina virtual *myVmMgmt* en un paso anterior.  Verá que el nombre de dominio completo de la máquina virtual es *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Aunque la parte *dar5p44cif3ulfq00wxznl3i3f* del nombre de dominio es diferente en su máquina virtual, el resto es igual. De forma predeterminada, todas las máquinas virtuales de Azure usan el servicio Azure DNS predeterminado. Todas las máquinas virtuales de una red virtual pueden resolver los nombres de las demás máquinas virtuales de la misma red virtual mediante el servicio Azure DNS predeterminado. En lugar de usar el servicio DNS predeterminado de Azure, puede usar su propio servidor DNS o la funcionalidad de dominio privado del servicio Azure DNS. Para más información, consulte [Resolución de nombre mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) o [Uso de Azure DNS para dominios privados](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para instalar Internet Information Services (IIS) para Windows Server en la máquina virtual *myVmWeb*, escriba el siguiente comando desde una sesión de PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Una vez completada la instalación de IIS, desconecte la sesión de Escritorio remoto de *myVmWeb*, lo cual le deja en la sesión de Escritorio remoto de *myVmMgmt*. Abra un explorador web y vaya a http://myvmweb. Verá la página principal de IIS.

Desconecte la sesión de Escritorio remoto de *myVmMgmt*.

Obtenga la dirección pública que Azure asignó a la máquina virtual *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

En su propio equipo, vaya a la dirección IP pública de la máquina virtual *myVmWeb*. Se produce un error al intentar ver la página de bienvenida de IIS desde su propio equipo. El motivo es que cuando se implementaron las máquinas virtuales, Azure creó un grupo de seguridad de red para cada máquina virtual, de forma predeterminada. 

Un grupo de seguridad de red contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante y saliente por dirección IP y puerto. El grupo de seguridad de red predeterminado que creó Azure permite la comunicación a través de todos los puertos entre los recursos de la misma red virtual. En las máquinas virtuales Windows, el grupo de seguridad de red predeterminado deniega todo el tráfico entrante desde Internet a través de todos los puertos y acepta el puerto TCP 3389 (RDP). Como resultado, de forma predeterminada, también puede conectarse directamente a la máquina virtual *myVmWeb* mediante RDP desde Internet, aunque puede que no quiera que el puerto 3389 se abra a un servidor web. Dado que la comunicación en la exploración web tiene lugar en el puerto 80, se produce un error en la comunicación desde Internet porque no hay ninguna regla en el grupo de seguridad de red predeterminado que permita el tráfico en ese puerto.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contenga.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a implementar una red virtual con varias subredes. También aprendió que, al crear una máquina virtual Windows, Azure crea una interfaz de red que se asocia a la máquina virtual, y crea un grupo de seguridad de red que solo permite el tráfico en el puerto 3389, desde Internet. Avance al siguiente tutorial para aprender a filtrar el tráfico de red que va a las subredes, en lugar de a máquinas virtuales individuales.

> [!div class="nextstepaction"]
> [Filtrado del tráfico de red que va a las subredes](./virtual-networks-create-nsg-arm-ps.md)
