<properties 
	pageTitle="Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium | Microsoft Azure" 
	description="Aprenda a crear y a administrar la compatibilidad con la red Virtual para las instancias de la Caché en Redis de Azure de nivel Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="sdanie"/>

# Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium
Caché en Redis de Azure tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché, incluido el nuevo nivel Premium.

El nivel Premium de Caché en Redis de Azure incluye la compatibilidad con la agrupación en clústeres, la persistencia y la red virtual. Una red virtual es una red privada en la nube. Cuando una instancia de Caché en Redis de Azure se configure con una red virtual, no será posible acceder a ella públicamente, solo se podrá acceder a ella desde máquinas virtuales y aplicaciones de dentro de la red virtual. En este artículo se describe cómo configurar la compatibilidad con red virtual de una instancia de Caché en Redis de Azure Premium.

>[AZURE.NOTE] Caché en Redis de Azure admite redes virtuales con los enfoques clásico y ARM.

Para obtener información sobre otras características de caché premium, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md) y [Cómo configurar la agrupación en clústeres para Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).

## ¿Por qué una red virtual?
La implementación de la [Red virtual de Azure](https://azure.microsoft.com/services/virtual-network/) ofrece seguridad mejorada y aislamiento para su Caché en Redis de Azure, así como subredes, directivas de control de acceso y otras características que restringen más el acceso a Caché en Redis de Azure.

## Compatibilidad con redes virtuales
La compatibilidad con redes virtuales se configura en la hoja **Nueva caché en Redis** durante la creación de la memoria caché.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Una vez que haya seleccionado un plan de tarifa Premium, la integración con una red virtual de la Caché en Redis de Azure se puede configurar seleccionando una red virtual que esté en la misma suscripción y ubicación que la memoria caché. Para usar una red virtual, primero debe crearla siguiendo los pasos de [Creación de una red virtual mediante el Portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [Creación de una red virtual (clásica) usando el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) y, luego, volver a la hoja **Nueva caché en Redis** para crear y configurar su memoria caché Premium  


Para configurar la red virtual de la memoria caché nueva, haga clic en **Red virtual** en la hoja **Nueva caché en Redis** y seleccione la red virtual deseada de la lista desplegable.

![Red virtual][redis-cache-vnet]

Seleccione la subred deseada en la lista desplegable **Subred** y especifique la **dirección IP estática** deseada. Si utiliza una red virtual con el enfoque clásico, el campo **Dirección IP estática** es opcional y no se especifica ninguna, se elegirá una de la red seleccionada.

>[AZURE.IMPORTANT] Si se implementa una Caché en Redis de Azure en una red virtual con el enfoque de ARM, la memoria caché debe estar en una subred dedicada que no contenga otros recursos, salvo instancias de Caché en Redis de Azure. Si se intenta implementar una Caché en Redis de Azure en una red virtual con el enfoque de ARM en una subred que contiene otros recursos, se producirá un error en la implementación.

![Red virtual][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Las cuatro primeras direcciones en una subred están reservadas y no se pueden usar. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Después de que se crea la memoria caché, para ver la configuración de la red virtual haga clic en **Red virtual** en la hoja **Configuración**.

![Red virtual][redis-cache-vnet-info]


Para conectarse a la instancia de Caché en Redis de Azure cuando usa una red virtual, especifique el nombre de host de la memoria caché en la cadena de conexión, como se muestra en el ejemplo siguiente.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Preguntas frecuentes sobre la red virtual de Caché en Redis de Azure

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Caché en Redis de Azure.

-	[¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Caché en Redis de Azure y las redes virtuales?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-	[¿Se pueden usar redes virtuales con una memoria caché Basic o Estándar?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-	[¿Por qué se produce un error al crear una caché en Redis en algunas subredes, pero no en otras?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)


## ¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Caché en Redis de Azure y las redes virtuales?

Cuando la Caché en Redis de Azure se hospeda en una red virtual, se usan los puertos de la tabla siguiente. Si estos puertos están bloqueados, puede que la memoria caché no funcione correctamente. Tener bloqueados uno o varios de estos puertos es el problema más común de una configuración incorrecta cuando se utiliza la Caché en Redis de Azure en una red virtual.

| Puertos | Dirección | Protocolo de transporte | Propósito | Dirección IP remota |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Salida | TCP | Dependencias de Redis en Almacenamiento de Azure/PKI (Internet) | * |
| 53 | Salida | TCP/UDP | Dependencias de Redis en DNS (Internet y red virtual) | * |
| 6379, 6380 | Entrada | TCP | Comunicación del cliente con Redis, equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | Entrante o saliente | TCP | Detalles de implementación para Redis | VIRTUAL\_NETWORK |
| 8500 | Entrada | TCP/UDP | Equilibrio de carga de Azure | AZURE\_LOADBALANCER |
| 10221-10231 | Entrante o saliente | TCP | Detalle de implementación para Redis (puede restringir el punto de conexión remoto a VIRTUAL\_NETWORK) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | Entrada | TCP | Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | Entrada | TCP | Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | Entrada | TCP/UDP | Equilibrio de carga de Azure | AZURE\_LOADBALANCER |
| 20226 | Entrante + saliente | TCP | Detalle de implementación de clústeres de Redis | VIRTUAL\_NETWORK |


Existen requisitos de conectividad de red para entornos para una Caché en Redis de Azure que no pueden cumplirse inicialmente en una red virtual. Caché en Redis de Azure requiere todos los elementos siguientes para funcionar correctamente cuando se utiliza en una red virtual.

-  Conectividad de red saliente a los puntos de conexión de Almacenamiento de Azure en todo el mundo. Aquí se incluyen los puntos de conexión ubicados en la misma región que la instancia de Caché en Redis de Azure, así como los puntos de conexión de almacenamiento ubicados en **otras** regiones de Azure. Los puntos de conexión de Almacenamiento de Azure se resuelven en los dominios DNS siguientes: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*. 
-  Conectividad de red saliente a *ocsp.msocsp.com*, *mscrl.microsoft.com* y *crl.microsoft.com*. Es necesario para admitir la funcionalidad SSL.
-  La configuración de DNS para la red virtual debe ser capaz de resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores. Se pueden cumplir los requisitos de DNS al asegurar que se configura y se mantiene una infraestructura DNS válida para la red virtual.



### ¿Se pueden usar redes virtuales con una memoria caché Basic o Estándar?

Las redes virtuales solo se pueden usar con memorias caché Premium.

### ¿Por qué se produce un error al crear una caché en Redis en algunas subredes, pero no en otras?

Si se implementa una Caché en Redis de Azure en una red virtual con el enfoque de ARM, la memoria caché debe estar en una subred dedicada que no contenga otro tipo de recursos. Si se intenta implementar una Caché en Redis de Azure en una subred de una red virtual con el enfoque de ARM que contenga otros recursos, se producirá un error en la implementación. Para poder crear una nueva caché en Redis, es preciso eliminar los recursos existentes en la subred.

Puede implementar varios tipos de recursos en una red virtual con el enfoque clásico, siempre que tenga suficientes direcciones IP disponibles.

## Uso de ExpressRoute con Caché en Redis de Azure

Los clientes pueden conectar un circuito de [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a su infraestructura de red virtual, lo que permite ampliar la red local a Azure.

De forma predeterminada, un circuito de ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet. Con este cliente de configuración, las aplicaciones podrán conectarse a otros puntos de conexión de Azure, lo que incluye Caché en Redis de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza a que el tráfico saliente de Internet fluya a nivel local. El flujo de tráfico interrumpe invariablemente la conectividad con Caché en Redis de Azure porque el tráfico saliente está bloqueado de forma local o porque se usa NAT para convertirlo en un conjunto de direcciones irreconocibles que no funcionan con varios puntos de conexión de Azure.

La solución es definir una, o varias, rutas definidas por el usuario (UDR) en la subred que contiene el servicio Caché en Redis de Azure. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la configuración siguiente:

- La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
- La UDR aplicada a la subred que contiene el servicio Caché en Redis de Azure define 0.0.0.0/0 con un tipo de salto siguiente de Internet (más adelante, en este mismo artículo, se muestra un ejemplo).

El efecto combinado de estos pasos es que la UDR a nivel de subred tendrá prioridad sobre la tunelización forzada de ExpressRoute, lo que garantiza el acceso saliente a Internet desde el servicio Caché en Redis de Azure.

Aunque la conexión a una instancia de Caché en Redis de Azure desde una aplicación local mediante ExpressRoute no es un escenario de uso típico por motivos de rendimiento (para que el rendimiento sea óptimo, los clientes de Caché en Redis de Azure deben estar en la misma región que el servicio Caché en Redis de Azure), en este caso la ruta de acceso de red saliente no puede atravesar servidores proxy corporativos internos ni se puede forzar su tunelización en local. Si lo hace, se cambiará la dirección NAT en vigor del tráfico de red saliente del servicio Caché en Redis de Azure. El cambio de la dirección NAT del tráfico de red saliente de una instancia de Caché en Redis de Azure causará errores de conectividad en muchos de los puntos de conexión enumerados anteriormente. Esto provocará errores en los intentos de creación del servicio Caché en Redis de Azure.

**IMPORTANTE:** las rutas definidas en una UDR **deben** ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplio y como tal puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.

**MUY IMPORTANTE:** Caché en Redis de Azure no es compatible con las configuraciones de ExpressRoute que **anuncian incorrectamente rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado**. Las configuraciones de ExpressRoute con el emparejamiento público configurado recibirán anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian incorrectamente en la ruta de acceso de interconexión privada, el resultado final es que se forzará incorrectamente la tunelización de todos los paquetes de red salientes desde la subred de la instancia de Caché en Redis de Azure a la infraestructura de red local del cliente. Este flujo de red interrumpirá el servicio Caché en Redis de Azure. La solución a este problema consiste en detener rutas anunciadas entre la ruta de acceso de interconexión pública y la ruta de acceso de interconexión privada.

Se puede encontrar información de contexto sobre las rutas definidas por el usuario en esta [información general](../virtual-network/virtual-networks-udr-overview.md).

Para más información acerca de ExpressRoute, consulte [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## Pasos siguientes
Obtenga información acerca de cómo usar más características de la memoria caché del nivel Premium.

-	[Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md)
-	[Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md)
-	[Importación y exportación de datos en la Caché en Redis de Azure](cache-how-to-import-export-data.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0601_2016-->