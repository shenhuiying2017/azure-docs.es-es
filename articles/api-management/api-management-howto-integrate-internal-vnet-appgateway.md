---
title: Uso de Azure API Management en una red virtual con Application Gateway | Microsoft Docs
description: Aprenda a instalar y configurar Azure API Management en una red virtual interna con Application Gateway (WAF) como front-end.
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: f9bc3ffda9f943a37fd5aadf440abf7d33a6d1de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integración de API Management en una red virtual interna con Application Gateway 

##<a name="overview"></a> Información general
 
El servicio API Management se puede configurar en una red virtual en un modo interno que hace que esta sea accesible únicamente desde dentro de la red virtual. Azure Application Gateway es un servicio de PAAS que proporciona un equilibrador de carga de nivel 7. Actúa como un servicio de proxy inverso y proporciona entre su oferta un firewall de aplicaciones web (WAF).

La combinación de una instancia de API Management aprovisionada en una red virtual interna con el front-end de Application Gateway permite los siguientes escenarios:

* Utilizar el mismo recurso de API Management para su uso por los consumidores internos y los consumidores externos.
* Utilizar un único recurso de API Management y tener definido un subconjunto de API en API Management disponible para los consumidores externos.
* Proporcionar una solución de llave en mano para activar y desactivar el acceso a API Management desde la red Internet pública. 

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos que se describen en este artículo, debe tener:

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Una instancia de APIM. Para más información, vea [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"></a> Escenario
En este artículo se explica cómo usar un único servicio de API Management para los consumidores tanto internos como externos y hacer que actúe como un único servidor de front-end para las API locales y en la nube. También verá cómo exponer solo un subconjunto de las API (resaltado en verde en el ejemplo) para permitir su uso externo, usando para ello la funcionalidad PathBasedRouting disponible en Application Gateway.

En el primer ejemplo de la configuración, todas las API se administran únicamente desde dentro de la red virtual. Los consumidores internos (resaltados en color naranja) pueden tener acceso a todas las API internas y externas. El tráfico nunca se envía a Internet y se entrega un alto rendimiento a través de circuitos Express Route.

![ruta de dirección URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Cree una red virtual y subredes independientes para API Management y Application Gateway. 
3. Si desea crear un servidor DNS personalizado para la red virtual, debe hacerlo antes de iniciar la implementación. Vuelva a comprobar que funciona asegurando que la máquina virtual creada en una subred nueva de la red virtual puede resolver y acceder a todos los puntos de conexión de servicio de Azure.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>¿Qué se necesita para crear una integración entre API Management y Application Gateway?

* **Grupo de servidores de back-end:** se trata de la dirección IP virtual interna del servicio API Management.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores se aplican a todos los servidores del grupo.
* **Puerto front-end:** es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico que llega se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla:** la regla enlaza un agente de escucha con un grupo de servidor back-end.
* **Sondeo de mantenimiento personalizado:** Application Gateway, de forma predeterminada, usa sondeos basados en direcciones IP para determinar cuáles son los servidores de BackendAddressPool que están activos. El servicio API Management responde solo a las solicitudes que tienen el encabezado de host correcto, por lo tanto, los sondeos predeterminados no podrán completarse. Es necesario definir el sondeo de mantenimiento personalizado para ayudar a la puerta de enlace de aplicaciones a determinar que el servicio está activo y debe reenviar las solicitudes.
* **Certificado de dominio personalizado:** para tener acceso a API Management desde Internet, debe crear una asignación de CNAME del nombre de host en el nombre DNS de front-end de Application Gateway. Esto garantiza que el encabezado de nombre de host y el certificado enviados a Application Gateway que se reenvían a API Management pueden ser reconocidos como válidos por APIM.

## <a name="overview-steps"></a> Pasos necesarios para integrar API Management y Application Gateway 

1. Cree un grupo de recursos para el Administrador de recursos.
2. Cree una red virtual, una subred y una IP pública para Application Gateway. Cree otra subred para API Management.
3. Cree un servicio API Management en la subred de la red virtual creada anteriormente y asegúrese de que usa el modo interno.
4. Configure el nombre de dominio personalizado del servicio API Management.
5. Cree un objeto de configuración de Application Gateway.
6. Cree un recurso de Application Gateway.
7. Cree un CNAME del nombre DNS público de Application Gateway en el nombre de host de proxy de API Management.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure.

```powershell
Login-AzureRmAccount
```

Autentíquese con sus credenciales.<BR>

### <a name="step-2"></a>Paso 2

Compruebe las suscripciones para la cuenta y selecciónela.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Paso 3

Cree un grupo de recursos (omita este paso si usa uno existente).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager:

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable subnet que se va a usar para Application Gateway al crear la red virtual.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Paso 2

Asigne el intervalo de direcciones 10.0.1.0/24 a la variable subnet que se va a usar para API Management al crear la red virtual.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Paso 3

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **apim-appGw-RG** para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y las subredes 10.0.0.0/24 y 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Paso 4

Asigne una variable de subred para los pasos siguientes.

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Cree un servicio de API Management dentro de una red virtual configurada en modo interno.

En el ejemplo siguiente se muestra cómo crear un servicio de API Management en una red virtual configurada únicamente para el acceso interno.

### <a name="step-1"></a>Paso 1
Cree un objeto de red virtual de API Management con la subred $apimsubnetdata creada anteriormente.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Paso 2
Cree un servicio de API Management dentro de la red virtual.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Cuando el comando anterior se complete con éxito, consulte [la configuración de DNS necesaria para tener acceso al servicio de API Management en una red virtual interna](api-management-using-with-internal-vnet.md#apim-dns-configuration) para tener acceso a él.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configuración de un nombre de dominio personalizado en API Management

### <a name="step-1"></a>Paso 1
Cargue el certificado con clave privada para el dominio. En este ejemplo es `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Paso 2
Una vez cargado el certificado, cree un objeto de configuración de nombre de host para el proxy con el nombre de host de `api.contoso.net`, ya que el certificado de ejemplo proporciona la autoridad para el dominio `*.contoso.net`. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público **publicIP01** en el grupo de recursos **apim-appGw-RG** para la región Oeste de EE. UU.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Creación de una configuración de puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia Application Gateway, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Paso 2

Configure el puerto IP de front-end para el punto de conexión de IP pública. Este puerto es el puerto al que se conectan los usuarios finales.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Paso 3

Configuración de la dirección IP de front-end con el punto de conexión de IP pública

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Paso 4

Configure el certificado para Application Gateway, usado para descifrar y volver a cifrar el tráfico que pasa por ella.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Paso 5

Cree el agente de escucha HTTP para Application Gateway. Asigne la configuración IP de front-end, el puerto y el certificado SSL que se usarán.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Paso 6

Cree un sondeo personalizado para el punto de conexión de dominio del proxy `ContosoApi` del servicio de API Management. La ruta de acceso `/status-0123456789abcdef` es un punto de conexión de mantenimiento predeterminado hospedado en todos los servicios de API Management. Establezca `api.contoso.net` como un nombre de host de sondeo personalizado para protegerlo con el certificado SSL.

> [!NOTE]
> El nombre de host `contosoapi.azure-api.net` es el nombre de host de proxy predeterminado configurado cuando se crea un servicio denominado `contosoapi` en el ámbito público de Azure. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Paso 7

Cargue el certificado que se usará en los recursos del grupo de back-end habilitado para SSL. Este es el mismo certificado que ha proporcionado en el paso 4 anterior.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Paso 8

Configure las opciones de back-end HTTP para Application Gateway. Esto incluye el establecimiento de un límite de tiempo de espera para la solicitud de back-end después del cual se cancela. Este valor es distinto del tiempo de espera del sondeo.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Paso 9:

Configure el grupo de direcciones IP de back-end denominado **apimbackend** con dirección IP virtual interna para el servicio de API Management creado anteriormente.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Paso 10

Cree una configuración para un back-end ficticio (inexistente). Las solicitudes a las rutas de acceso de API que no se desean exponer desde API Management a través de Application Gateway llegarán a este back-end y se devolverá un error 404.

Defina la configuración HTTP para el back-end ficticio.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Configure un back-end ficticio **dummyBackendPool**, que señale a una dirección FQDN **dummybackend.com**. Esta dirección FQDN no existe en la red virtual.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Cree una configuración de regla que Application Gateway utilice de forma predeterminada y que señale al back-end inexistente **dummybackend.com** en la red virtual.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Paso 11

Configuración de rutas de acceso de reglas de URL para los grupos de back-end Esto permite seleccionar solo algunas de las API en API Management para que se expongan al público. Por ejemplo, en el caso de encontrar `Echo API` (/echo/), `Calculator API` (/calc/), etc., haga que solo `Echo API` resulte accesible desde Internet. 

En el ejemplo siguiente se crea una regla sencilla para la ruta de acceso "/echo/" que enruta el tráfico al back-end "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Si la ruta de acceso no coincide con las reglas de ruta de acceso que se desean habilitar desde API Management, la configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado llamado **dummyBackendPool**. Por ejemplo, http://api.contoso.net/calc/* dirige a **dummyBackendPool**, ya que es el grupo predeterminado para el tráfico no coincidente.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

El paso anterior garantiza que solo se permiten las solicitudes para la ruta de acceso "/echo" a través de Application Gateway. Las solicitudes a otras API configuradas en API Management generarán errores 404 desde Application Gateway cuando se tiene acceso a ellas desde Internet. 

### <a name="step-12"></a>Paso 12

Cree una configuración de regla para que Application Gateway use el enrutamiento basado en la ruta de acceso de la dirección URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Paso 13

Configure el número de instancias y el tamaño de Application Gateway. Aquí usamos [WAFS SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) para aumentar la seguridad de los recursos de API Management.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Paso 14

Configuración de WAFS para que esté en modo "Prevención".
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Cree una instancia de Application Gateway con todos los objetos de configuración de los pasos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Aplicación de un CNAME al nombre de host del proxy de API Management para el nombre DNS público del recurso de Application Gateway

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, Application Gateway requiere un nombre DNS asignado dinámicamente, que puede no ser fácil de usar. 

El nombre DNS de Application Gateway se debe utilizar para crear un registro CNAME, que apunta el nombre de host del proxy (por ejemplo, `api.contoso.net` en los ejemplos anteriores) a este nombre de DNS. Para configurar el registro IP CNAME de front-end, recupere los detalles de Application Gateway y su nombre DNS o IP asociados mediante el elemento PublicIPAddress. No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"></a>Resumen
El servicio Azure API Management configurado en una red virtual proporciona una interfaz de puerta de enlace única para todas las API configuradas, independientemente de si están hospedadas en local o en la nube. La integración de Application Gateway con API Management proporciona la flexibilidad de habilitar de manera selectiva API determinadas para que estén accesibles en Internet, así como la provisión de un firewall de aplicación web como front-end para la instancia de API Management.

##<a name="next-steps"></a> Pasos siguientes
* Obtenga más información sobre Azure Application Gateway.
  * [Introducción a Puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md)
  * [Firewall de aplicaciones web de Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Application Gateway mediante enrutamiento basado en rutas de acceso](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Más información acerca de API Management y redes virtuales
  * [El uso de API Management solo está disponible en la red virtual](api-management-using-with-internal-vnet.md)
  * [Usar Azure API Management con redes virtuales](api-management-using-with-vnet.md)
