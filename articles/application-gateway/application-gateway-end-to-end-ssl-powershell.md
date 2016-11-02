<properties
    pageTitle="Configuración de la directiva SSL y SSL de extremo a extremo con Application Gateway | Microsoft Azure"
    description="En este artículo se describe cómo configurar SSL de extremo a extremo con Application Gateway mediante PowerShell para Azure Resource Manager"
    services="application-gateway"
    documentationCenter="na"
    authors="georgewallace"
    manager="carmonmills"
    editor="tysonn"/>

<tags
    ms.service="application-gateway"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="gwallace"/>


# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configuración de la directiva SSL y SSL de extremo a extremo con Application Gateway mediante PowerShell

## <a name="overview"></a>Información general

Application Gateway admite el cifrado de extremo a extremo del tráfico. Para ello, lo que hace es terminar la conexión SSL en la puerta de enlace de aplicaciones. La puerta de enlace aplica entonces las reglas de enrutamiento al tráfico, vuelve a cifrar el paquete y lo reenvía al back-end adecuado según las reglas de enrutamiento definidas. Cualquier respuesta del servidor web pasa por el mismo proceso en su regreso al usuario final.

Otra característica que admite la puerta de enlace de aplicaciones es la deshabilitación de determinadas versiones del protocolo SSL. Application Gateway admite la deshabilitación de la siguiente versión del protocolo: TLSv1.0, TLSv1.1 y TLSv1.2.

![imagen de escenario][scenario]

## <a name="scenario"></a>Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante SSL de extremo a extremo con PowerShell.

En este escenario:

- Creará un grupo de recursos llamado "appgw-rg".
- Creará una red virtual denominada "appgwvnet" con un bloque CIDR reservado de 10.0.0.0/16.
- Creará dos subredes llamadas "appgwsubnet" y "appsubnet".
- Creará una puerta de enlace de aplicaciones pequeña que admita el cifrado SSL de extremo a extremo y deshabilite determinados protocolos SSL.

## <a name="before-you-begin"></a>Antes de empezar

Para configurar SSL de extremo a extremo con una puerta de enlace de aplicaciones, hacen falta certificados para la puerta de enlace y los servidores back-end. El certificado de puerta de enlace se usa para cifrar y descifrar el tráfico que se le envía a través de SSL. El certificado de puerta de enlace debe estar en formato de Intercambio de información personal (.pfx). Este formato de archivo permite la exportación de la clave privada, necesaria para que la puerta de enlace de aplicaciones realice el cifrado y descifrado del tráfico. 

Para el cifrado SSL de extremo a extremo, el back-end debe estar en la lista de permitidos junto con la puerta de enlace de aplicaciones. Para ello, se carga el certificado público de los back-ends en la puerta de enlace de aplicaciones. De esta manera se garantiza que la puerta de enlace de aplicaciones solo se comunica con instancias de back-end conocidas, lo que asegura la comunicación de extremo a extremo. Este proceso se describe en los pasos siguientes:

## <a name="create-the-resource-group"></a>Crear el grupo de recursos

Esta sección le lleva por la creación de un grupo de recursos que contiene la puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Inicie sesión en la cuenta de Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Seleccione la suscripción que se usará en este escenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Paso 3

