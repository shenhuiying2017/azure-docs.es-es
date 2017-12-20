---
title: Crear una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL | Microsoft Docs
description: "En esta página se proporcionan instrucciones para crear y configurar una puerta de enlace de aplicaciones de Azure mediante reglas de enrutamiento de direcciones URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f0b085ebf922cd5b14acd91bf86b9262a6921e9e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Crear una puerta de enlace de aplicaciones mediante el enrutamiento basado en rutas de acceso

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI de Azure 2.0](application-gateway-create-url-route-cli.md)

El enrutamiento basado en rutas de acceso asocia rutas en función de la ruta de acceso de la dirección URL de una solicitud HTTP. Comprueba si hay una ruta a un grupo de servidores back-end configurado para la dirección URL indicada en la puerta de enlace de aplicaciones y, luego, envía el tráfico de red al grupo de servidores back-end definido. Un uso común del enrutamiento basado en URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores de back-end.

Azure Application Gateway tiene dos tipos de reglas: de enrutamiento básico y de enrutamiento basado en rutas de acceso. El tipo básico proporciona un servicio round robin para los grupos de servidores back-end. El enrutamiento basado en rutas de acceso, además de la distribución round robin, también usa el patrón de ruta de acceso de la dirección URL de solicitud para elegir el grupo de servidores back-end.

## <a name="scenario"></a>Escenario

En el ejemplo siguiente, Application Gateway atiende el tráfico de contoso.com con dos grupos de servidores back-end: un grupo de servidores de vídeos y un grupo de servidores de imágenes.

Las solicitudes de http://contoso.com/image* se enrutan al grupo de servidores de imágenes (**pool1**), mientras que las solicitudes de http://contoso.com/video* se enrutan al grupo de servidores de vídeos (**pool2**). Si ninguno de los patrones de ruta de acceso coincide, se seleccionará un grupo de servidores predeterminado (**pool1**).

