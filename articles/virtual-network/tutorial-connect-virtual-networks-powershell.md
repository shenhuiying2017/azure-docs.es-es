---
title: 'Conexión de redes virtuales con emparejamiento de redes virtuales: PowerShell | Microsoft Docs'
description: Aprenda a conectar redes virtuales con el emparejamiento de redes virtuales.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conexión de redes virtuales con emparejamiento de redes virtuales usando PowerShell

Puede conectar redes virtuales entre sí con el emparejamiento de redes virtuales. Una vez que las redes virtuales están emparejadas, los recursos de ambas se pueden comunicar entre sí con el mismo ancho de banda y la misma latencia que si estuvieran en la misma red virtual. En este artículo se describe la creación y el emparejamiento de dos redes virtuales. Aprenderá a:

> [!div class="checklist"]
> * Crear dos redes virtuales
> * Crear un emparejamiento entre redes virtuales
> * Realizar un emparejamiento de prueba

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 3.6 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-virtual-networks"></a>Creación de redes virtuales

Antes de crear una red virtual, cree un grupo de recursos para ella y los demás recursos que se crearon en este artículo. Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada *myVirtualNetwork1* con el prefijo de dirección *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). En el ejemplo siguiente se crea una configuración de subred con un prefijo de dirección 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Escriba la configuración de subred en la red virtual con [ASet-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea la siguiente subred:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Cree una red virtual con un prefijo de dirección 10.1.0.0/16 y una subred:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

El prefijo de dirección para la red virtual *myVirtualNetwork2* no se superpone con el prefijo de dirección de la red virtual *myVirtualNetwork1*. No se pueden emparejar redes virtuales con prefijos de direcciones superpuestos.

## <a name="peer-virtual-networks"></a>Emparejamiento de redes virtuales

Crear un emparejamiento con [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). El siguiente ejemplo empareja *myVirtualNetwork1* con *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

En la salida que se devuelve al ejecutarse el comando anterior, verá que **PeeringState** está en estado *Iniciado*. El emparejamiento permanece en estado *Iniciado* hasta que cree el emparejamiento de *myVirtualNetwork2* con *myVirtualNetwork1*. Cree un emparejamiento de *myVirtualNetwork2* con *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

En la salida que se devuelve al ejecutarse el comando anterior, verá que **PeeringState** está en estado *Conectado*. Azure también cambia el estado del emparejamiento *myVirtualNetwork1-myVirtualNetwork2* a *Conectado*. Confirme que el estado del emparejamiento *myVirtualNetwork1-myVirtualNetwork2* cambia a *Conectado* con [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Los recursos de una red virtual no se comunican con los de la otra hasta que el estado **PeeringState** de los emparejamientos de ambas redes virtuales es *Conectado*. 

Los emparejamientos se realizan entre dos redes virtuales, pero no son transitivos. Así, por ejemplo, si también desea emparejar *myVirtualNetwork2* con *myVirtualNetwork3*, tendrá que crear un emparejamiento adicional entre las redes virtuales *myVirtualNetwork2* y *myVirtualNetwork3*. Aunque *myVirtualNetwork1* esté emparejada con *myVirtualNetwork2*, los recursos de *myVirtualNetwork1* solo pueden acceder a los recursos de  *myVirtualNetwork3* si *myVirtualNetwork1* también se empareja con *myVirtualNetwork3*. 

Antes de emparejar redes virtuales de producción, se recomienda familiarizarse en detalle con la [introducción al emparejamiento](virtual-network-peering-overview.md), la [administración del emparejamiento](virtual-network-manage-peering.md) y los [límites de red virtual ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Aunque en este artículo se explica un emparejamiento entre dos redes virtuales en la misma suscripción y ubicación, también puede emparejar redes virtuales en [diferentes regiones](#register) y [diferentes suscripciones de Azure](create-peering-different-subscriptions.md#powershell). También puede crear [diseños de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con emparejamiento.

## <a name="test-peering"></a>Realizar un emparejamiento de prueba

Para probar la comunicación de red entre las máquinas virtuales en diferentes redes virtuales mediante un emparejamiento, implemente una máquina virtual en cada subred y establezca la comunicación entre ellas. 

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree una máquina virtual en cada red virtual para poder validar la comunicación entre ellas posteriormente.

Cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual llamada *myVm1* en la red virtual *myVirtualNetwork1*. La opción `-AsJob` crea la máquina virtual en segundo plano para que pueda realizar el siguiente paso. Cuando se le solicite, escriba el nombre de usuario y la contraseña con los que quiere iniciar sesión en la máquina virtual.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure asigna automáticamente 10.0.0.4 como dirección IP privada de la máquina virtual, porque 10.0.0.4 es la primera dirección IP disponible en *Subnet1* de *myVirtualNetwork1*. 

Cree una máquina virtual en la red virtual *myVirtualNetwork2*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

La creación de la máquina virtual tarda algunos minutos. Aunque no se muestra en la salida devuelta, Azure asignó 10.1.0.4 como dirección IP privada de la máquina virtual, porque 10.1.0.4 es la primera dirección IP disponible en *Subnet1* de *myVirtualNetwork2*. 

No continúe con los pasos siguientes hasta que Azure cree la máquina virtual y devuelve el resultado a PowerShell.

### <a name="test-virtual-machine-communication"></a>Prueba de comunicación de la máquina virtual

Puede conectarse a la dirección IP pública de una máquina virtual desde Internet. Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Ejecute el comando siguiente en el equipo local para crear una sesión de Escritorio remoto con la máquina virtual *myVm1*. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

A continuación se crea, se descarga y se abre en el equipo un archivo de Protocolo de Escritorio remoto (.rdp). Escriba el nombre de usuario y la contraseña (puede que tenga que seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o **Conectar** para continuar con la conexión.

Desde un símbolo del sistema, habilite ping mediante el Firewall de Windows, para que pueda hacer ping en esta máquina virtual desde *myVm2* en un paso posterior.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Aunque en este artículo se usa ping para hacer una prueba, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones de producción.

Para conectar con la máquina virtual *myVm2*, escriba el siguiente comando desde el símbolo del sistema en la máquina virtual *myVm1*:

```
mstsc /v:10.1.0.4
```

Como ha habilitado ping en *myVm1*, ahora puede hacer ping en él mediante una dirección IP desde un símbolo del sistema en la máquina virtual *myVm2* :

```
ping 10.0.0.4
```

Recibirá cuatro respuestas. Si hace ping por el nombre de la máquina virtual (*myVm1*), en lugar de su dirección IP, el ping falla, porque *myVm1* es un nombre de host desconocido. La resolución de nombres predeterminada de Azure funciona entre máquinas virtuales de la misma red virtual, pero no entre máquinas virtuales de diferentes redes virtuales. Para resolver nombres en varias redes virtuales, tiene que [implementar su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) o usar [dominios privados de Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Desconecte las sesiones RDP para ambos *myVm1* y *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contenga.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Registro para la versión preliminar del emparejamiento de red virtual global**

El emparejamiento de redes virtuales en las mismas regiones tiene disponibilidad general. El emparejamiento de redes virtuales de diferentes regiones actualmente se encuentra en versión preliminar. Consulte las [actualizaciones de redes virtuales](https://azure.microsoft.com/updates/?product=virtual-network) para ver las regiones disponibles. Para emparejar redes virtuales entre regiones, primero tiene que registrarse en la versión preliminar; para ello, complete los pasos siguientes (en la suscripción en que se encuentra cada red virtual que desea emparejar):

1. Registre la suscripción en que se encuentra cada máquina virtual que desea emparejar para la versión preliminar; para ello, escriba los comandos siguientes:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Si intenta emparejar redes virtuales en diferentes regiones antes de que la salida **RegistrationState** que recibe después de escribir el comando anterior sea **Registrado** para ambas suscripciones, el emparejamiento produce un error.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a conectar dos redes con el emparejamiento de redes virtuales. Puede [conectar su equipo a una red virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mediante VPN e interactuar con recursos en una red virtual, o en redes virtuales emparejadas.

Siga con los ejemplos de scripts reutilizables para completar muchas de las tareas descritas en los artículos sobre las redes virtuales.

> [!div class="nextstepaction"]
> [Ejemplos de scripts de red virtual](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
