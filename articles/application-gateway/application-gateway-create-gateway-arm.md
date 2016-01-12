<properties 
   pageTitle="Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure mediante el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure

Puerta de enlace de aplicaciones es el equilibrador de carga de nivel 7. Ofrece conmutación por error, enrutamiento del rendimiento de solicitudes HTTP entra distintos servidores, ya se que se encuentren en la nube o en una implementación local. La puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesión basado en cookies, descarga SSL.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


Este artículo le guiará por los pasos necesarios para crear y configurar, iniciar y eliminar una puerta de enlace de aplicaciones.


>[AZURE.IMPORTANT]Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: el Administrador de recursos y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las pestañas de la parte superior de este artículo. Este documento tratará de la creación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure. Para usar la versión básica, vaya a [crear una implementación clásica de la puerta de enlace de aplicaciones mediante PowerShell](application-gateway-create-gateway.md).



## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente de la sección **Windows PowerShell** de la [página de descarga](http://azure.microsoft.com/downloads/).
2. Creará una red virtual y subred para la Puerta de enlace de aplicaciones. Asegúrese de que no hay máquinas virtuales o de que las implementaciones de nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configurará para usar la Puerta de enlace de aplicaciones deben existir o tener sus extremos creados en la red virtual o tener una VIP/IP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?
 

- **Grupo de servidores back-end:** la lista de direcciones IP de los servidores back-end. Las direcciones IP mostradas deben pertenecer a la subred de la red virtual o ser una VIP/IP pública. 
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público abierto en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha:** la escucha tiene un puerto front-end, un protocolo (Http o Https, estos distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). 
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla *básica*. La regla *básica* es la distribución de carga round robin.


 
## Creación de una Puerta de enlace de aplicaciones

La diferencia entre el uso clásico del portal clásico de Azure y el Administrador de recursos de Azure será el orden en que creará la Puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con el Administrador de recursos, todos los elementos que componen una Puerta de enlace de aplicaciones se configurarán individualmente y, a continuación, se unirán para crear el recurso Puerta de enlace de aplicaciones.


A continuación se proporcionan los pasos necesarios para crear una Puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual, una subred y una dirección IP pública para la Puerta de enlace de aplicaciones
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Creación de un recurso para la Puerta de enlace de aplicaciones


## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en Uso de [Windows Powershell con el Administrador de recursos](powershell-azure-resource-manager.md).

### Paso 1

		Login-AzureRmAccount



### Paso 2

Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription 

Se le pedirá que se autentique con sus credenciales.<BR>

### Paso 3 

Elija qué suscripción de Azure va a utilizar.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Paso 4

Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una Puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos llamado "appgw-RG" en la ubicación "West US".


>[AZURE.NOTE]Si necesita configurar un sondeo personalizado para Puerta de enlace de aplicaciones, consulte el artículo [Creación, inicio o eliminación de una puerta de enlace de aplicaciones](application-gateway-create-probe-ps.md). Consulte también [Application gateway health monitoring overview](application-gateway-probe-overview.md) para más información.



## Creación de una red virtual y una subred para la Puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos:

### Paso 1	
	
Asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se usará para crear una red virtual

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Paso 2	

Crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región West US con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Paso 3
	
Asigna una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones.

	$subnet=$vnet.Subnets[0]

## Creación de una dirección IP pública para la configuración del front-end

Crea un recurso IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región West US.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones de la aplicación. En los pasos siguientes se crean los elementos de configuración necesarios para un recurso de Puerta de enlace de aplicaciones.

### Paso 1

Crea una configuración de IP de puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, toma una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tendrá una dirección IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Paso 2

Este paso configurará el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50". Serán las direcciones IP que reciben el tráfico de red procedente del extremo IP del front-end. Deberá reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de la aplicación.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Paso 3

Configura la opción Puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada en el grupo de back-end.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Paso 4

Configura el puerto IP del front-end, en este caso llamado "frontendport01", para el extremo IP público.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### Paso 5

Crea la configuración de direcciones IP front-end denominada "fipconfig01" y asocia la dirección IP pública con dicha configuración.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Paso 6

Crea el nombre de agente de escucha "listener01" y asocia el puerto front-end con la configuración de direcciones IP front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Paso 7 

Crea la regla de enrutamiento del equilibrador de carga denominado "rule01" mediante la configuración del comportamiento del equilibrador de carga.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Paso 8

Configura el tamaño de la instancia de la Puerta de enlace de aplicaciones

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de Puerta de enlace de aplicaciones con el cmdlet New-AzureRmApplicationGateway

Crea una Puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Eliminación de una Puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de aplicaciones, deberá hacer lo siguiente en orden:

1. Use el cmdlet `Stop-AzureRmApplicationGateway` para detener la puerta de enlace. 
2. Utilice el cmdlet `Remove-AzureRmApplicationGateway` para quitar la puerta de enlace.
3. Compruebe que se quitó la puerta de enlace con el cmdlet `Get-AzureRmApplicationGateway`.

### Paso 1

Obtenga el objeto de la Puerta de enlace de aplicaciones y asócielo a una variable "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2
	 
Use `Stop-AzureRmApplicationGateway` para detener la Puerta de enlace de aplicaciones:

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Cuando el estado de la Puerta de enlace de aplicaciones sea Detenido, use el cmdlet `Remove-AzureRmApplicationGateway` para quitar el servicio.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]Se puede usar el elemento opcional "-force" para suprimir el mensaje de confirmación


Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureRmApplicationGateway`. Este paso no es necesario.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	
## Pasos siguientes

Si desea configurar la descarga SSL, consulte [Configuración de una puerta de enlace de aplicaciones para descarga SSL](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de aplicaciones para usarla con ILB, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->