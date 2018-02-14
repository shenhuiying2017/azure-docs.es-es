---
title: "Creación de una puerta de enlace de aplicaciones con redireccionamiento externo mediante Azure PowerShell | Microsoft Docs"
description: "Obtenga información sobre cómo crear una puerta de enlace de aplicaciones que redirija el tráfico web a un sitio externo mediante Azure PowerShell."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: b3639e1aecc2e78e8b107d7af12ca77118f55651
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Creación de una puerta de enlace de aplicaciones con redireccionamiento externo mediante Azure PowerShell

Puede usar Azure PowerShell para configurar el [redireccionamiento del tráfico web](application-gateway-multi-site-overview.md) al crear una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, va a configurar un agente de escucha y una regla que redirigirán el tráfico web que llega a la puerta de enlace de aplicaciones de sitio externo.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Configuración de la red
> * Crear un agente de escucha y una regla de redireccionamiento
> * Creación de una puerta de enlace de aplicaciones

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell localmente, para este tutorial se requiere la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Para encontrar la versión, ejecute ` Get-Module -ListAvailable AzureRM`. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Crear recursos de red

Cree la configuración de subred *myAGSubnet* mediante [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Cree la red virtual llamada *myVNet* mediante [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) con la configuración de subred. Y, por último, cree la dirección IP pública mediante [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Estos recursos se usan para proporcionar conectividad de red a la puerta de enlace de aplicaciones y sus recursos asociados.

```azurepowershell-interactive
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creación de las configuraciones IP y el puerto de front-end

Asocie el elemento *myAGSubnet* que creó anteriormente a la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Asigne la dirección IP pública a la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). Y, después, puede crear el puerto HTTP mediante [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

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

### <a name="create-the-backend-pool-and-settings"></a>Creación de la configuración y el grupo de servidores back-end

Cree el grupo de servidores back-end llamado *defaultPool* para la puerta de enlace de aplicaciones mediante [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Configure los valores del grupo mediante [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>Creación del agente de escucha y de una regla

Se necesita un agente de escucha para habilitar la puerta de enlace de aplicaciones para enrutar el tráfico correctamente. Cree el agente de escucha mediante [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) con la configuración de front-end y el puerto de front-end creados anteriormente. El tráfico se redirige mediante la creación de una configuración de redireccionamiento que se pueda agregar a la regla de enrutamiento creada.

Se necesita una regla de enrutamiento para que el agente de escucha sepa dónde enviar el tráfico entrante. Cree una regla básica llamada *redirectRule* mediante [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) con la configuración de redireccionamiento.

```azurepowershell-interactive
$defaultListener = New-AzureRmApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzureRmApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "http://bing.com"
$redirectRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
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
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

Puede usar [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para obtener la dirección IP pública de la puerta de enlace de aplicaciones. Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

En el explorador debe aparecer *bing.com*.

## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido cómo:

> [!div class="checklist"]
> * Configuración de la red
> * Crear un agente de escucha y una regla de redireccionamiento
> * Creación de una puerta de enlace de aplicaciones

> [!div class="nextstepaction"]
> [Obtenga más información sobre lo que puede hacer con la puerta de enlace de aplicaciones](./application-gateway-introduction.md).