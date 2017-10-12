---
title: "Configuración de un firewall de aplicaciones web: Azure Application Gateway | Microsoft Docs"
description: "Este artículo contiene instrucciones acerca de cómo comenzar a usar el firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configuración del firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [CLI de Azure](application-gateway-web-application-firewall-cli.md)

Aprenda a crear una puerta de enlace de aplicaciones web habilitada para un firewall de aplicaciones web (WAF). Descubra también cómo puede agregar un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente.

El firewall de aplicaciones web de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones.

 Application Gateway es un equilibrador de carga de nivel 7. Proporciona solicitudes HTTP de enrutamiento de rendimiento y conmutación por error entre distintos servidores, tanto en la nube como en entornos locales. Application Gateway cuenta con numerosas características de controlador de entrega de aplicaciones (ADC):

 * Equilibrio de carga HTTP
 * Afinidad de sesión basada en cookies
 * Descarga de Capa de sockets seguros (SSL)
 * Sondeos de estado personalizados
 * Compatibilidad con la funcionalidad de multisitio
 
 Para obtener una lista completa de las características admitidas, consulte [Introducción a Application Gateway](application-gateway-introduction.md).

Descubra también cómo [agregar un WAF a una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway). También se explica el proceso de [creación de una puerta de enlace de aplicaciones que utiliza un firewall de aplicaciones web](#create-an-application-gateway-with-web-application-firewall).

![Imagen de escenario][scenario]

## <a name="waf-configuration-differences"></a>Diferencias de configuración de WAF

Si ha leído este artículo acerca de cómo [crear una puerta de enlace de aplicaciones con PowerShell](application-gateway-create-gateway-arm.md), sabrá qué opciones de la SKU deben configurarse al crear una puerta de enlace de aplicaciones. El firewall de aplicaciones web (WAF) proporciona una configuración adicional que se puede definir al configurar la SKU en una puerta de enlace de aplicaciones. No hay ningún otro cambio que deba realizar en la puerta de enlace de aplicaciones propiamente dicha.

| **Configuración** | **Detalles**
|---|---|
|**SKU** |Una puerta de enlace de aplicaciones normal sin un firewall de aplicaciones web admite los tamaños **Standard\_Small**, **Standard\_Medium** y **Standard\_Large**. Con la introducción de un firewall de aplicaciones web, hay dos SKU adicionales: **WAF\_Medium** y **WAF\_Large**. WAF no se admite en puertas de enlace de aplicaciones de pequeño tamaño.|
|**Nivel** | Los valores disponibles son **Estándar** o **WAF**. Cuando se usa un firewall de aplicaciones web, debe elegir **WAF**.|
|**Modo** | Esta opción determina el modo del firewall de aplicaciones web. Los valores permitidos son **Detección** y **Prevención**. Cuando el firewall de aplicaciones web está configurado en modo **Detección**, todas las amenazas se guardan en un archivo de registro. En modo **Prevención**, los eventos se siguen registrando pero el atacante recibe una respuesta 403 no autorizado de la puerta de enlace de aplicaciones.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Incorporación de un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Para obtener más información, consulte [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sesión en la cuenta de Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Seleccione la suscripción que se usará en este escenario.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Recupere la puerta de enlace en la que desea agregar un firewall de aplicaciones web.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Configure la SKU de WAF. Los tamaños disponibles son **WAF\_Large** y **WAF\_Medium**. Cuando se usa un WAF, el nivel debe ser **WAF**. Confirme la capacidad cuando establezca la SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Configure las opciones de WAF, tal como se define en el ejemplo siguiente: Para **FirewallMode**, los valores disponibles son **Prevención** y **Detección**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Actualice la puerta de enlace de aplicaciones con la configuración definida en el paso anterior.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Este comando actualiza la puerta de enlace de aplicaciones con un firewall de aplicaciones web. Para aprender a consultar los registros de la puerta de enlace de aplicaciones, consulte [Diagnósticos de Application Gateway](application-gateway-diagnostics.md). Debido a la naturaleza de la seguridad de WAF, los registros deben revisarse periódicamente para entender la posición de seguridad de las aplicaciones web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web

Los pasos siguientes le llevan por el proceso entero de creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web.

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Para obtener más información, consulte [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

1. Ejecute `Login-AzureRmAccount` para iniciar sesión en Azure. Se le solicita que se autentique con sus credenciales.

2. Ejecute `Get-AzureRmSubscription` para comprobar las suscripciones de la cuenta.

3. Elija la suscripción de Azure que se va a usar.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos para la puerta de enlace de aplicaciones.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" con la ubicación "West US".

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Configuración de una red virtual

Una puerta de enlace de aplicaciones requiere su propia subred. En este paso, creará una red virtual con el espacio de direcciones 10.0.0.0/16 y dos subredes, una para la puerta de enlace de aplicaciones y otra para los miembros del grupo de back-end.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Configuración de las direcciones IP públicas

Para controlar las solicitudes externas, la puerta de enlace de aplicaciones requiere una dirección IP pública. Esta dirección IP pública no debe definido un elemento `DomainNameLabel` para que la pueda usar la puerta de enlace de aplicaciones.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configuración de la puerta de enlace de aplicaciones

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Las puertas de enlace de aplicaciones creadas con la configuración básica de WAF se configuran con CRS 3.0 como medida de protección.

## <a name="get-an-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso consiste en configurar el front-end para la comunicación. Si se utiliza una dirección IP pública, la puerta de enlace de aplicaciones necesita un nombre DNS asignado dinámicamente (estos nombres no son descriptivos). Para asegurarse de que los usuarios finales pueden llegar a la puerta de enlace de aplicaciones, utilice un registro CNAME que apunte al punto de conexión público de la puerta de enlace de aplicaciones. Para más información, consulte [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Para configurar un alias, recupere los detalles de la puerta de enlace de aplicaciones y su nombre de IP o DNS asociado mediante el elemento PublicIPAddress asociado a dicha puerta de enlace de aplicaciones. Utilice el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME, que hace que las dos aplicaciones web apunten a este nombre DNS. No es recomendable utilizar registros de tipo A, ya que la VIP podría cambiar al reiniciarse la puerta de enlace de aplicaciones.

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

Si desea configurar un registro de diagnóstico para incluir todos los eventos que se detecten o se impidan con una puerta de enlace de aplicaciones web, consulte [Diagnósticos de Application Gateway](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
