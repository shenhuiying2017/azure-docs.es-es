---
title: "Creación y administración de Azure Application Gateway: PowerShell | Microsoft Docs"
description: "Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure mediante el Administrador de recursos de Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: c419e1032476818e430251246022ae14e4355024
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona solicitudes HTTP de enrutamiento del rendimiento y conmutación por error y entre distintos servidores, independientemente de que se encuentren en la nube o en el entorno local. Application Gateway proporciona numerosas características del controlador de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (Capa de sockets seguros), los sondeos personalizados sobre el estado, la compatibilidad con multisitio, y muchas más. Para encontrar una lista completa de las características admitidas, visite [Introducción a Application Gateway](application-gateway-introduction.md).

Este artículo le guiará por los pasos necesarios para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones.

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y el clásico. Antes de trabajar con los recursos de Azure asegúrese de que conoce los [modelos de implementación y las herramientas](../azure-classic-rm.md) . Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este documento describe la creación de una puerta de enlace de aplicaciones con Azure Resource Manager. Para utilizar la versión clásica, vaya a [Creación, inicio o eliminación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
1. Si tiene una red virtual existente, seleccione una subred vacía existente o cree una subred en la red virtual existente, únicamente para uso de la puerta de enlace de aplicaciones. No se puede implementar la puerta de enlace de la aplicación en una red virtual diferente de los recursos que se van a implementar detrás de la puerta de enlace de aplicaciones.
1. Los servidores que configure para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una Puerta de enlace de aplicaciones?

* **Grupo de servidores back-end**: lista de direcciones IP, FQDN y NIC de los servidores back-end. Si se utilizan direcciones IP, estas deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end** : cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores backend.
* **Agente de escucha**: tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla**: enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe dirigir el tráfico cuando llega a un agente de escucha determinado.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

1. Inicie sesión en Azure y escriba sus credenciales.

  ```powershell
  Login-AzureRmAccount
  ```

2. Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```

3. Elección de la suscripción de Azure que se va a usar.

  ```powershell
  Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
  ```

4. Cree un grupo de recursos (omita este paso si usa uno existente).

  ```powershell
  New-AzureRmResourceGroup -Name ContosoRG -Location "West US"
  ```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado **ContosoRG** y la ubicación **Este de EE. UU.**

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md) .


## <a name="create-the-application-gateway-configuration-objects"></a>Creación de objetos de configuración de la puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

```powershell
# Create a subnet and assign the address space of 10.0.0.0/24
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with the address space of 10.0.0.0/16 and add the subnet
$vnet = New-AzureRmVirtualNetwork -Name ContosoVNET -ResourceGroupName ContosoRG -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the newly created subnet
$subnet=$vnet.Subnets[0]

# Create a public IP address that is used to connect to the application gateway. Application Gateway does not support custom DNS names on public IP addresses.  If a custom name is required for the public endpoint, a CNAME record should be created to point to the automatically generated DNS name for the public IP address.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create a gateway IP configuration. The gateway picks up an IP addressfrom the configured subnet and routes network traffic to the IP addresses in the backend IP pool. Keep in mind that each instance takes one IP address.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Configure a backend pool with the addresses of your web servers. These backend pool members are all validated to be healthy by probes, whether they are basic probes or custom probes.  Traffic is then routed to them when requests come into the application gateway. Backend pools can be used by multiple rules within the application gateway, which means one backend pool could be used for multiple web applications that reside on the same host.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Configure backend http settings to determine the protocol and port that is used when sending traffic to the backend servers. Cookie-based sessions are also determined by the backend HTTP settings.  If enabled, cookie-based session affinity sends traffic to the same backend as previous requests for each packet.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

# Configure a frontend port that is used to connect to the application gateway through the public IP address
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Configure the frontend IP configuration with the public IP address created earlier.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Configure the listener.  The listener is a combination of the front end IP configuration, protocol, and port and is used to receive incoming network traffic. 
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Configure a basic rule that is used to route traffic to the backend servers. The backend pool settings, listener, and backend pool created in the previous steps make up the rule. Based on the criteria defined traffic is routed to the appropriate backend.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU for the application gateway, this determines the size and whether or not WAF is used.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Cuando finalice, recupere los detalles sobre DNS y VIP de la puerta de enlace de aplicaciones del recurso de IP pública asociado a la puerta de enlace de aplicaciones.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName ContosoRG
```

## <a name="delete-the-application-gateway"></a>Eliminación de la puerta de enlace de aplicaciones

En el ejemplo siguiente se elimina la puerta de enlace de aplicaciones.

```powershell
# Retrieve the application gateway
$gw = Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG

# Stops the application gateway
Stop-AzureRmApplicationGateway -ApplicationGateway $gw

# Once the application gateway is in a stopped state, use the `Remove-AzureRmApplicationGateway` cmdlet to remove the service.
Remove-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG -Force
```

> [!NOTE]
> Se puede usar el modificador **-force** para suprimir el mensaje de confirmación de eliminación.

Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureRmApplicationGateway`. Este paso no es necesario.

```powershell
Get-AzureRmApplicationGateway -Name ContosoAppGateway -ResourceGroupName ContosoRG
```

## <a name="get-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puedan llegar a la Application Gateway, se puede utilizar un registro CNAME para que apunte al punto de conexión público de la Application Gateway. Para ello, recupere los detalles de la puerta de enlace de aplicaciones y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a la puerta de enlace de aplicaciones. Esta operación se puede realizar con Azure DNS u otro proveedor de DNS, mediante la creación de un registro CNAME que apunte a la [dirección IP pública](../dns/dns-custom-domain.md#public-ip-address). No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

> [!NOTE]
> Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, realice los pasos siguientes:

```powershell
Remove-AzureRmResourceGroup -Name ContosoRG
```

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una instancia de Application Gateway para la descarga de SSL](application-gateway-ssl.md).

Si quiere configurar una instancia de Application Gateway para usarla con un equilibrador de carga interno, consulte [Creación de una instancia de Application Gateway con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea más información acerca de las opciones de equilibrio de carga en general, visite:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
