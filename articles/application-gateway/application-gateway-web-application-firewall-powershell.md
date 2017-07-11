---
title: "Configuración del firewall de aplicaciones web en una instancia de Azure Application Gateway | Microsoft Docs"
description: "En este artículo se proporcionan instrucciones sobre cómo comenzar a usar el firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: c23e7404f9eee6f1246cafc72c6733546cc82934
ms.contentlocale: es-es
ms.lasthandoff: 05/04/2017


---
<a id="configure-web-application-firewall-on-a-new-or-existing-application-gateway" class="xliff"></a>

# Configuración del firewall de aplicaciones web en una puerta de enlace de aplicaciones nueva o existente

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Aprenda a crear una puerta de enlace de aplicaciones habilitada para un firewall de aplicaciones web o a agregar un firewall de aplicaciones web a una puerta de enlace de aplicaciones existente.

El firewall de aplicaciones web (WAF) de Azure Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones.

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. La aplicación proporciona numerosas características de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (capa de sockets seguros), los sondeos personalizados sobre el estado y la compatibilidad con sitios múltiples. Para obtener una lista completa de las características admitidas, consulte la información general sobre Application Gateway.

En el artículo siguiente se muestra cómo [agregar el firewall de aplicaciones web a una puerta de enlace de aplicaciones existente](#add-web-application-firewall-to-an-existing-application-gateway) y [crear una puerta de enlace de aplicaciones que usa el firewall de aplicaciones web](#create-an-application-gateway-with-web-application-firewall).

![imagen de escenario][scenario]

<a id="waf-configuration-differences" class="xliff"></a>

## Diferencias de configuración de WAF

Si ha leído [Creación de una puerta de enlace de aplicaciones con PowerShell](application-gateway-create-gateway-arm.md), comprenderá los valores de SKU que se deben configurar al crear una puerta de enlace de aplicaciones. WAF proporciona una configuración adicional que se puede definir al configurar la SKU en una puerta de enlace de aplicaciones. No hay ningún otro cambio que deba realizar en la puerta de enlace de aplicaciones propiamente dicha.

| **Configuración** | **Detalles**
|---|---|
|**SKU** |Una puerta de enlace de aplicaciones normal sin WAF admite los tamaños **Standard\_Small**, **Standard\_Medium** y **Standard\_Large**. Con la introducción de WAF, hay dos SKU adicionales, **WAF\_Medium** y **WAF\_Large**. No se admite WAF en puertas de enlace de aplicaciones de pequeño tamaño.|
|**Nivel** | Los valores disponibles son **Estándar** o **WAF**. Cuando se usa el firewall de aplicaciones web, se debe seleccionar **WAF**.|
|**Modo** | Esta configuración es el modo de WAF. Los valores permitidos son **Detección** y **Prevención**. Cuando WAF está configurado en modo de detección, todas las amenazas se almacenan en un archivo de registro. En modo de prevención, los eventos se siguen registrando pero el atacante recibe una respuesta 403 no autorizado de la puerta de enlace de aplicaciones.|

<a id="add-web-application-firewall-to-an-existing-application-gateway" class="xliff"></a>

## Adición del firewall de aplicaciones web a una puerta de enlace de aplicaciones existente

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sesión en la cuenta de Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Seleccione la suscripción que se usará en este escenario.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Recupere la puerta de enlace a la que va a agregar el firewall de aplicaciones web.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

1. Configure la SKU del firewall de aplicaciones web. Los tamaños disponibles son **WAF\_Large** y **WAF\_Medium**. Cuando se usa un firewall de aplicaciones web el nivel debe ser **WAF** y la capacidad se debe confirmar cuando se establece la SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

1. Configure las opciones de WAF, tal como se define en el ejemplo siguiente:

   Para **FirewallMode**, los valores disponibles son prevención y detección.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

1. Actualice la puerta de enlace de aplicaciones con la configuración definida en el paso anterior.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Este comando actualiza la puerta de enlace de aplicaciones con el firewall de aplicaciones web. Se recomienda consultar [Diagnósticos de Application Gateway](application-gateway-diagnostics.md) para entender cómo ver los registros de la puerta de enlace de aplicaciones. Debido a la naturaleza de la seguridad de WAF, los registros se deben revisar periódicamente para entender la postura de seguridad de las aplicaciones web.

<a id="create-an-application-gateway-with-web-application-firewall" class="xliff"></a>

## Creación de una puerta de enlace de aplicaciones con el firewall de aplicaciones web

Los pasos siguientes le llevan por el proceso entero de creación de una puerta de enlace de aplicaciones con el firewall de aplicaciones web.

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sesión en Azure mediante la ejecución de `Login-AzureRmAccount`; se le pedirá que se autentique con sus credenciales.

1. Compruebe las suscripciones de la cuenta mediante la ejecución de `Get-AzureRmSubscription`.

1. Elección de la suscripción de Azure que se va a usar.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

<a id="create-a-resource-group" class="xliff"></a>

### Crear un grupo de recursos

Cree un grupo de recursos para la puerta de enlace de aplicaciones.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md) .

<a id="configure-virtual-network" class="xliff"></a>

### Configuración de una red virtual

La puerta de enlace de aplicaciones requiere su propia subred. En este paso creará una red virtual con un espacio de direcciones de 10.0.0.0/16 y dos subredes, una para la puerta de enlace de aplicaciones y otra para los miembros del grupo de back-end.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for Application Gateway instances. With a smaller subnet, you may not be able to add more instance of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the backend pool members subnet
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previous created subnets
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

<a id="configure-public-ip-address" class="xliff"></a>

### Configuración de direcciones IP públicas

Para controlar las solicitudes externas, la puerta de enlace de aplicaciones requiere una dirección IP pública. Esta dirección IP pública no debe definido un elemento `DomainNameLabel` para que la pueda usar la puerta de enlace de aplicaciones.

```powershell
# Create a public IP address for use with the application gateway. Defining the domainnamelabel during creation is not supported for use with application gateway
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

<a id="configure-the-application-gateway" class="xliff"></a>

### Configuración de la Puerta de enlace de aplicaciones

```powershell
# Create a IP configuration. This configures what subnet the Application Gateway uses. When Application Gateway starts, it picks up an IP address from the subnet configured and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a backend pool to hold the addresses or NICs for the application that application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authenication certificate that will be used to communicate with the backend servers
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Conifugre the backend HTTP settings to be used to define how traffic is routed to the backend pool. The authenication certificate used in the previous step is added to the backend http settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a frontend port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a frontend IP configuration to associate the public IP address with the application gateway
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end ip configuration, port, and ssl certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

#Create a load balancer routing rule that configures the load balancer behavior. In this example, a basic round robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

#Configure the waf configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway utilizing all the previously created configuration objects
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Las puertas de enlace de la aplicación creadas con la configuración de firewall de aplicación web básica se configuran con CRS 3.0 para protección.

<a id="get-application-gateway-dns-name" class="xliff"></a>

## Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puedan llegar a la Application Gateway, se puede utilizar un registro CNAME para que apunte al punto de conexión público de la Application Gateway. [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para ello, recupere los detalles de la puerta de enlace de aplicaciones y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a la puerta de enlace de aplicaciones. El nombre DNS de la puerta de enlace de aplicaciones se debe utilizar para crear un registro CNAME, que apunta las dos aplicaciones web a este nombre DNS. No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

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

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

Aprenda a configurar el registro de diagnóstico para registrar los eventos que se detectan o impiden con el firewall de aplicaciones web en [Diagnósticos de Application Gateway](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

