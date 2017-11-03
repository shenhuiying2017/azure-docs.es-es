---
title: "Máquinas virtuales Windows y redes virtuales de Azure | Microsoft Docs"
description: "Tutorial: Administración de máquinas virtuales Windows y redes virtuales de Azure con Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/12/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 21f2d586a4c468071bec55c65005b35baf323fe7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Administración de máquinas virtuales Windows y redes virtuales de Azure con Azure PowerShell

Las máquinas virtuales de Azure utilizan las redes de Azure para la comunicación de red interna y externa. Este tutorial le guía a través de la implementación de dos máquinas virtuales y la configuración de redes de Azure para estas máquinas virtuales. Se da por supuesto que en los ejemplos de este tutorial las máquinas virtuales hospedan una aplicación web con un back-end de base de datos, sin embargo, no se implementa ninguna aplicación en el tutorial. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual y una subred
> * Crear una dirección IP pública
> * Crear una máquina virtual de front-end
> * Protegen el tráfico de red.
> * Creación de la máquina virtual de "back-end"

Al completar este tutorial, podrá ver que se crearon los siguientes recursos:

![Red virtual con dos subredes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: red virtual que las máquinas virtuales usan para comunicarse entre sí y con Internet.
- *myFrontendSubnet*: subred de *myVNet* que usan los recursos de front-end.
- *myPublicIPAddress*: dirección IP pública que se usa para tener acceso a *myFrontendVM* desde Internet.
- *myFrontentNic*: interfaz de red que usa *myFrontendVM* para comunicarse con *myBackendVM*.
- *myFrontendVM*: máquina virtual que se usa para la comunicación entre Internet y *myBackendVM*.
- *myBackendNSG*: grupo de seguridad de red que controla la comunicación entre *myFrontendVM* y *myBackendVM*.
- *myBackendSubnet*: subred asociada a *myBackendNSG* y que usan los recursos de back-end.
- *myBackendNic*: interfaz de red que usa *myBackendVM* para comunicarse con *myFrontendVM*.
- *myBackendVM*: máquina virtual que usa el puerto 1433 para comunicarse con *myFrontendVM*.

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Para encontrar la versión, ejecute `Get-Module -ListAvailable AzureRM`. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="vm-networking-overview"></a>Introducción a las redes de máquinas virtuales

Las redes virtuales de Azure habilitan las conexiones de red seguras entre máquinas virtuales, Internet y otros servicios de Azure SQL Database. Las redes virtuales se dividen en segmentos lógicos llamados subredes. Las subredes se utilizan para controlar el flujo de red y como límite de seguridad. La implementación de una máquina virtual incluye, por lo general, una interfaz de red virtual, que está conectada a una subred.

## <a name="create-a-virtual-network-and-subnet"></a>Creación de una red virtual y una subred

Para este tutorial, se crea una única red virtual con dos subredes: una subred de front-end para hospedar una aplicación web y una subred de back-end para hospedar un servidor de bases de datos.

Antes de poder crear una máquina virtual, debe crear un grupo de recursos mediante [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myRGNetwork* en la ubicación *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Crear configuraciones de subred

Cree una configuración de subred llamada *myFrontendSubnet* mediante [AzureRmVirtualNetworkSubnetConfig New](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Además, debe crear una configuración de subred llamada *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Creación de una red virtual

Cree una red virtual denominada *myVNet* mediante *myFrontendSubnet* y *myBackendSubnet* mediante [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Entonces, se ha creado una red y se ha segmentado en dos subredes, una para servicios front-end y otra para servicios back-end. En la siguiente sección, se crean máquinas virtuales y se conectan a estas subredes.

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Una dirección IP pública permite que los recursos de Azure estén accesibles en Internet. El método de asignación de la dirección IP pública se puede configurar como dinámico o estático. De forma predeterminada, la dirección IP pública se asigna dinámicamente. Las direcciones IP dinámicas se liberan al desasignar una máquina virtual. Este comportamiento hace que la dirección IP cambie durante cualquier operación que incluya una desasignación de máquina virtual.

El método de asignación se puede establecer en estático, lo que garantiza que la dirección IP siga asignada a una máquina virtual, incluso durante un estado de desasignación. Cuando se usa una dirección IP asignada estáticamente, no se puede especificar la propia dirección IP, sino que se asigna desde un grupo de direcciones disponibles.

Cree una dirección IP pública denominada *myPublicIPAddress* mediante [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Puede cambiar el parámetro -AllocationMethod a `Static`, para asignar una dirección IP pública estática.

## <a name="create-a-front-end-vm"></a>Crear una máquina virtual de front-end

Para que una máquina virtual se comunique en una red virtual, debe tener una interfaz de red virtual (NIC). Cree una NIC con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Use estas credenciales para conectarse a la máquina virtual en pasos adicionales:

```azurepowershell-interactive
$cred = Get-Credential
```

Cree las máquinas virtuales mediante [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) y [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="secure-network-traffic"></a>Protegen el tráfico de red.

Un grupo de seguridad de red (NSG) contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a redes virtuales de Azure (VNet). Los NSG se pueden asociar a subredes o a interfaces de red individuales. Cuando un NSG está asociado a una interfaz de red, se aplica solo a la máquina virtual asociada. Cuando un grupo de seguridad de red está asociado a una subred, las reglas se aplican a todos los recursos conectados a la subred.

### <a name="network-security-group-rules"></a>Reglas del grupo de seguridad de red

Las reglas de NSG definen puertos de redes a través de los que se permite o deniega el tráfico. Las reglas pueden incluir intervalos de direcciones IP de origen y destino para que el tráfico se controle entre sistemas o subredes específicos. Las reglas de NSG también incluyen una prioridad (entre 1 y 4096). Las reglas se evalúan por orden de prioridad. Una regla con una prioridad de 100 se evalúa antes que una regla con prioridad de 200.

Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas. No se pueden eliminar las reglas predeterminadas, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree.

- **Red virtual:** el tráfico que se origina y termina en una red virtual se permite en las direcciones tanto de entrada como de salida.
- **Internet:** se permite el tráfico saliente pero se bloquea el entrante.
- **Equilibrador de carga:** se permite que el equilibrador de carga de Azure sondee el estado de las máquinas virtuales y las instancias de rol. Si no va a usar un conjunto con equilibrio de carga, puede invalidar esta regla.

### <a name="create-network-security-groups"></a>Creación de grupos de seguridad de red

Cree una regla de entrada denominada *myFrontendNSGRule* para permitir el tráfico web entrante en *myFrontendVM* mediante [AzureRmNetworkSecurityRuleConfig New](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Puede limitar el tráfico interno a *myBackendVM* solo desde *myFrontendVM* creando un NSG para la subred de "back-end". En el ejemplo siguiente, se crea una regla NSG denominada *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Agregue un nuevo grupo de seguridad de red denominado *myFrontendNSG* mediante [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

A continuación, agregue un nuevo grupo de seguridad de red denominado *myBackendNSG* mediante New-AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Agregue los grupos de seguridad de red a las subredes:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Crear una máquina virtual de back-end

La manera más fácil de crear la máquina virtual de back-end que se detalla en este tutorial es usar una imagen de SQL Server. Con este tutorial solo se crea la máquina virtual con el servidor de base de datos, pero no se proporciona información del acceso a la base de datos.

Creación de *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Creación de *myBackendVM*:

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

La imagen que se utiliza tiene instalado SQL Server, pero no se usa en este tutorial. Se incluye para mostrar cómo se pueden configurar una máquina virtual para controlar el tráfico de web y otra para controlar la administración de bases de datos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado y protegido redes de Azure cuando están relacionadas con máquinas virtuales. 

> [!div class="checklist"]
> * Creación de una red virtual y una subred
> * Crear una dirección IP pública
> * Crear una máquina virtual de front-end
> * Protegen el tráfico de red.
> * Crear una máquina virtual de back-end

Prosiga con el siguiente tutorial para aprender a supervisar la protección de datos en máquinas virtuales mediante Azure Backup.

> [!div class="nextstepaction"]
> [Copia de seguridad de máquinas virtuales Windows en Azure](./tutorial-backup-vms.md)
