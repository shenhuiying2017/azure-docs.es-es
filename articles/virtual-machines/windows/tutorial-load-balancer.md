---
title: "Cómo equilibrar la carga de máquinas virtuales de Windows en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar Azure Load Balancer para crear una aplicación con alta disponibilidad y segura en tres máquinas virtuales de Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6738d88d5a0430abaf3855dbf97a618e4c83617f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Cómo equilibrar la carga de máquinas virtuales de Windows en Azure para crear una aplicación de alta disponibilidad
El equilibrio de carga proporciona un mayor nivel de disponibilidad al distribuir las solicitudes entrantes entre varias máquinas virtuales. En este tutorial, aprenderá sobre los distintos componentes de Azure Load Balancer que distribuyen el tráfico y proporcionan una alta disponibilidad. Aprenderá a:

> [!div class="checklist"]
> * Creación de un equilibrador de carga de Azure
> * Creación del sondeo de estado de un equilibrador de carga
> * Crear reglas de tráfico del equilibrador de carga
> * Usar la extensión de script personalizada para crear un sitio IIS básico
> * Crear máquinas virtuales y conectarlas a un equilibrador de carga
> * Ver un equilibrador de carga en acción
> * Agregar y quitar las máquinas virtuales de un equilibrador de carga

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="azure-load-balancer-overview"></a>Información general sobre Azure Load Balancer
Un equilibrador de carga de Azure es un equilibrador de carga de nivel 4 (TCP, UDP) que proporciona una alta disponibilidad mediante la distribución del tráfico entrante entre máquinas virtuales con un estado correcto. Un sondeo de estado de equilibrador de carga supervisa un puerto determinado en cada máquina virtual y solo distribuye tráfico a una máquina virtual operativa.

Se define una configuración de IP de front-end que contiene una o varias direcciones IP públicas. Esta configuración de IP de front-end permite que el equilibrador de carga y las aplicaciones sean accesibles a través de Internet. 

Las máquinas virtuales se conectarán a un equilibrador de carga mediante su tarjeta de interfaz de red (NIC) virtual. Para distribuir el tráfico a las máquinas virtuales, un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red (NIC) virtual conectadas al equilibrador de carga.

Para controlar el flujo de tráfico, se definen reglas de equilibrador de carga para determinados puertos y protocolos que se asignan a las máquinas virtuales.


## <a name="create-azure-load-balancer"></a>Crear un equilibrador de carga de Azure
En esta sección se detalla cómo se puede crear y configurar cada componente del equilibrador de carga. Antes de poder crear el equilibrador de carga, cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupLoadBalancer* en la ubicación *EastUS*:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>Crear una dirección IP pública
Para obtener acceso a la aplicación en Internet, necesita una dirección IP pública para el equilibrador de carga. Cree una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). En el ejemplo siguiente se crea una dirección IP pública denominada *myPublicIP* en el grupo de recursos *myResourceGroupLoadBalancer*:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Crear un equilibrador de carga
Cree una dirección IP de front-end con [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). En el ejemplo siguiente se crea una dirección IP de front-end denominada *myFrontEndPool*: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Cree un grupo de direcciones de back-end con [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). En el siguiente ejemplo se crea un grupo de direcciones de back-end denominado *myBackEndPool*:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Ahora, cree el equilibrador de carga con [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). En el ejemplo siguiente se crea un equilibrador de carga denominado *myLoadBalancer* utilizando la dirección *myPublicIP*:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado
Para permitir que el equilibrador de carga supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. De forma predeterminada, una máquina virtual se quita de la distribución del equilibrador de carga después de dos errores consecutivos en un intervalo de 15 segundos. Cree un sondeo de estado en función de un protocolo o una página de comprobación de mantenimiento específica para la aplicación. 

En el ejemplo siguiente se crea un sondeo de TCP. También se pueden crear sondeos HTTP personalizados para comprobaciones de estado más específicas. Al usar un sondeo HTTP personalizado, debe crear la página de comprobación de estado, por ejemplo *healthcheck.aspx*. El sondeo debe devolver una respuesta **HTTP 200 OK** para que el equilibrador de carga mantenga el host en rotación.

Para crear un sondeo de estado TCP, se usa [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). En el ejemplo siguiente se crea un sondeo de estado denominado *myHealthProbe* que supervisa cada máquina virtual:

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Actualice el equilibrador de carga con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga
Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Se define la configuración de IP front-end para el tráfico entrante y el grupo IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Para asegurarse de que solo las máquinas virtuales correctas reciban tráfico, también hay que definir el sondeo de estado que se va usar.

