---
title: "Creación de una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL | Microsoft Docs"
description: "En esta página se proporcionan instrucciones para crear y configurar una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: jdial
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: 9af41bac2f073e5d3770ac17357306e1af86c3e6


---
# <a name="create-an-application-gateway-using-path-based-routing"></a>Creación de una puerta de enlace de aplicaciones mediante enrutamiento basado en rutas de acceso

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-url-route-arm-ps.md)
> 
> 

El enrutamiento basado en URL permite asociar rutas en función de la dirección URL de la solicitud HTTP. Comprueba si hay una ruta a un grupo de back-end configurado para las listas de direcciones URL en la puerta de enlace de aplicaciones y envía el tráfico de red al grupo de back-end definido. Un uso común del enrutamiento basado en URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores de back-end.

El enrutamiento basado en URL incorpora un nuevo tipo de regla en la puerta de enlace de aplicaciones. La puerta de enlace de aplicaciones tiene dos tipos de reglas: básicas y PathBasedRouting. El tipo de regla básica proporciona un servicio round robin mientras que el tipo PathBasedRouting, además de la distribución round robin, también tiene en cuenta el patrón de ruta de acceso de la URL de la solicitud durante la elección del grupo de back-end.

> [!IMPORTANT]
> PathPattern: la lista de patrones de ruta de acceso con los que se buscan coincidencias. Cada uno de ellos debe comenzar con / y el único lugar donde se permite un carácter "\*" es al final. Algunos ejemplos válidos son /xyz, /xyz* o /xyz/*. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer "?" o "#", y esos caracteres no se permiten. 
> 
> 

## <a name="scenario"></a>Escenario

En el ejemplo siguiente, la puerta de enlace de aplicaciones atiende el tráfico de contoso.com con dos grupos de servidores de back-end: el grupo de servidores de vídeo y el grupo de servidores de imagen.

Las solicitudes de http://contoso.com/image* se enrutan a un grupo de servidores de imágenes (pool1) y las de http://contoso.com/video* a un grupo de servidores de vídeos (pool2). Si ninguno de los patrones de ruta de acceso coincide, se selecciona un grupo de servidores predeterminado (pool1).

![ruta de dirección URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Tendrá que crear una red virtual y subred para la puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores agregados al grupo de back-end para usar la puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una Puerta de enlace de aplicaciones?

* **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla** : enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico que llegue a un agente de escucha concreto.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

La diferencia entre el uso del Portal de Azure clásico y Azure Resource Manager es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con Resource Manager, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.

Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Cree un grupo de recursos para el Administrador de recursos.
2. Cree una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones.
3. Cree un objeto de configuración de la Puerta de enlace de aplicaciones.
4. Cree un recurso de Puerta de enlace de aplicaciones.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Paso 1

Inicie sesión en Azure.

```powershell
Login-AzureRmAccount
```

Se le solicita que se autentique con sus credenciales.<BR>

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
New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

También puede crear etiquetas para un grupo de recursos de la puerta de enlace de aplicaciones:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de un sondeo personalizado para la Puerta de enlace de aplicaciones de Azure (clásica) mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md) .
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos.

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Paso 2

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Paso 3

Asignación de una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-application-gateway-configuration"></a>Creación de una configuración de puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Paso 2

Configure el grupo de direcciones IP de back-end llamado **pool01** y **pool2** con las direcciones IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** para **pool1** y **134.170.186.46**, **134.170.189.221**, **134.170.186.50** para **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50
```

En este ejemplo, hay dos grupos de back-end para enrutar el tráfico de red según la ruta de dirección URL. Un grupo recibe el tráfico de la ruta de dirección URL "/video" y otro lo recibe de la ruta "/image". Reemplace las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación. 

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
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Paso 6

Configuración del agente de escucha En este paso se configura el agente de escucha para la dirección IP pública y el puerto que se utiliza para recibir el tráfico de red entrante. 

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Paso 7

Configuración de rutas de acceso de reglas de URL para los grupos de back-end En este paso, se configura la ruta de acceso relativa que utiliza la puerta de enlace de aplicaciones para definir la asignación entre la ruta de dirección URL y el grupo de back-end que se asigna para administrar el tráfico entrante.

En el ejemplo siguiente se crean dos reglas: una para la ruta de acceso "/image/" que enruta el tráfico al grupo "pool1" de back-end y otra para la ruta de acceso "/video/" que enruta el tráfico al grupo "pool2" de back-end.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

La configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado si la ruta de acceso no coincide con ninguna de las reglas de ruta de acceso predefinidas. 

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Paso 8

Creación de una configuración de regla En este paso se configura la puerta de enlace de aplicaciones para usar el enrutamiento basado en ruta de dirección URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Paso 9:

Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Cree una puerta de enlace de aplicaciones con todos los objetos de configuración de los pasos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
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

Si quiere obtener información sobre la descarga de Capa de sockets seguros (SSL), consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Resource Manager](application-gateway-ssl-arm.md).




<!--HONumber=Nov16_HO3-->


