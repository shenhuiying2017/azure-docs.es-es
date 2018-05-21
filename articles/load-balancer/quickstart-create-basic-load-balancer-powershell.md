---
title: 'Guía de inicio rápido: Creación de un equilibrador de carga básico en Azure PowerShell | Microsoft Docs'
description: En esta guía de inicio rápido, se explica cómo crear un equilibrador de carga básico utilizando PowerShell.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms:custom: mvc
ms.openlocfilehash: 2e80a090d003770f47d28dfaacf7ba5140f7b41f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="get-started"></a>Guía de inicio rápido: Creación de un equilibrador de carga público con Azure PowerShell
En esta guía de inicio rápido, se explica cómo crear un equilibrador de carga básico utilizando Azure PowerShell. Para probar el equilibrador de carga, implemente dos máquinas virtuales que ejecuten Windows Server y equilibre la carga de una aplicación web entre ellas.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para realizar los pasos de este artículo necesita la versión 5.4.1 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure. 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para poder crear el equilibrador de carga, debe crear primero un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupLB* en la ubicación *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública
Para obtener acceso a la aplicación en Internet, necesita una dirección IP pública para el equilibrador de carga. Cree una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). En el ejemplo siguiente, se crea una dirección IP pública denominada *myPublicIP* en el grupo de recursos *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Dynamic" `
  -Name "myPublicIP"
```
## <a name="create-basic-load-balancer"></a>Creación de un equilibrador de carga básico
 En esta sección, va a configurar la dirección IP de front-end y el grupo de direcciones de back-end del equilibrador de carga y a crear el equilibrador de carga básico.
 
### <a name="create-frontend-ip"></a>Creación de la dirección IP de front-end
Cree una dirección IP de front-end con [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). En el ejemplo siguiente, se crea una configuración de direcciones IP de front-end llamada *myFrontEnd* y se asocia a la dirección *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>Configuración del grupo de direcciones de back-end

Cree un grupo de direcciones de back-end con [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). En el resto de los pasos las máquinas virtuales se conectan a este grupo back-end. En el siguiente ejemplo se crea un grupo de direcciones de back-end denominado *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>Creación de un sondeo de estado
Para permitir que el equilibrador de carga supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. De forma predeterminada, una máquina virtual se quita de la distribución del equilibrador de carga después de dos errores consecutivos en un intervalo de 15 segundos. Cree un sondeo de estado en función de un protocolo o una página de comprobación de mantenimiento específica para la aplicación. 

En el ejemplo siguiente se crea un sondeo de TCP. También se pueden crear sondeos HTTP personalizados para comprobaciones de estado más específicas. Al usar un sondeo HTTP personalizado, debe crear la página de comprobación de estado, por ejemplo *healthcheck.aspx*. El sondeo debe devolver una respuesta **HTTP 200 OK** para que el equilibrador de carga mantenga el host en rotación.

Para crear un sondeo de estado TCP, se usa [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). En el ejemplo siguiente, se crea un sondeo de estado llamado *myHealthProbe* que supervisa cada máquina virtual en el puerto *HTTP* *80*:

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga
Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Se define la configuración de IP front-end para el tráfico entrante y el grupo IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Para asegurarse de que solo las máquinas virtuales correctas reciban tráfico, también hay que definir el sondeo de estado que se va usar.

Cree una regla del equilibrador de carga con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). En el ejemplo siguiente se crea una regla del equilibrador de carga llamada *myLoadBalancerRule* y se equilibra el tráfico en el puerto *TCP* *80*:

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Creación de las reglas NAT

Cree las reglas NAT con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig). En el ejemplo siguiente, se crean unas reglas NAT llamadas *myLoadBalancerRDP1* y *myLoadBalancerRDP2* para permitir conexiones RDP con los servidores back-end a través de los puertos 4221 y 4222:

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Creación de un equilibrador de carga

Cree un equilibrador de carga básico con [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). En el ejemplo siguiente, se crea un equilibrador de carga básico público llamado myLoadBalancer usando la configuración de direcciones IP de front-end, el grupo de servidores back-end, el sondeo de estado, la regla de equilibrio de carga y las reglas NAT que creó en los pasos anteriores:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Crear recursos de red
Para poder implementar algunas máquinas virtuales y probar el equilibrador, debe crear unos recursos de red que lo permitan: una red virtual y varias NIC virtuales. 

### <a name="create-a-virtual-network"></a>Crear una red virtual
Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada con una subred llamada *myVnet* con *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>Creación de un grupo de seguridad de red
Cree un grupo de seguridad de red para definir las conexiones entrantes a la red virtual.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Creación de una regla de grupo de seguridad de red para el puerto 3389
Cree una regla de grupo de seguridad de red para permitir conexiones RDP a través del puerto 3389 con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creación de una regla de grupo de seguridad de red para el puerto 80
Cree una regla de grupo de seguridad de red para permitir conexiones entrantes a través del puerto 80 con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

Cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup`
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup'`
-SecurityRules $rule1,$rule2
```

###<a name="create-nics"></a>Cree tarjetas NIC
Cree NIC virtuales con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). En el ejemplo siguiente, se crean dos NIC virtuales. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes). Puede crear NIC virtuales y máquinas virtuales adicionales en cualquier momento y agregarlas al equilibrador de carga:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>Crear máquinas virtuales
Para mejorar la alta disponibilidad de la aplicación, coloque las máquinas virtuales en un conjunto de disponibilidad.

Cree un conjunto de disponibilidad con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Establezca un nombre de usuario de administrador y una contraseña para las máquinas virtuales con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Ahora puede crear las máquinas virtuales con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente, se crean dos máquinas virtuales y los componentes de red virtual necesarios, si aún no existen:

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

El parámetro `-AsJob` crea la máquina virtual como tarea en segundo plano, por lo que PowerShell solicita la vuelta. Puede ver detalles de trabajos en segundo plano con el cmdlet `Job`. Se tarda unos minutos en crear y configurar las dos máquinas virtuales.

### <a name="install-iis-with-custom-web-page"></a>Instalación de IIS con una página web personalizada
 
Siga estos pasos para instalar IIS con una página web personalizada en las dos máquinas virtuales de back-end:

1. Obtenga la dirección IP pública del equilibrador de carga. Para ello, use `Get-AzureRmPublicIPAdress`.

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Cree una conexión de Escritorio remoto a VM1 utilizando la dirección IP pública del paso anterior. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Escriba las credenciales de *VM1* para iniciar la sesión RDP.
4. Inicie Windows PowerShell en VM1 y use los siguientes comandos para instalar el servidor IIS y actualizar el archivo htm predeterminado.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Cierre la conexión RDP con *myVM1*.
6. Cree una conexión RDP con *myVM2* ejecutando el comando `mstsc /v:PublicIpAddress:4222` y repita el paso 4 con *VM2*.

## <a name="test-load-balancer"></a>Prueba del equilibrador de carga
Obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de *myPublicIP* que se ha creado anteriormente:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

A continuación, puede escribir la dirección IP pública en un explorador web. Se muestra el sitio web, incluido el nombre de host de la máquina virtual a la que el equilibrador de carga distribuye el tráfico como en el ejemplo siguiente:

![Prueba del equilibrador de carga](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Para ver cómo el equilibrador de carga distribuye el tráfico entre las dos máquinas virtuales que ejecutan la aplicación, puede forzar la actualización del explorador web.


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Pasos siguientes
- [Más información acerca de Azure Load Balancer](load-balancer-overview.md)
