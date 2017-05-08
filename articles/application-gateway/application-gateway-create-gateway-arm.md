---
title: "Creación y administración de Azure Application Gateway: PowerShell | Microsoft Docs"
description: "Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure mediante el Administrador de recursos de Azure"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla de Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local.
Application Gateway proporciona numerosas características del Controlador de entrega de aplicaciones (ADC), entre las que se incluyen el equilibrio de carga HTTP, la afinidad de sesiones basada en cookies, la descarga SSL (Capa de sockets seguros), los sondeos personalizados sobre el estado, la compatibilidad con multisitio, etc.

Para obtener una lista completa de las características admitidas, visite [Introducción a Application Gateway](application-gateway-introduction.md)

Este artículo le guiará por los pasos necesarios para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones.

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: Azure Resource Manager y el clásico. Antes de trabajar con los recursos de Azure asegúrese de que conoce los [modelos de implementación y las herramientas](../azure-classic-rm.md) . Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este documento describe la creación de una puerta de enlace de aplicaciones con Azure Resource Manager. Para utilizar la versión clásica, vaya a [Creación, inicio o eliminación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
1. Si tiene una red virtual existente, seleccione una subred vacía existente o cree una subred en la red virtual existente, únicamente para uso de la puerta de enlace de aplicaciones. No se puede implementar la puerta de enlace de la aplicación en una red virtual diferente de los recursos que se van a implementar detrás de la puerta de enlace de aplicaciones.
1. Los servidores que configure para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## <a name="what-is-required-to-create-an-application-gateway"></a>¿Qué se necesita para crear una Puerta de enlace de aplicaciones?

* **Grupo de servidores back-end**: lista de direcciones IP, FQDN y NIC de los servidores back-end. Si se utilizan direcciones IP, estas deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha** : tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla** : enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico que llegue a un agente de escucha concreto.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

La diferencia entre Azure Portal y Azure Resource Manager es el orden en el que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con Resource Manager, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.

A continuación se muestran los pasos necesarios para crear una puerta de enlace de aplicaciones.

## <a name="create-a-resource-group-for-resource-manager"></a>Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

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
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado **appgw-RG** y la ubicación **Oeste de EE. UU.**

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md) .

## <a name="create-a-virtual-network-and-a-subnet"></a>Creación de una red virtual y una subred

En el ejemplo siguiente se muestra cómo crear una red virtual con Resource Manager. En este ejemplo se crea una red virtual para la puerta de enlace de aplicaciones. Application Gateway requiere su propia subred. Por este motivo, la subred creada para Application Gateway es más pequeña que el espacio de direcciones de la red virtual. El uso de una subred más pequeña permite configurar en la misma subred otros recursos, incluidos, entre otros, los servidores web.

### <a name="step-1"></a>Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual. En este paso se crea el objeto de configuración de subred para Application Gateway, que se utiliza en el ejemplo siguiente.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Paso 2

Cree una red virtual denominada **appgwvnet** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24. En este paso se completa la configuración de la red virtual con una sola subred en la que residirá Application Gateway.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Paso 3

Asigne la variable de subred para los pasos siguientes. En un paso posterior, esta variable se transfiere al cmdlet `New-AzureRMApplicationGateway`.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Cree un recurso IP público **publicIP01** en el grupo de recursos **appgw-rg** para la región Oeste de EE. UU. Application Gateway puede usar una dirección IP pública, una dirección IP interna o ambas para recibir solicitudes de equilibrio de carga.  En este ejemplo, solo se usa una dirección IP pública. En el ejemplo siguiente, no hay ningún nombre DNS configurado para la creación de la dirección IP pública.  Application Gateway no admite nombres DNS personalizados en direcciones IP públicas.  Si se requiere un nombre personalizado para el punto de conexión público, debe crearse un registro CNAME para señalar al nombre DNS generado automáticamente para la dirección IP pública.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> Se asigna una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## <a name="create-the-application-gateway-configuration-objects"></a>Creación de objetos de configuración de la puerta de enlace de aplicaciones

Se deben haber definido todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada **gatewayIP01**. Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Paso 2

Configure el grupo de direcciones IP del back-end denominado **pool01** con las direcciones IP para **pool1**. Estas direcciones IP son las direcciones IP de los recursos que hospedan la aplicación web que Application Gateway protegerá. Mediante sondeos, que pueden ser básicos o personalizados, se validan todos estos miembros del grupo back-end para comprobar que se encuentran en buen estado.  A continuación, el tráfico se enruta hacia ellos cuando las solicitudes llegan a Application Gateway. Varias reglas de Application Gateway pueden usar los grupos back-end, lo que significa que un grupo back-end podría usarse para varias aplicaciones web que residan en el mismo host.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

