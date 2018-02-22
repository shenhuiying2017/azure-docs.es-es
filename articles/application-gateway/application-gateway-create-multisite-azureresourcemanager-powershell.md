---
title: "Creación de una puerta de enlace de aplicaciones con hospedaje de varios sitios mediante Azure PowerShell | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones que hospeda varios sitios mediante Azure PowerShell.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: ed385eac624f5c59981c01ee70ba2a1700a78653
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Creación de una puerta de enlace de aplicaciones con hospedaje de varios sitios mediante Azure PowerShell

Puede usar Azure PowerShell para configurar el [hospedaje de varios sitios web](application-gateway-multi-site-overview.md) cuando se crea una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, creará grupos de back-end mediante conjuntos de escalado de máquinas virtuales. Después, configurará agentes de escucha y reglas basados en los dominios que posee para asegurarse de que el tráfico web llega a los servidores adecuados en los grupos. En este tutorial se da por supuesto que posee varios dominios y se van a utilizar los ejemplos de *www.contoso.com* y *www.fabrikam.com*.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Configuración de la red
> * Creación de una puerta de enlace de aplicaciones
> * Creación de agentes de escucha y reglas de enrutamiento
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end
> * Creación de un registro CNAME en el dominio

![Ejemplo de enrutamiento de varios sitios](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell localmente, para este tutorial se requiere la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Para encontrar la versión, ejecute ` Get-Module -ListAvailable AzureRM`. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Crear recursos de red

Configure las subredes llamadas *myBackendSubnet* y *myAGSubnet* mediante [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Cree la red virtual llamada *myVNet* mediante [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) con las configuraciones de subred. Y, por último, cree la dirección IP pública llamada *myAGPublicIPAddress* con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Estos recursos se usan para proporcionar conectividad de red a la puerta de enlace de aplicaciones y sus recursos asociados.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creación de las configuraciones IP y el puerto de front-end

Asocie el elemento *myAGSubnet* que creó anteriormente a la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Asigne el elemento *myAGPublicIPAddress* a la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pools-and-settings"></a>Creación de la configuración y los grupos de servidores back-end

Cree los grupos de servidores back-end llamados *contosoPool* y *fabrikamPool* para la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Configure los valores del grupo mediante [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$contosoPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$fabrikamPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Creación de las reglas y los agentes de escucha

Es necesario un agente de escucha para que la puerta de enlace de aplicaciones enrute el tráfico de forma adecuada a los grupos de servidores back-end. En este tutorial, creará los agentes de escucha para cada uno de los dos dominios. En este ejemplo, se crean los agentes de escucha para los dominios de *www.contoso.com* y *www.fabrikam.com*.

Cree los agentes de escucha llamados*contosoListener* y *fabrikamListener* mediante [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) con la configuración de front-end y el puerto de front-end creados anteriormente. Se requieren reglas para que los agentes de escucha sepan qué grupo de servidores back-end se usa para el tráfico entrante. Cree las reglas básicas denominadas *contosoRule* y *fabrikamRule* con [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosolistener = New-AzureRmApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"
$fabrikamlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"
$contosoRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
$fabrikamRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Ahora que ha creado los recursos de apoyo necesarios, especifique los parámetros para la puerta de enlace de aplicaciones llamada *myAppGateway* con [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) y, a continuación, cree la puerta de enlace mediante [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Creación de conjuntos de escalado de máquinas virtuales

En este ejemplo, creará dos conjuntos de escalado de máquinas virtuales que admiten los dos grupos de back-end que ha creado. Los conjuntos de escalado que se crean se llaman *myvmss1* y *myvmss2*. Cada conjunto de escalado contiene dos instancias de máquina virtual en las que se instala IIS. Asignará el conjunto de escalado al grupo de servidores back-end cuando configure los valores de IP.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$fabrikamPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
  }
  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
  $vmssConfig = New-AzureRmVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic
  Set-AzureRmVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
  Set-AzureRmVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i
  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalación de IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i
  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="create-cname-record-in-your-domain"></a>Creación de un registro CNAME en el dominio

Después de crear la puerta de enlace de aplicaciones con la dirección IP pública, puede obtener la dirección DNS y usarla para crear un registro CNAME en el dominio. Puede usar [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para obtener la dirección DNS de la puerta de enlace de aplicaciones. Copie el valor de *fqdn* de DNSSettings y úselo como valor del registro CNAME que creó. No se recomienda el uso de registros A, ya que la IP virtual puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

Escriba el nombre de dominio en la barra de direcciones del explorador. Por ejemplo, http://www.contoso.com.

![Prueba del sitio de contoso en la puerta de enlace de aplicaciones](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

Cambie la dirección al otro dominio. Debería ver algo parecido al ejemplo siguiente:

![Prueba del sitio de fabrikam en la puerta de enlace de aplicaciones](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido cómo:

> [!div class="checklist"]
> * Configuración de la red
> * Creación de una puerta de enlace de aplicaciones
> * Creación de agentes de escucha y reglas de enrutamiento
> * Crear conjuntos de escalado de máquinas virtuales con los grupos de back-end
> * Creación de un registro CNAME en el dominio

> [!div class="nextstepaction"]
> [Obtenga más información sobre lo que puede hacer con la puerta de enlace de aplicaciones](application-gateway-introduction.md).