Cree un grupo de recursos (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se crea una red virtual y dos subredes. Una subred se usa para alojar la puerta de enlace de aplicaciones. La otra subred se usa para los servidores back-end que hospedan la aplicación web.

### <a name="step-1"></a>Paso 1

Asigne un intervalo de direcciones para la subred de la puerta de enlace de aplicaciones propiamente dicha.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Paso 2

Asigne un intervalo de direcciones para el grupo de direcciones de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Paso 3

Cree una red virtual con las subredes definidas en los pasos anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Paso 4

Recupere el recurso de red virtual y los recursos de subred que se usarán en los pasos siguientes:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso de IP pública para la puerta de enlace de aplicaciones. Esta dirección IP pública se usa en un paso posterior.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application Gateway no admite el uso de una dirección IP pública creada con una etiqueta de dominio definida. Solo se admite una dirección IP pública con una etiqueta de dominio creada dinámicamente. Si necesita un nombre DNS descriptivo para la puerta de enlace de aplicaciones, se recomienda usar un registro CNAME como alias.

## <a name="create-an-application-gateway-configuration-object"></a>Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones de la aplicación. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones. Esta configuración determina la subred que usará Application Gateway. Cuando se inicia Application Gateway, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Paso 2

Cree una configuración IP de front-end. Esta configuración asigna una dirección IP pública o privada al front-end de la puerta de enlace de aplicaciones. El paso siguiente asocia la dirección IP pública del paso anterior con la configuración IP de front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Paso 3

Configure el grupo de direcciones IP de back-end con las direcciones IP de los servidores web de back-end. Estas direcciones IP son las direcciones que reciben el tráfico de red procedente del punto de conexión de la IP del front-end. Reemplazará las siguientes direcciones IP para agregar sus propios puntos de conexión de dirección IP de la aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nombre de dominio completo (FQDN) es también un valor válido en lugar de una dirección IP para los servidores back-end.

### <a name="step-4"></a>Paso 4

Configure el puerto IP de front-end para el punto de conexión de IP pública. Este puerto es el puerto al que se conectan los usuarios finales.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Paso 5

Configure el certificado de la puerta de enlace de aplicaciones. Este certificado se usa para descifrar y volver a cifrar el tráfico de la puerta de enlace de aplicaciones.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] En este ejemplo se configura el certificado que se usa para la conexión SSL. Es preciso que el certificado tenga el formato .pfx y que la contraseña tenga entre 4 y 12 caracteres.

### <a name="step-6"></a>Paso 6

Cree el agente de escucha HTTP para la puerta de enlace de aplicaciones. Asigne la configuración IP de front-end, el puerto y el certificado SSL que se usarán.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Paso 7

Cargue el certificado que se usará en los recursos del grupo de back-end habilitado para SSL.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] El certificado proporcionado en este paso debe ser la clave pública del certificado pfx presente en el back-end. En este paso se coloca el back-end en la lista de permitidos con la puerta de enlace de aplicaciones. 

### <a name="step-8"></a>Paso 8

Configure los valores HTTP de back-end de la puerta de enlace de aplicaciones. Asigne el certificado cargado en el paso anterior a la configuración HTTP.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Paso 9:

Cree una regla de enrutamiento del equilibrador de carga que configure el comportamiento del equilibrador de carga. En este ejemplo, se crea una regla básica de operaciones por turnos.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Paso 10

Configure el tamaño de la instancia de la puerta de enlace de aplicaciones.  Los tamaños disponibles son **Standard\_Small**, **Standard\_Medium** y **Standard\_Large**.  Para la capacidad, los valores disponibles son de 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Para las pruebas se puede elegir 1 en Número de instancias. Es importante saber que el SLA no cubre ningún número de instancias que esté por debajo de las dos instancias y, por consiguiente, no se recomienda. Las puertas de enlace pequeñas se deben usar para pruebas de desarrollo, no con fines de producción.

### <a name="step-11"></a>Paso 11

Configure la directiva SSL que se usará en la puerta de enlace de aplicaciones. Application Gateway admite la posibilidad de deshabilitar determinadas versiones del protocolo SSL.

Los valores siguientes son una lista de versiones de protocolo que se pueden deshabilitar.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

En el ejemplo siguiente se deshabilita TLSv1\_0 y TLSv1\_1

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Con todos los pasos anteriores, cree la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones es un proceso de ejecución largo.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Deshabilitación de las versiones de protocolo SSL en una puerta de enlace de aplicaciones existente

Los pasos anteriores le llevan por la creación de una aplicación con SSL de extremo a extremo y la deshabilitación de determinadas versiones del protocolo SSL. En el ejemplo siguiente se deshabilitan determinadas directivas SSL en una puerta de enlace de aplicaciones existente.

### <a name="step-1"></a>Paso 1

Recupere la puerta de enlace de aplicaciones que se actualizará.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Paso 2

Defina una directiva SSL. En el ejemplo siguiente, TLSv1.0 está deshabilitado.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0 -ApplicationGateway $gw

### <a name="step-3"></a>Paso 3

Por último, actualice la puerta de enlace. Es importante tener en cuenta que este último paso es una tarea de ejecución prolongada. Cuando termine, SSL de extremo a extremo está configurado en la puerta de enlace de aplicaciones.

    $gw | Set-AzureRmApplicationGateway

## <a name="next-steps"></a>Pasos siguientes

Aprenda sobre la protección de la seguridad de las aplicaciones web con el firewall de aplicaciones web mediante Application Gateway en [Información general sobre el firewall de aplicaciones web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png


<!--HONumber=Oct16_HO2-->


