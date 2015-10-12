<properties 
	pageTitle="Detalles de configuración de red para trabajar con ExpressRoute" 
	description="Detalles de configuración de red para ejecutar entornos del Servicio de aplicaciones en las redes virtuales conectadas a un circuito de ExpressRoute." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute 

## Información general ##
Los clientes pueden conectar un circuito de [Azure ExpressRoute][ExpressRoute] a su infraestructura de red virtual, lo que permite ampliar la red local a Azure. Se puede crear un entorno del Servicio de aplicaciones en una subred de esta infraestructura de [red virtual][virtualnetwork]. Las aplicaciones que se ejecutan en el entorno del Servicio de aplicaciones pueden establecer conexiones seguras con los recursos backend a los que solo se puede tener acceso través de la conexión ExpressRoute.

**Nota:** no se puede crear un entorno del Servicio de aplicaciones en una red virtual "v2". Sin embargo, los entornos del Servicio de aplicaciones solo funcionan actualmente con las redes virtuales clásicas "v1".

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Conectividad de red necesaria ##
Existen requisitos de conectividad de red para entornos del Servicio de aplicaciones que no pueden cumplirse inicialmente en una red virtual conectada a ExpressRoute.

Los entornos del Servicio de aplicaciones requieren todas las opciones siguientes para funcionar correctamente:


-  Conectividad de red saliente a los recursos de Almacenamiento de Azure en todo el mundo y Base de datos SQL ubicados en la misma región que el entorno del Servicio de aplicaciones. Esta ruta de acceso de red no puede atravesar servidores proxy corporativos internos porque, al hacerlo, probablemente cambiará la dirección NAT efectiva del tráfico de red de salida. Al cambiar la dirección NAT del tráfico de red de salida de un entorno del Servicio de aplicaciones dirigido a los extremos de Almacenamiento de Azure y Base de datos SQL, se producirán errores de conectividad.
-  La configuración DNS para la red virtual debe ser capaz de resolver extremos en los siguientes dominios controlados de Azure: **.file.core.windows.net*, **.blob.core.windows.net*, **.database.windows.net*.
-  La configuración DNS para la red virtual debe permanecer estable siempre que se creen entornos del Servicio de aplicaciones, así como durante la reconfiguración y los cambios de escala de los entornos del Servicio de aplicaciones.   
-  Si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace de VPN, el servidor DNS debe estar accesible y disponible. 
-  El acceso de red entrante a los puertos necesarios para los entornos del Servicio de aplicaciones debe estar permitido, como se describe en este [artículo][requiredports].

El requisito de DNS puede cumplirse al garantizar una configuración DNS válida para la red virtual.

Se pueden cumplir los requisitos de acceso de red entrante mediante la configuración de un [grupo de seguridad de red][NetworkSecurityGroups] en la subred del entorno del Servicio de aplicaciones, como se describe en este [artículo][requiredports].

## Habilitar la conectividad de red saliente para un entorno del Servicio de aplicaciones##
De forma predeterminada, un circuito de ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet. Con esta configuración, un entorno del Servicio de aplicaciones podrá conectarse a otros extremos de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada que fuerza a que el tráfico saliente de Internet fluya a nivel local a través de la infraestructura del proxy/firewall de un cliente. El flujo de tráfico interrumpe invariablemente entornos del Servicio de aplicaciones porque el tráfico saliente está bloqueado de forma local o NAT tendría un conjunto de direcciones irreconocibles que ya no funcionan con varios extremos de Azure.

La solución es definir una (o más) rutas definidas por el usuario (UDR) en la subred que contiene el entorno del Servicio de aplicaciones. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

La información de fondo sobre las rutas definidas por el usuario está disponible en esta [información general][UDROverview].

Los detalles sobre cómo crear y configurar las rutas definidas por el usuario están disponibles en este [procedimiento][UDRHowTo].

## Ejemplo de configuración de rutas definidas por el usuario para un entorno del Servicio de aplicaciones ##

**Requisitos previos**

1. Instale la versión más reciente de Azure PowerShell desde la [página Descargas de Azure][AzureDownloads] (fecha: junio de 2015 o posterior). En "Herramientas de línea de comandos" hay un vínculo "Instalar" en "Windows Powershell" que instalará los cmdlets más recientes de PowerShell.

2. Se recomienda crear una única subred para que el entorno del Servicio de aplicaciones lo utilice de manera exclusiva. Esto garantiza que las rutas definidas por el usuario aplicadas a la subred solo abrirán el tráfico saliente para el entorno del Servicio de aplicaciones.
3. **Importante**: no implemente el entorno del Servicio de aplicaciones hasta **después** de haber completado los siguientes pasos de configuración. Esto garantiza que la conectividad de red saliente esté disponible antes de intentar implementar un entorno del Servicio de aplicaciones.

**Paso 1: Crear una tabla de ruta con nombre**

El fragmento de código siguiente crea una tabla de ruta llamada "DirectInternetRouteTable" en la región Oeste de EE.UU. de Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Paso 2: Crear una o varias rutas en la tabla de ruta**

Necesitará agregar una o varias rutas a la tabla de ruta para habilitar el acceso de salida a Internet. En el siguiente ejemplo se agregan las rutas suficientes para cubrir todas las posibles direcciones de Azure utilizadas en la región Oeste de EE.UU.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Para obtener una lista completa y actualizada de los intervalos CIDR que usa Azure, puede descargar un archivo XML que contiene todos los intervalos en el [Centro de descarga de Microsoft][DownloadCenterAddressRanges].

**Nota:** en un momento dado, estará disponible un intervalo CIDR abreviado de 0.0.0.0/0 utilizarse en el parámetro *AddressPrefix*. Esta forma abreviada equivale a "todas las direcciones de Internet". Por ahora, los desarrolladores necesitarán usar un amplio conjunto de intervalos CIDR suficientes para cubrir todos los intervalos de direcciones posibles de Azure.

**Paso 3: Asociar la tabla de ruta a la subred que contiene el entorno del Servicio de aplicaciones**

El último paso de configuración es asociar la tabla de ruta a la subred donde se implementará el entorno del Servicio de aplicaciones. El comando siguiente asocia "DirectInternetRouteTable" a la subred "ASESubnet" que, en última instancia, contendrá un entorno del Servicio de aplicaciones.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Paso 4: Pasos finales**

Una vez que la tabla de ruta está enlazada a la subred, se recomienda que primero pruebe y confirme el efecto deseado. Por ejemplo, implemente una máquina virtual en la subred y confirme que:


- El tráfico saliente a los extremos de Azure no fluye hacia abajo en el circuito de ExpressRoute.
- Las búsquedas DNS para los extremos de Azure se resuelven correctamente. 

Tras haber confirmado los pasos anteriores, puede eliminar la máquina virtual y después continuar con la creación de un entorno del Servicio de aplicaciones.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->