Cree una regla del equilibrador de carga con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). En el ejemplo siguiente se crea una regla del equilibrador de carga denominada *myLoadBalancerRule* y se equilibra el tráfico en el puerto *80*:

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Actualice el equilibrador de carga con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Configurar la red virtual
Antes de implementar algunas máquinas virtuales y poder probar el equilibrador, cree los recursos de red virtual auxiliares. Para más información sobre las redes virtuales, consulte el tutorial [Administración de Azure Virtual Networks](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Crear recursos de red
Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada con una subred llamada *myVnet* con *mySubnet*:

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Cree una regla de grupo de seguridad de red con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) y después cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Agregue el grupo de seguridad de red a la subred con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) y, después, actualice la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). 

En el ejemplo siguiente, se crea una regla de grupo de seguridad de red denominada *myNetworkSecurityGroup* y se aplica a *mySubnet*:

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

Las NIC virtuales se crean con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). En el ejemplo siguiente se crean tres NIC. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes). Puede crear NIC virtuales y máquinas virtuales adicionales en cualquier momento y agregarlas al equilibrador de carga:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Crear máquinas virtuales
Para mejorar la alta disponibilidad de la aplicación, coloque las máquinas virtuales en un conjunto de disponibilidad.

Cree un conjunto de disponibilidad con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado *myAvailabilitySet*:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Establezca un nombre de usuario de administrador y una contraseña para las máquinas virtuales con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Ahora puede crear las máquinas virtuales con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crean tres máquinas virtuales:

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

Se tarda unos minutos en crear y configurar las tres máquinas virtuales.

### <a name="install-iis-with-custom-script-extension"></a>Instalar IIS con la extensión de script personalizado
En un tutorial anterior sobre [Cómo personalizar una máquina virtual de Windows](tutorial-automate-vm-deployment.md), aprendió a automatizar la personalización de máquinas virtuales con la extensión de script personalizado para Windows. Puede usar el mismo enfoque para instalar y configurar IIS en las máquinas virtuales.

Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar la extensión de script personalizado. La extensión ejecuta `powershell Add-WindowsFeature Web-Server` para instalar el servidor web de IIS y después actualiza la página *Default.htm* para mostrar el nombre de host de la máquina virtual:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Prueba del equilibrador de carga
Obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de *myPublicIP* que se ha creado anteriormente:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

A continuación, puede escribir la dirección IP pública en un explorador web. Se muestra el sitio web, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico como en el ejemplo siguiente:

![Ejecución del sitio web de IIS](./media/tutorial-load-balancer/running-iis-website.png)

Para ver cómo el equilibrador de carga distribuye el tráfico entre las tres máquinas virtuales que ejecutan la aplicación, puede realizar una actualización forzada del explorador web.


## <a name="add-and-remove-vms"></a>Agregar y quitar máquinas virtuales
Puede que tenga que realizar labores de mantenimiento de las máquinas virtuales que ejecutan la aplicación, como la instalación de actualizaciones del sistema operativo. Para gestionar un aumento de tráfico a la aplicación, tiene que agregar más máquinas virtuales. Esta sección le muestra cómo quitar o agregar una máquina virtual desde el equilibrador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Eliminación de una máquina virtual del equilibrador de carga
Obtenga la tarjeta de interfaz de red con [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) y después establezca la propiedad *LoadBalancerBackendAddressPools* de la NIC virtual en *$null*. Por último, actualice la NIC virtual:

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Para ver cómo el equilibrador de carga distribuye el tráfico entre las dos máquinas virtuales que quedan ejecutando la aplicación, puede realizar una actualización forzada del explorador web. Ahora puede realizar tareas de mantenimiento en la máquina virtual, como instalar actualizaciones del sistema operativo o realizar un reinicio de máquina virtual.

### <a name="add-a-vm-to-the-load-balancer"></a>Incorporación de una máquina virtual al equilibrador de carga
Después de realizar el mantenimiento de la máquina virtual, o si necesita expandir la capacidad, establezca la propiedad *LoadBalancerBackendAddressPools* de la NIC virtual en el elemento *BackendAddressPool* de [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Obtención del equilibrador de carga:

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un equilibrador de carga y conectó máquinas virtuales a él. Ha aprendido a:

> [!div class="checklist"]
> * Creación de un equilibrador de carga de Azure
> * Creación del sondeo de estado de un equilibrador de carga
> * Crear reglas de tráfico del equilibrador de carga
> * Usar la extensión de script personalizada para crear un sitio IIS básico
> * Crear máquinas virtuales y conectarlas a un equilibrador de carga
> * Ver un equilibrador de carga en acción
> * Agregar y quitar las máquinas virtuales de un equilibrador de carga

Avance al siguiente tutorial para aprender a administrar redes de máquinas virtuales.

> [!div class="nextstepaction"]
> [Administración de máquinas y redes virtuales](./tutorial-virtual-network.md)
