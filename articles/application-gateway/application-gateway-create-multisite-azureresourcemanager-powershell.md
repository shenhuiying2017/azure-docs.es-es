---
title: "Creación de una puerta de enlace de aplicaciones para hospedar varios sitios | Microsoft Docs"
description: "Esta página proporciona instrucciones para crear y configurar una puerta de enlace de aplicaciones de Azure para hospedar varias aplicaciones web en la misma puerta de enlace."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Creación de una puerta de enlace de aplicaciones para hospedar varias aplicaciones web

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-multisite-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-multisite-azureresourcemanager-powershell.md)

El hospedaje de varios sitios permite implementar más de una aplicación web en la misma puerta de enlace de aplicaciones. Se basa en la presencia de un encabezado host en la solicitud HTTP entrante para determinar el agente de escucha que recibe el tráfico. Luego, dicho agente dirige el tráfico al grupo de back-end adecuado, el configurado en la definición de las reglas de la puerta de enlace. En las aplicaciones web con SSL habilitado, la puerta de enlace de aplicaciones depende de la extensión de la indicación de nombre de servidor (SNI) para elegir el agente de escucha correcto para el tráfico web.En las aplicaciones web con SSL habilitado, Application Gateway depende de la extensión de la indicación de nombre de servidor (SNI) para elegir el agente de escucha correcto para el tráfico web. Un uso común del hospedaje de varios sitios es el equilibrio de carga de las solicitudes de diferentes dominios web entre diferentes grupos de servidores de back-end. De forma similar, varios subdominios del mismo dominio raíz también se pueden hospedar en la misma puerta de enlace de aplicaciones.

## <a name="scenario"></a>Escenario

En el siguiente ejemplo, la puerta de enlace de aplicaciones sirve el tráfico a contoso.com y a fabrikam.com con dos grupos de servidores back-end: el grupo de servidores de contoso y el grupo de servidores de fabrikam. Se puede usar una configuración similar para hospedar subdominios como app.contoso.com y blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/).
2. Los servidores agregados al grupo de back-end para usar la puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual en una red virtual o con una dirección IP/VIP pública asignada.

## <a name="requirements"></a>Requisitos

* **Grupo de servidores back-end** : lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública. También puede utilizarse el FQDN.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). En el caso de las puertas de enlace de aplicaciones con sitios múltiples habilitados, también se agregan el nombre de host y los indicadores de SNI.
* **Regla:** enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico que llegue a un agente de escucha concreto. Las reglas se procesan en el orden en que aparecen y el tráfico se dirige a través de la primera regla que coincida independientemente de la especificidad. Por ejemplo, si tiene una regla que usa un agente de escucha básico y una regla que usa un agente de escucha multisitio en el mismo puerto, la regla con el agente de escucha multisitio debe aparecer antes que la regla con el agente de escucha básico para que la regla multisitio funcione según lo previsto.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

A continuación se muestran los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Cree un grupo de recursos para el Administrador de recursos.
2. Cree una red virtual, subredes y una IP pública para la puerta de enlace de aplicaciones.
3. Cree un objeto de configuración de la Puerta de enlace de aplicaciones.
4. Cree un recurso de Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. En [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md), encontrará más información al respecto.

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure.

```powershell
Login-AzureRmAccount
```
Se le solicita que se autentique con sus credenciales.

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Paso 3

Elección de la suscripción de Azure que se va a usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Paso 4

Cree un grupo de recursos (omita este paso si usa uno existente).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

También puede crear etiquetas para un grupo de recursos de la puerta de enlace de aplicaciones:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Azure Resource Manager requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado **appgw-RG** cuya ubicación es **oeste de EE. UU**.

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-subnets"></a>Creación una red virtual y subredes

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos. En este paso, se crean dos subredes. La primera es para la propia puerta de enlace de aplicaciones. La puerta de enlace de aplicaciones requiere una subred propia que contenga sus instancias. En dicha subred solo se pueden implementar otras puertas de enlace de aplicaciones. La segunda subred se usa para contener los servidores back-end de las aplicaciones.

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable subnet que se va a usar para contener la puerta de enlace de aplicaciones.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Paso 2

