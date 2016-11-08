---
title: Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure | Microsoft Docs
description: Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure mediante el Administrador de recursos de Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: gwallace

---
# Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure
Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies y descarga SSL (Capa de sockets seguros).

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
> * [CLI de Azure](application-gateway-create-gateway-cli.md)
> 
> 

Este artículo le guiará por los pasos necesarios para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones.

> [!IMPORTANT]
> Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: el Administrador de recursos y el clásico. Antes de trabajar con los recursos de Azure asegúrese de que conoce los [modelos de implementación y las herramientas](../azure-classic-rm.md). Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este documento describe la creación de una puerta de enlace de aplicaciones con Azure Resource Manager. Para utilizar la versión clásica, vaya a [Creación, inicio o eliminación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).
> 
> 

## Antes de empezar
1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Si tiene una red virtual existente, seleccione una subred vacía existente o cree una subred en la red virtual existente, únicamente para uso de la puerta de enlace de aplicaciones. No se puede implementar la puerta de enlace de la aplicación en una red virtual diferente de los recursos que se van a implementar detrás de la puerta de enlace de aplicaciones.
3. Los servidores que configure para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?
* **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
* **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
* **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
* **Agente de escucha**: tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
* **Regla**: enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico que llegue a un agente de escucha concreto.

## Creación de una puerta de enlace de aplicaciones
La diferencia entre el uso del Portal de Azure clásico y Azure Resource Manager es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con Resource Manager, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.

A continuación se muestran los pasos necesarios para crear una puerta de enlace de aplicaciones.

## Creación de un grupo de recursos para el Administrador de recursos
Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### Paso 1
Inicie sesión en Azure.

    Login-AzureRmAccount

Se le solicita que se autentique con sus credenciales.

### Paso 2
Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription

### Paso 3
Elección de la suscripción de Azure que se va a usar.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Paso 4
Cree un grupo de recursos (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

> [!NOTE]
> Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de un sondeo personalizado para la Puerta de enlace de aplicaciones de Azure (clásica) mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).
> 
> 

## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones
En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos.

### Paso 1
Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Paso 2
Cree una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### Paso 3
Asigne una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones.

    $subnet=$vnet.Subnets[0]

## Creación de una dirección IP pública para la configuración del front-end
Cree un recurso IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región West US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones de la aplicación. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### Paso 1
Cree una configuración de IP de puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia toma una dirección IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### Paso 2
Configure el grupo de direcciones IP del back-end denominado "pool01" con las direcciones IP "134.170.185.46,134.170.188.221,134.170.185.50". Estas direcciones IP son las direcciones que reciben el tráfico de red procedente del punto de conexión de la IP del front-end. Reemplaza las direcciones IP anteriores para agregar sus propios puntos de conexión de direcciones IP de la aplicación.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### Paso 3
Configure la opción de la puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### Paso 4
Configure el puerto IP del front-end denominado "frontendport01" para el punto de conexión de la IP pública.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Paso 5
Cree la configuración de direcciones IP front-end denominada "fipconfig01" y asocie la dirección IP pública con dicha configuración.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Paso 6
Cree el nombre del agente de escucha "listener01" y asocie el puerto front-end con la configuración de direcciones IP del front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Paso 7
Cree la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Paso 8
Configure el tamaño de la instancia de la Puerta de enlace de aplicaciones.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

> [!NOTE]
> El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.
> 
> 

## Creación de una puerta de enlace de aplicaciones mediante New-AzureRmApplicationGateway
Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### Paso 9:
Recupere los detalles sobre DNS y VIP de la puerta de enlace de aplicaciones del recurso de IP pública vinculado a la puerta de enlace de aplicaciones.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## Eliminación de una puerta de enlace de aplicaciones
Para eliminar una Puerta de enlace de aplicaciones, siga estos pasos:

### Paso 1
Obtenga el objeto de puerta de enlace de aplicaciones y asócielo a una variable "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2
Utilice **Stop-AzureRmApplicationGateway** para detener la puerta de enlace de aplicaciones.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Una vez que la puerta de enlace de aplicaciones esté en estado detenido, utilice el cmdlet **Remove-AzureRmApplicationGateway** para quitar el servicio.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



> [!NOTE]
> Se puede usar el modificador **-force** para suprimir el mensaje de confirmación de eliminación.
> 
> 

Para comprobar que el servicio se ha quitado, se puede usar el cmdlet **Get-AzureRmApplicationGateway**. Este paso no es necesario.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Pasos siguientes
Si desea configurar la descarga de SSL, consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica](application-gateway-ssl.md).

Si quiere configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

* [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0907_2016-->