![Ruta de dirección URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Cree una red virtual y una subred para una puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube use esa subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Asegúrese de que los servidores agregados al grupo de servidores back-end de la puerta de enlace de aplicaciones existen o de que sus puntos de conexión se hayan creado en la red virtual o con una dirección IP/IP virtual pública asignada.

## <a name="requirements-to-create-an-application-gateway"></a>Requisitos para crear una puerta de enlace de aplicaciones

* **Back-end server pool** (Grupo de servidores back-end): lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o deben ser una dirección IP o IP virtual pública.
* **Configuración del grupo de servidores back-end:** Puerto, protocolo y afinidad basada en cookies. Están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** Puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha**: Tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla**: Enlaza el agente de escucha y el grupo de servidores back-end y define el grupo de servidores back-end al que se debe dirigir el tráfico cuando llega a un agente de escucha.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

La diferencia entre usar el modelo clásico de implementación y el de Azure Resource Manager es el orden en el que se crea la puerta de enlace de aplicaciones y los elementos que se deben configurar.

Con Resource Manager, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de puerta de enlace de aplicaciones.

Siga estos pasos para crear una puerta de enlace de aplicaciones:

1. Cree un grupo de recursos para Resource Manager.
2. Cree una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones.
3. Cree un objeto de configuración de la puerta de enlace de aplicaciones.
4. Cree un recurso de Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Encontrará más información en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

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

Cree un grupo de recursos. (Si utiliza un grupo de recursos existente, puede omitir este paso).

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

También puede crear etiquetas para un grupo de recursos para una puerta de enlace de aplicaciones:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager requiere que los grupos de recursos especifiquen una ubicación predeterminada, que se usa para todos los recursos de ese grupo. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior hemos creado un grupo de recursos denominado "appgw-RG" con la ubicación "West US".

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, vaya a [Crear una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Vea [Información general sobre la supervisión de estado de Application Gateway](application-gateway-probe-overview.md) para más información.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager. En este ejemplo se crea una red virtual para la puerta de enlace de aplicaciones. Application Gateway necesita su propia subred. Por este motivo, la subred creada para la puerta de enlace de aplicaciones es más pequeña que el espacio de direcciones de la red virtual. Esto permite configurar en la misma subred otros recursos (por ejemplo, servidores web).

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual.  Así se crea el objeto de configuración de subred para la puerta de enlace de aplicaciones, que se usa en el ejemplo siguiente.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Paso 2

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24. En este paso se lleva a cabo la configuración de la red virtual con una sola subred en la que residirá la puerta de enlace de aplicaciones.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Paso 3

Asigne la variable de subred para los pasos siguientes. Esta variable se pasa al cmdlet `New-AzureRMApplicationGateway` en un paso posterior.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. Application Gateway puede usar una dirección IP pública, una dirección IP interna o ambas para recibir solicitudes de equilibrio de carga.  En este ejemplo se usa solo una dirección IP pública. En el siguiente ejemplo no hay ningún nombre DNS configurado para crear la dirección IP pública, ya que Application Gateway no admite los nombres DNS personalizados en las direcciones IP públicas.  Si se requiere un nombre personalizado para el punto de conexión público, cree un registro CNAME para que señale al nombre DNS generado automáticamente para la dirección IP pública.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-the-application-gateway-configuration"></a>Crear la configuración de la puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia Application Gateway, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo de direcciones IP de servidor back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Paso 2

Configure los grupos de direcciones IP de servidor back-end llamados **pool1** y **pool2** con las direcciones IP de **pool1** y **pool2**. Estas son las direcciones IP de los recursos que hospedan la aplicación web que la puerta de enlace de aplicaciones protegerá. Estos miembros del grupo de servidores back-end se validan para comprobar que están en buen estado mediante sondeos, que pueden ser básicos o personalizados. A continuación, el tráfico se enruta hacia ellos cuando las solicitudes llegan a Application Gateway. Los grupos de servidores back-end se pueden usar en varias reglas dentro de la puerta de enlace de aplicaciones. Esto significa que se puede usar un grupo de servidores back-end para varias aplicaciones web que residen en el mismo host.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

En este ejemplo hay dos grupos de servidores back-end que enrutan el tráfico de red según la ruta de dirección URL. Un grupo recibe el tráfico de la ruta de dirección URL "/video", mientras que el otro lo recibe de la ruta "/image". Reemplace las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación. 

### <a name="step-3"></a>Paso 3

Configure las opciones de la puerta de enlace de aplicaciones **poolsetting01** y **poolsetting02** para el tráfico de red con carga equilibrada del grupo de servidores back-end. En este ejemplo, se configuran distintas opciones de configuración para los grupos de back-end. Cada grupo de servidores back-end puede tener su propia configuración.  Las reglas usan la configuración de HTTP de los servidores back-end para enrutar el tráfico hacia los miembros del grupo de servidores back-end correctos. Esto determina el protocolo y el puerto empleados para enviar tráfico a los miembros del grupo de servidores back-end. Las sesiones basadas en cookies también dependen de la configuración de HTTP de los servidores back-end. Si está habilitada, la afinidad de sesión basada en cookies envía el tráfico al mismo servidor back-end que las solicitudes anteriores para cada paquete.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Paso 4

Configure la dirección IP de front-end con puntos de conexión de direcciones IP públicas. Un agente de escucha usa el objeto de configuración de la dirección IP de front-end para relacionar la dirección IP con orientación externa con el agente de escucha.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Paso 5

Configuración del puerto front-end de una puerta de enlace de aplicaciones El agente de escucha usa el objeto de configuración de puerto front-end para definir el puerto del agente de escucha en el que la puerta de enlace de aplicaciones escucha el tráfico.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Paso 6

Configure el agente de escucha para la dirección IP pública y el puerto empleados para recibir el tráfico de red entrante. En el ejemplo siguiente se toma la configuración de la dirección IP de front-end configurada anteriormente, la configuración del puerto front-end y un protocolo Http o Https (distinguen mayúsculas de minúsculas). Luego, se configura el agente de escucha. En este ejemplo, el agente escucha el tráfico HTTP en el puerto 80 en la dirección IP pública que se creó anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Paso 7

Configuración de rutas de acceso de reglas de URL para los grupos de back-end En este paso se configura la ruta de acceso relativa que usa Application Gateway y se define la asignación entre la ruta de dirección URL y el grupo de servidores back-end que se asigna para administrar el tráfico entrante.

> [!IMPORTANT]
> Cada ruta de acceso debe comenzar con una "/" y solo se permiten los asteriscos al final. Algunos ejemplos válidos son /xyz, /xyz*, o /xyz/*. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer "?" o "#", y esos caracteres no se permiten. 

En el ejemplo siguiente se crean dos reglas: una para la ruta de acceso "/image/" que enruta el tráfico al grupo **pool1** de servidores back-end y otra para la ruta de acceso "/video/", que enruta el tráfico al grupo **pool2** de servidores back-end. Estas reglas garantizan que el tráfico de cada conjunto de direcciones URL se enrute al servidor back-end. Por ejemplo, http://contoso.com/image/figure1.jpg se dirige al grupo **pool1** y http://contoso.com/video/example.mp4 se dirige al grupo **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Si la ruta de acceso no coincide con ninguna de las reglas de ruta de acceso predefinidas, la configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado. Por ejemplo, http://contoso.com/shoppingcart/test.html va al grupo **pool1**, ya que es el grupo predeterminado para el tráfico no coincidente.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Paso 8

Creación de una configuración de regla En este paso se configura la puerta de enlace de aplicaciones para usar el enrutamiento basado en ruta de dirección URL. La variable `$urlPathMap` definida en el paso anterior ahora se usa para crear la regla basada en la ruta de acceso. En este paso se asocia la regla con un agente de escucha y la asignación de ruta de acceso de dirección URL creada anteriormente.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Paso 9:

Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Cree una puerta de enlace de aplicaciones con todos los objetos de configuración de los pasos anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Obtención de un nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, deberá configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la instancia de Application Gateway requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios puedan llegar a la puerta de enlace de aplicaciones, puede usar un registro CNAME para que apunte al punto de conexión público de dicha puerta. Para más información, vea [Configuración de un nombre de dominio personalizado para un servicio en la nube de Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Para configurar el registro IP CNAME de front-end, recupere los detalles de la puerta de enlace de aplicaciones y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a Application Gateway. Use el nombre DNS de la puerta de enlace de aplicaciones para crear un registro CNAME. No se recomienda usar registros A, ya que la IP virtual podría cambiar al reiniciarse la puerta de enlace de aplicaciones.

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

Si quiere obtener información sobre la descarga de la Capa de sockets seguros (SSL), vea [Configuración de una puerta de enlace de aplicaciones para la descarga de SSL mediante Azure Resource Manager](application-gateway-ssl-arm.md).

