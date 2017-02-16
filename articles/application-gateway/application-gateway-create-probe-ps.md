---
title: "Creación de un sondeo personalizado para Application Gateway mediante PowerShell en Resource Manager | Microsoft Docs"
description: Aprenda a crear un sondeo personalizado para Puerta de enlace de aplicaciones mediante PowerShell en el Administrador de recursos
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d883cdc007beaf17118c6b6ddbc8345c3bfb5ef2
ms.openlocfilehash: c766763e4633c4905595ae15aca0679b5ecaf5bd


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Creación de un sondeo personalizado para Puerta de enlace de aplicaciones de Azure mediante PowerShell en el Administrador de recursos de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-probe-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md).  En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del [modelo de implementación clásica](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Paso 1

Utilice Login-AzureRmAccount para autenticarse.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Paso 3

Elección de la suscripción de Azure que se va a usar. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Paso 4

Cree un grupo de recursos (omita este paso si usa uno existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado **appgw-RG** y la ubicación **Oeste de EE. UU.**

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

Con estos pasos, se crea una red virtual y una subred para la puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se usará para crear una red virtual.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Paso 2

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Paso 3

Asigne una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones.

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Creación de un objeto de configuración de la puerta de enlace de aplicaciones con un sondeo personalizado

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones de la aplicación. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Paso 2

Configure el grupo de direcciones IP del back-end denominado **pool01** con las direcciones IP **134.170.185.46, 134.170.188.221, 134.170.185.50**. Estos valores las direcciones IP que reciben el tráfico de red procedente del punto de conexión de la IP del front-nd. Reemplace las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>Paso 3

El sondeo personalizado se configura en este paso.

Los parámetros utilizados son:

* **Interval** : configura las comprobaciones de intervalo de sondeo en segundos.
* **Timeout** : define el tiempo de espera de sondeo para una comprobación de respuesta HTTP.
* **Hostname y path**: dirección URL completa que Application Gateway invoca para determinar el estado de la instancia. Por ejemplo, si tiene el sitio web **http://contoso.com/**, el sondeo personalizado se puede configurar para **http://contoso.com/path/custompath.htm**, con el fin de que las comprobaciones del sondeo tengan una respuesta HTTP satisfactoria.
* **UnhealthyThreshold** : el número de respuestas HTTP con error que es necesario para marcar la instancia del back-end como **incorrecta**.

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>Paso 4

Configure la puerta de enlace de aplicaciones **poolsetting01** para el tráfico del grupo del back-end. Este paso también tiene una configuración de tiempo de espera para la respuesta del grupo del back-end a una solicitud de puerta de enlace de aplicaciones. Cuando una respuesta del back-end alcanza un límite de tiempo de espera, Puerta de enlace de aplicaciones cancela la solicitud. Este valor es diferente al tiempo de espera de sondeo, que es solo para que la respuesta del back-end a las comprobaciones de sondeo.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>Paso 5

Configure el puerto IP del front-end denominado **frontendport01** para el punto de conexión de la IP pública.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>Paso 6

Cree la configuración de direcciones IP del front-end denominada **fipconfig01** y asocie la dirección IP pública con dicha configuración.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>Paso 7

Cree el nombre del agente de escucha **listener01** y asocie el puerto del front-end con la configuración de direcciones IP del front-end.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>Paso 8

Cree la regla de enrutamiento del equilibrador de carga denominada **rule01** que configura el comportamiento del equilibrador de carga.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>Paso 9:

Configure el tamaño de la instancia de la puerta de enlace de aplicaciones.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```


> [!NOTE]
> El valor predeterminado de **InstanceCount** es 2, con un valor máximo de 10. El valor predeterminado de **GatewaySize** es Medium. Se puede elegir entre **Standard_Small**, **Standard_Medium** y **Standard_Large**.
> 
> 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Creación de una puerta de enlace de aplicaciones mediante New-AzureRmApplicationGateway

Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Agregar un sondeo a una puerta de enlace de aplicaciones existente

Debe seguir cuatro pasos para agregar un sondeo personalizado a una puerta de enlace de aplicaciones existente.

### <a name="step-1"></a>Paso 1

Cargue el recurso de puerta de enlace de aplicaciones en una variable de PowerShell mediante `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Paso 2

Agregue un sondeo a la configuración de puerta de enlace existente.

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

En el ejemplo, el sondeo personalizado está configurado para comprobar la dirección URL contoso.com/path/custompath.htm cada 30 segundos. Se configura un umbral de tiempo de espera de 120 segundos con un número máximo de 8 solicitudes de sondeo con error.

### <a name="step-3"></a>Paso 3

Agregue el sondeo al tiempo de espera y la configuración de grupo de back-end mediante `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>Paso 4

Guarde la configuración en la puerta de enlace de aplicaciones mediante `Set-AzureRmApplicationGateway`.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Eliminación de un sondeo de una puerta de enlace de aplicaciones existente

Para eliminar un sondeo personalizado de una puerta de enlace de aplicaciones existentes, lleve a cabo los siguientes pasos.

### <a name="step-1"></a>Paso 1

Cargue el recurso de puerta de enlace de aplicaciones en una variable de PowerShell mediante `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```


### <a name="step-2"></a>Paso 2

Quite la configuración de sondeo de la puerta de enlace de aplicaciones mediante `Remove-AzureRmApplicationGatewayProbeConfig`.

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>Paso 3

Actualice la configuración de grupo de back-end para eliminar la configuración de sondeo y tiempo de espera mediante `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Paso 4

Guarde la configuración en la puerta de enlace de aplicaciones mediante `Set-AzureRmApplicationGateway`. 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puedan llegar a la puerta de enlace de aplicaciones, se puede utilizar un registro CNAME para que apunte al punto de conexión público de la puerta de enlace de aplicaciones. [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para ello, recupere los detalles de la puerta de enlace de aplicaciones y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a la puerta de enlace de aplicaciones. El nombre DNS de la puerta de enlace de aplicaciones se debe utilizar para crear un registro CNAME, que apunta las dos aplicaciones web a este nombre DNS. No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar la descarga de SSL visitando [Configuración de la descarga SSL](application-gateway-ssl-arm.md)




<!--HONumber=Nov16_HO4-->


