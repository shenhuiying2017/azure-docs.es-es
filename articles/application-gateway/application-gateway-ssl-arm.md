---
title: Configuración de una puerta de enlace de aplicaciones para la descarga SSL con el Administrador de recursos de Azure | Microsoft Docs
description: En esta página se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el Administrador de recursos de Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2016
ms.author: gwallace

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure
> [!div class="op_single_selector"]
> -[Azure Portal](application-gateway-ssl-portal.md)
> -[PowerShell de Azure Resource Manager](application-gateway-ssl-arm.md)
> -[Azure Classic PowerShell](application-gateway-ssl.md)
> 
> 

 Puerta de enlace de aplicaciones de Azure puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="before-you-begin"></a>Antes de empezar
1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Cree una red virtual y una subred para la puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de una red virtual.
3. Los servidores que configure para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## <a name="what-is-required-to-create-an-application-gateway?"></a>¿Qué se necesita para crear una Puerta de enlace de aplicaciones?
* **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha:** tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se dirigirá el tráfico cuando llega a un agente de escucha concreto. Actualmente, solo se admite la regla *básica* . La regla *básica* es la distribución de carga round robin.

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a *Https* (con distinción entre mayúsculas y minúsculas). El elemento **SslCertificate** se agrega a **HttpListener** con el valor de la variable configurado para el certificado SSL. El puerto front-end debe actualizarse al 443.

**Para habilitar la afinidad basada en cookies**: se puede configurar una puerta de enlace de aplicaciones para asegurarse de que las solicitudes de una sesión de cliente siempre se dirigen a la misma máquina virtual de la granja de servidores web. Este escenario se realiza mediante la inyección de una cookie de la sesión que permita a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *Habilitado* en el elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones
La diferencia entre el uso del modelo de implementación clásica de Azure y el de Azure Resource Manager es el orden en que se crea una puerta de enlace de aplicaciones y los elementos que es preciso configurar.

Con Resource Manager, todos los componentes de una puerta de enlace de aplicaciones se configuran individualmente y, luego, se unen para crear un recurso de la puerta de enlace de aplicaciones.

Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Crear un recurso de la puerta de enlace de aplicaciones

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos
Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos de Azure. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1
    Login-AzureRmAccount

### <a name="step-2"></a>Paso 2
Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

Se le solicita que se autentique con sus credenciales.<BR>

### <a name="step-3"></a>Paso 3
Elección de la suscripción de Azure que se va a usar. <BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Paso 4
Cree un grupo de recursos (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta configuración se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.
En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos:

### <a name="step-1"></a>Paso 1
    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

En este ejemplo se asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se va a usar para crear una red virtual.

### <a name="step-2"></a>Paso 2
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

En este ejemplo se crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

### <a name="step-3"></a>Paso 3
    $subnet = $vnet.Subnets[0]

En este ejemplo se asigna el objeto de subred a la variable $subnet para los siguientes pasos.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end
    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

En este ejemplo se crea un recurso de IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región oeste de EE. UU.

## <a name="create-an-application-gateway-configuration-object"></a>Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
### <a name="step-1"></a>Paso 1
    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

En este ejemplo se crea una configuración de la IP de la puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

### <a name="step-2"></a>Paso 2
    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

En este ejemplo se configura el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221 y 134.170.185.50". Estos valores las direcciones IP que reciben el tráfico de red procedente del punto de conexión de la IP del front-nd. Reemplace las direcciones IP del ejemplo anterior por las de los puntos de conexión de la aplicación web.

### <a name="step-3"></a>Paso 3
    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

En este ejemplo se configura la opción de la puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

### <a name="step-4"></a>Paso 4
    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

En este ejemplo se configura el puerto IP del front-end denominado "frontendport01" para el punto de conexión de la IP pública.

### <a name="step-5"></a>Paso 5
    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

En este ejemplo se configura el certificado que se usa para la conexión SSL. Es preciso que el certificado tenga el formato .pfx y que la contraseña tenga entre 4 y 12 caracteres.

### <a name="step-6"></a>Paso 6
    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

En este ejemplo se crea la configuración de la IP de front-end denominada "fipconfig01" y asocia la dirección IP pública con dicha configuración.

### <a name="step-7"></a>Paso 7
    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


En este ejemplo se crea el nombre de agente de escucha "listener01" y se asocia el puerto de front-end con la configuración de la IP del front-end y el certificado.

### <a name="step-8"></a>Paso 8
    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

En este ejemplo se crea la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

### <a name="step-9"></a>Paso 9:
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

En este ejemplo se configura el tamaño de instancia de la puerta de enlace de aplicaciones.

> [!NOTE]
> El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard_Small, Standard_Medium y Standard_Large.
> 
> 

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creación de una puerta de enlace de aplicaciones con New-AzureApplicationGateway
    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

En este ejemplo se crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".

## <a name="next-steps"></a>Pasos siguientes
Si desea configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno (ILB), consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Oct16_HO2-->