Asigne el intervalo de direcciones 10.0.1.0/24 a la variable subnet2 que se va a usar para los grupos back-end.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Paso 3

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región oeste de EE. UU. con el prefijo 10.0.0.0/16 y las subredes 10.0.0.0/24, y 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Paso 4

Asignación de una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Creación de una configuración de puerta de enlace de aplicaciones

Antes de crear la puerta de enlace de aplicaciones, debe configurar todos los elementos de configuración. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia la puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de la red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Paso 2

Configure el grupo de direcciones IP de back-end llamado **pool01** y **pool2** con las direcciones IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** para **pool1** y **134.170.186.46**, **134.170.189.221**, **134.170.186.50** para **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

En este ejemplo, hay dos grupos de back-end para enrutar el tráfico de red en función del sitio solicitado. Un grupo recibe el tráfico del sitio "contoso.com" y otro lo recibe del sitio "fabrikam.com". Tiene que reemplazar las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación. En lugar de las direcciones IP internas, también se pueden usar direcciones IP públicas, el nombre de dominio completo o la NIC de una máquina virtual para las instancias de back-end. Use el parámetro "-BackendFQDNs" en PowerShell para especificar FQDN, en lugar de direcciones IP.

### <a name="step-3"></a>Paso 3

Configure la opción de la puerta de enlace de aplicaciones **poolsetting01** y **poolsetting02** para el tráfico de red con carga equilibrada en el grupo de back-end. En este ejemplo, se configuran distintas opciones de configuración para los grupos de back-end. Cada grupo de back-end puede tener su propia configuración de grupo de back-end.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Paso 4

Configuración de la dirección IP de front-end con el punto de conexión de IP pública

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Paso 5

Configuración del puerto front-end de una puerta de enlace de aplicaciones

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Paso 6

Configuración de dos certificados SSL para los dos sitios web que vamos a admitir en este ejemplo. Uno de ellos es para el tráfico de contoso.com y el otro es para el de fabrikam.com. Deben ser certificados emitidos por una entidad de certificación para sus sitios web. Los certificados autofirmados se admiten, pero no se recomiendan para el tráfico de producción.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Paso 7

Configuración de dos agentes de escucha para los dos sitios web del ejemplo. En este paso se configuran los agentes de escucha para la dirección IP pública, el puerto y el host usados para recibir el tráfico entrante. El parámetro HostName es necesario para lograr la compatibilidad con varios sitios y se debe establecer en el sitio web adecuado para el que se recibe el tráfico. El parámetro RequireServerNameIndication debe establecerse en true para los sitios web que necesitan compatibilidad con SSL en un escenario de múltiples hosts. Si se requiere compatibilidad con SSL, también es preciso especificar el certificado SSL que se usa para proteger el tráfico de esa aplicación web. La combinación de FrontendIPConfiguration, FrontendPort y HostName debe ser única en cada agente de escucha. Cada agente de escucha puede admitir solo un certificado.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Paso 8

Creación de la configuración de dos reglas para las dos aplicaciones web de este ejemplo. Una regla une los agentes de escucha, los grupos de back-end y la configuración de http. En este paso se configura la puerta de enlace de aplicaciones para que use una regla de enrutamiento básica, una para cada sitio web. El tráfico para cada sitio web lo recibe el agente de escucha que tenga configurado y, luego, se dirige a su grupo de back-end configurado, para lo que se usan las propiedades especificadas en BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Paso 9:

Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Cree una puerta de enlace de aplicaciones con todos los objetos de configuración de los pasos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> El aprovisionamiento de Application Gateway es una operación larga y puede tardar algún tiempo en completarse.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

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

## <a name="next-steps"></a>Pasos siguientes

Aprenda a proteger sitios web con [Firewall de aplicaciones web de Application Gateway (versión preliminar)](application-gateway-webapplicationfirewall-overview.md)