En este ejemplo, hay dos grupos de back-end para enrutar el tráfico de red según la ruta de dirección URL. Un grupo recibe el tráfico de la ruta de dirección URL "/video" y otro lo recibe de la ruta "/image". Reemplace las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación.

### <a name="step-3"></a>Paso 3

Configure la opción de la puerta de enlace de aplicaciones **poolsetting01** para el tráfico de red con carga equilibrada del grupo de back-end. Cada grupo de back-end puede tener su propia configuración de grupo de back-end.  Las reglas usan la configuración de HTTP de back-end para enrutar el tráfico hacia los miembros del grupo back-end correctos. La configuración de HTTP de back-end determina el protocolo y el puerto que se usa al enviar tráfico a los miembros del grupo back-end. Las sesiones basadas en cookies también dependen de la configuración de HTTP de back-end.  Si está habilitada, la afinidad de la sesión basada en cookies envía tráfico al mismo servidor back-end como solicitudes anteriores para cada paquete.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Paso 4

Configuración del puerto front-end de una puerta de enlace de aplicaciones Un agente de escucha usa el objeto de configuración de puerto front-end para definir en qué puerto escucha Application Gateway el tráfico en el agente de escucha.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Paso 5

Configuración de la dirección IP de front-end con el punto de conexión de IP pública Un agente de escucha usa el objeto de configuración de la dirección IP de front-end para relacionar la dirección IP con orientación externa con el agente de escucha.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Paso 6

Configuración del agente de escucha En este paso se configura el agente de escucha para la dirección IP pública y el puerto que se utiliza para recibir el tráfico de red entrante. En el ejemplo siguiente se toma la configuración de la dirección IP de front-end y la configuración del puerto front-end que se han configurado anteriormente, junto con un protocolo (http o https), y se configura el agente de escucha. En este ejemplo, el agente de escucha escucha el tráfico HTTP en el puerto 80 en la dirección IP pública que se creó anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Paso 7

Cree la regla de enrutamiento del equilibrador de carga denominada **rule01** que configura el comportamiento del equilibrador de carga. La configuración del grupo back-end, el agente de escucha y el grupo back-end creado en los pasos anteriores componen la regla. Según los criterios definidos, el tráfico se enruta al servidor back-end adecuado.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Paso 8

Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> El valor predeterminado de **InstanceCount** es 2, con un valor máximo de 10. El valor predeterminado de **GatewaySize** es Medium. Se puede elegir entre **Standard_Small**, **Standard_Medium** y **Standard_Large**.

## <a name="create-the-application-gateway"></a>Creación de la puerta de enlace de aplicaciones

Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Recupere los detalles sobre DNS y VIP de la puerta de enlace de aplicaciones del recurso de IP pública vinculado a la puerta de enlace de aplicaciones.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Eliminación de la puerta de enlace de aplicaciones

Para eliminar una Puerta de enlace de aplicaciones, siga estos pasos:

### <a name="step-1"></a>Paso 1

Obtenga el objeto de puerta de enlace de aplicaciones y asócielo a una variable `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Paso 2

Use `Stop-AzureRmApplicationGateway` para detener la puerta de enlace de aplicaciones.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Cuando el estado de la puerta de enlace de aplicaciones sea detenido, use el cmdlet `Remove-AzureRmApplicationGateway` para quitar el servicio.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> Se puede usar el modificador **-force** para suprimir el mensaje de confirmación de eliminación.

Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureRmApplicationGateway`. Este paso no es necesario.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Obtención del nombre DNS de una puerta de enlace de aplicaciones

Una vez creada la puerta de enlace, el siguiente paso es configurar el front-end para la comunicación. Cuando se utiliza una dirección IP pública, la puerta de enlace de aplicaciones requiere un nombre DNS asignado dinámicamente, que no es descriptivo. Para asegurarse de que los usuarios finales puedan llegar a la Application Gateway, se puede utilizar un registro CNAME para que apunte al punto de conexión público de la Application Gateway. [Configuración de un nombre de dominio personalizado en Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para encontrar el nombre DNS creado dinámicamente, recupere los detalles de Application Gateway y su nombre DNS o IP asociados mediante el elemento PublicIPAddress asociado a Application Gateway. El nombre DNS de la puerta de enlace de aplicaciones se debe utilizar para crear un registro CNAME, que apunta las dos aplicaciones web a este nombre DNS. No se recomienda el uso de registros A, ya que la VIP puede cambiar al reiniciarse la puerta de enlace de aplicaciones.

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

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, complete los pasos siguientes:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una instancia de Application Gateway para la descarga de SSL](application-gateway-ssl.md).

Si quiere configurar una instancia de Application Gateway para usarla con un equilibrador de carga interno, consulte [Creación de una instancia de Application Gateway con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea más información acerca de las opciones de equilibrio de carga en general, visite:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


