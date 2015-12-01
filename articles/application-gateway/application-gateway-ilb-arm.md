<properties 
   pageTitle="Creación de una Puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure con un equilibrador de carga interno (ILB) para el Administrador de recursos de Azure"
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


# Creación de una Puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

Puerta de enlace de aplicaciones se puede configurar con una VIP para Internet o con un extremo interno no expuesto a Internet, también conocido como extremo equilibrador de carga interno (ILB). Configurar la puerta de enlace con un ILB es útil para la línea interna de aplicaciones empresariales que no se exponen en Internet. También es útil para los servicios o niveles dentro de una aplicación de varios niveles que se asientan en un límite de seguridad no expuesto a Internet, pero siguen necesitando distribución de carga round robin, permanencia de sesión o terminación SSL. Este artículo le guiará por los pasos necesarios para configurar una puerta de enlace de la aplicaciones con un ILB.

## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [Página de descarga](http://azure.microsoft.com/downloads/).
2. Creará una red virtual y subred para la Puerta de enlace de aplicaciones. Asegúrese de que no hay máquinas virtuales o de que las implementaciones de nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configurará para usar la Puerta de enlace de aplicaciones deben existir o tener sus extremos creados en la red virtual o tener una VIP/IP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?
 

- **Grupo de servidores back-end:** la lista de direcciones IP de los servidores back-end. Las direcciones IP mostradas deben pertenecer a la subred de la red virtual o ser una VIP/IP pública. 
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público abierto en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha:** la escucha tiene un puerto front-end, un protocolo (Http o Https, estos distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). 
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla *básica*. La regla *básica* es la distribución de carga round robin.


 
## Creación de una Puerta de enlace de aplicaciones

La diferencia entre el uso clásico del portal clásico de Azure y el Administrador de recursos de Azure será el orden en que creará la Puerta de enlace de aplicaciones y los elementos que es necesario configurar. Con el Administrador de recursos, todos los elementos que componen una Puerta de enlace de aplicaciones se configurarán individualmente y, a continuación, se unirán para crear el recurso Puerta de enlace de aplicaciones.


A continuación se proporcionan los pasos necesarios para crear una Puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual y una subred para la Puerta de enlace de aplicaciones
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Creación de un recurso para la Puerta de enlace de aplicaciones


## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del ARM. Hay más información disponible en [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).

### Paso 1

		PS C:\> Login-AzureRmAccount

### Paso 2

Compruebe las suscripciones para la cuenta.

		PS C:\> get-AzureRmSubscription 

Se le pedirá que se autentique con sus credenciales.<BR>

### Paso 3 

Elija qué suscripción de Azure va a utilizar.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Paso 4

Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una Puerta de enlace de aplicaciones usan el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos llamado "appgw-rg" en la ubicación "West US".

## Creación de una red virtual y una subred para la Puerta de enlace de aplicaciones

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos:

### Paso 1	
	
	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se usará para crear una red virtual

### Paso 2
	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Crea una red virtual denominada "appgwvnet" en el grupo de recursos "appw-rg" para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24
	
### Paso 3

	$subnet=$vnet.subnets[0]

Asigna el objeto de subred a la variable $subnet para los siguientes pasos.
 

## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

### Paso 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configuración de IP de Puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, toma una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tendrá una dirección IP.
 
### Paso 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

Este paso configurará el grupo de direcciones IP back-end denominado "pool01" con las direcciones IP "10.0.0.10,10.0.0.11, 10.0.0.12". Serán las direcciones IP que reciben el tráfico de red procedente del extremo IP del front-end. Deberá reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de la aplicación.

### Paso 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configura la opción Puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada en el grupo de back-end.

### Paso 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura el puerto IP del front-end denominado "frontendport01" para el ILB.

### Paso 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Crea la configuración de direcciones IP front-end denominada "fipconfig01", que asocia una dirección IP privada de la subred de red virtual actual.

### Paso 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crea el nombre de agente de escucha "listener01" y asocia el puerto front-end con la configuración de direcciones IP front-end.

### Paso 7 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regla de enrutamiento del equilibrador de carga denominado "rule01" mediante la configuración del comportamiento del equilibrador de carga.

### Paso 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura el tamaño de la instancia de la Puerta de enlace de aplicaciones

>[AZURE.NOTE]El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de la Puerta de enlace de aplicaciones con New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crea una Puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".



## Inicio de la puerta de enlace

Una vez configurada la puerta de enlace, use el cmdlet `Start-AzureRmApplicationGateway` para iniciarla. La facturación de una puerta de enlace de aplicaciones comienza después de que se haya iniciado correctamente.


**Nota**: El cmdlet `Start-AzureRmApplicationGateway` puede tardar hasta 15-20 minutos en completarse.

En el ejemplo siguiente, la puerta de enlace de aplicaciones se denomina "appgwtest" y el grupo de recursos es "appgw-rg":


### Paso 1

Obtenga el objeto de la Puerta de enlace de aplicaciones y asócielo a una variable "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2
	 
Use `Start-AzureRmApplicationGateway` para iniciar la Puerta de enlace de aplicaciones:

	PS C:\> Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureRmApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificación del estado de la Puerta de enlace de aplicaciones

Use el cmdlet `Get-AzureRmApplicationGateway` para comprobar el estado de la puerta de enlace. Si *Start-AzureApplicationGateway* se realizó correctamente en el paso anterior, el estado debe ser *En ejecución*.


## Eliminación de una Puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de aplicaciones, deberá hacer lo siguiente en orden:

1. Use el cmdlet `Stop-AzureRmApplicationGateway` para detener la puerta de enlace. 
2. Use el cmdlet `Remove-AzureRmApplicationGateway` para quitar la puerta de enlace.
3. Compruebe que se quitó la puerta de enlace con el cmdlet `Get-AzureApplicationGateway`.

Este ejemplo muestra el cmdlet `Stop-AzureRmApplicationGateway` en la primera línea, seguido de la salida.

### Paso 1

Obtenga el objeto de la Puerta de enlace de aplicaciones y asócielo a una variable "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2
	 
Use `Stop-AzureRmApplicationGateway` para detener la Puerta de enlace de aplicaciones:

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Cuando el estado de la Puerta de enlace de aplicaciones sea Detenido, use el cmdlet `Remove-AzureRmApplicationGateway` para quitar el servicio.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]Se puede usar el elemento opcional "-force" para suprimir el mensaje de confirmación
>

Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureRmApplicationGateway`. Este paso no es necesario.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Pasos siguientes

Si desea configurar la descarga SSL, consulte [Configuración de una puerta de enlace de aplicaciones para descarga SSL](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de aplicaciones para usarla con ILB, consulte [Creación de una Puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1125_2015-->