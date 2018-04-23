---
title: 'Tutorial: Filtrado del tráfico de red en Azure PowerShell | Microsoft Docs'
description: En este tutorial, aprenderá a filtrar el tráfico de red que se dirige a una subred, con un grupo de seguridad de red, mediante PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 63dfa89af64e500e8ed0292ab282150636e57ab3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>Tutorial: Filtrado del tráfico de red con un grupo de seguridad de red mediante PowerShell

Puede filtrar el tráfico de red entrante y saliente de una subred de una red virtual con un grupo de seguridad de red. Los grupos de seguridad de red contienen reglas de seguridad que filtran el tráfico de red por dirección IP, puerto y protocolo. Las reglas de seguridad se aplican a los recursos implementados en una subred. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de seguridad de red y reglas de seguridad
> * Crear una red virtual y asociar un grupo de seguridad de red a una subred
> * Implementar máquinas virtuales (VM) en una subred
> * Probar los filtros de tráfico

Si lo prefiere, puede realizar los pasos de este artículo con la [CLI de Azure](tutorial-filter-network-traffic-cli.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, en este tutorial necesitará la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

Un grupo de seguridad de red contiene reglas de seguridad. Las reglas de seguridad especifican un origen y destino. Los orígenes y destinos pueden ser grupos de seguridad de aplicaciones.

### <a name="create-application-security-groups"></a>Creación de grupos de seguridad de aplicaciones

En primer lugar, cree un grupo de recursos para todos los recursos creados en este tutorial con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente se crea un grupo de recursos en la ubicación *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Cree un grupo de seguridad de aplicaciones con [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Un grupo de seguridad de aplicaciones permite agrupar servidores con requisitos de filtrado de puertos similar. El ejemplo siguiente crea dos grupos de seguridad de aplicaciones.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Creación de reglas de seguridad

Cree una regla de seguridad con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). En el ejemplo siguiente se crea una regla que permite el tráfico entrante desde Internet al grupo de seguridad de aplicaciones *myWebServers* a través de los puertos 80 y 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

En este tutorial, se expone RDP (puerto 3389) a Internet para la máquina virtual *myAsgMgmtServers*. Para entornos de producción, en lugar de exponer el puerto 3389 a Internet, se recomienda conectarse a los recursos de Azure que desea administrar mediante una [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o una conexión de red [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

Cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). En el ejemplo siguiente se crea un grupo de seguridad de red llamado *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Crear una red virtual

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual llamada *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) y, a continuación, escriba la configuración de subred en la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). En el ejemplo siguiente se agrega una subred llamada *mySubnet* a la red virtual y se asocia el grupo de seguridad de red *myNsg* a dicha subred:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Antes de crear las máquinas virtuales, recupere el objeto de red virtual con la subred con [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Cree una dirección IP pública para cada máquina virtual con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Cree dos interfaces de red con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) y asigne una dirección IP pública a la interfaz de red. En el ejemplo siguiente se crea una interfaz de red, se asocia la dirección IP pública *myVmWeb* y se convierte en un miembro del grupo de seguridad de aplicaciones *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

En el ejemplo siguiente se crea una interfaz de red, se asocia la dirección IP pública *myVmMgmt* y se convierte en un miembro del grupo de seguridad de aplicaciones *myAsgWebServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Cree dos máquinas virtuales en la red virtual para que pueda validar el filtrado de tráfico en un paso posterior. 

Cree una configuración de máquina virtual con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) y, a continuación, cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crea una máquina virtual que actúa como un servidor web. La opción `-AsJob` crea la máquina virtual en segundo plano, por lo tanto, puede continuar con el siguiente paso: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Cree una máquina virtual para que actúe como un servidor de administración:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

La creación de la máquina virtual tarda algunos minutos. No continúe con el paso siguiente hasta que Azure finalice la creación de la máquina virtual.

## <a name="test-traffic-filters"></a>Probar los filtros de tráfico

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use el comando siguiente para crear una sesión de Escritorio remoto con la máquina virtual *myVmMgmt* desde su equipo local. Reemplace `<publicIpAddress>` con la dirección IP que devolvió el comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.

Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **Más opciones** y luego **Usar una cuenta diferente**, para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión. 
   
La conexión se realiza correctamente porque el puerto 3389 tiene permitida la entrada desde Internet al grupo de seguridad de aplicaciones *myAsgMgmtServers* en el que se encuentra la interfaz de red conectada a la máquina virtual *myVmMgmt*.

Use el siguiente comando para crear una conexión de escritorio remoto con la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt* desde PowerShell:

``` 
mstsc /v:myvmWeb
```

La conexión se realiza correctamente porque una regla de seguridad predeterminada dentro de cada grupo de seguridad de red permite el tráfico a través de todos los puertos entre todas las direcciones IP de una red virtual. No es posible crear una conexión de escritorio remoto con la máquina virtual *myVmWeb* desde Internet porque la regla de seguridad para *myAsgWebServers* no permite el puerto 3389 de entrada desde Internet.

Use el siguiente comando para instalar Microsoft IIS en la máquina virtual *myVmWeb* desde PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Una vez completada la instalación de IIS, desconecte la sesión de la máquina virtual *myVmWeb*, lo que le deja en la conexión de escritorio remoto de la máquina virtual *myVmMgmt*. Para ver la pantalla de bienvenida de IIS, abra un explorador de Internet y vaya a http://myVmWeb.

Desconéctese de la máquina virtual *myVmMgmt*.

En su equipo, escriba el siguiente comando desde PowerShell para recuperar la dirección IP pública del servidor *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Para confirmar que tiene acceso al servidor web *myVmWeb* desde fuera de Azure, abra un explorador de Internet en el equipo y vaya a `http://<public-ip-address-from-previous-step>`. La conexión se realiza correctamente porque el puerto 80 tiene permitida la entrada desde Internet al grupo de seguridad de aplicaciones *myAsgMgmtServers* en el que se encuentra la interfaz de red conectada a la máquina virtual *myVmWeb*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un grupo de seguridad de red y lo ha asociado a una subred de una red virtual. Para más información acerca de los grupos de seguridad de red, consulte [Introducción a los grupos de seguridad de red](security-overview.md) y [Administración de un grupo de seguridad de red](manage-network-security-group.md).

De forma predeterminada, Azure enruta el tráfico entre subredes. En su lugar, puede elegir enrutar el tráfico entre subredes a través de una máquina virtual, que actúa como un firewall, por ejemplo. Para aprender a crear una tabla de rutas, avance al siguiente tutorial.

> [!div class="nextstepaction"]
> [Creación de una tabla de rutas](./tutorial-create-route-table-portal.md)
