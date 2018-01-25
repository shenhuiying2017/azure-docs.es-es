---
title: "Configuración de una red virtual para Azure Redis Cache Premium | Microsoft Docs"
description: Aprenda a crear y a administrar la compatibilidad con la red virtual para las instancias de la Azure Redis Cache de nivel Premium
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 74ec104bebec2004a8b7116865c2394c02b12638
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Cómo configurar la compatibilidad de red virtual para una instancia de Azure Redis Cache Premium
Azure Redis Cache tiene diferentes ofertas de caché que proporcionan flexibilidad en la elección del tamaño y las características de la caché, incluidas las características de nivel premium como la agrupación en clústeres, la persistencia y la compatibilidad de red virtual. Una red virtual es una red privada en la nube. Cuando una instancia de Azure Redis Cache se configure con una red virtual, no será posible acceder a ella públicamente, solo se podrá acceder a ella desde máquinas virtuales y aplicaciones de dentro de la red virtual. En este artículo se describe cómo configurar la compatibilidad con red virtual de una instancia de Azure Redis Cache Premium.

> [!NOTE]
> Azure Redis Cache admite redes virtuales clásicas y de Resource Manager.
> 
> 

Para obtener información sobre otras características de caché Premium, consulte [Introducción al nivel Premium de Azure Redis Cache](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>¿Por qué una red virtual?
La implementación de [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) aporta seguridad mejorada y aislamiento de su Azure Redis Cache, así como subredes, directivas de Access Control y otras características para restringir aún más el acceso.

## <a name="virtual-network-support"></a>Compatibilidad con redes virtuales
La compatibilidad con Virtual Network se configura en la hoja **Nueva caché en Redis** durante la creación de la memoria caché. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Una vez que haya seleccionado un plan de tarifa premium, la integración con una red virtual de Redis se puede configurar seleccionando una red virtual que esté en la misma suscripción y ubicación que la memoria caché. Para usar una nueva red virtual, primero es preciso crearla, para lo que es preciso seguir los pasos de [Creación de una red virtual mediante Azure Portal](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [Creación de Virtual Network (clásica) mediante Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) y, luego, volver a la hoja **Nueva caché en Redis** para crear y configurar una memoria caché Premium.

Para configurar la red virtual de la memoria caché nueva, haga clic en **Virtual Network** en la hoja **Nueva caché en Redis** y seleccione la red virtual deseada en la lista desplegable.

![Red virtual][redis-cache-vnet]

Seleccione la subred deseada en la lista desplegable **Subred** y especifique la **dirección IP estática** deseada. Si utiliza una red virtual con el enfoque clásico, el campo **Dirección IP estática** es opcional y no se especifica ninguna, sino que se elegirá una de la red seleccionada.

> [!IMPORTANT]
> Si se implementa una instancia de Azure Redis Cache en una red virtual de Resource Manager, la memoria caché debe estar en una subred dedicada que no contenga otros recursos, salvo instancias de Azure Redis Cache. Si se intenta implementar una caché de Azure Redis Cache en una red virtual de Resource Manager en una subred que contiene otros recursos, se produce un error en la implementación.
> 
> 

![Red virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure reserva algunas direcciones IP dentro de cada subred y estas direcciones no se pueden usar. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo, junto con otras tres direcciones usadas para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Además de las direcciones IP que usa la infraestructura de Azure Virtual Network, cada instancia de Redis de la subred usa dos direcciones IP por partición y una dirección IP adicional para el equilibrador de carga. Se considera que una caché no en clúster tiene una partición.
> 
> 

Una vez que se crea la memoria caché, para ver la configuración de la red virtual es preciso hacer clic en **Virtual Network** en el **menú Recursos**.

![Red virtual][redis-cache-vnet-info]

Para conectarse a la instancia de Azure Redis Cache cuando usa una red virtual, especifique el nombre de host de la memoria caché en la cadena de conexión, como se muestra en el ejemplo siguiente:

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

## <a name="azure-redis-cache-vnet-faq"></a>Preguntas frecuentes sobre la red virtual de Azure Redis Cache
La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Azure Redis Cache.

* [¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Azure Redis Cache y las redes virtuales?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [¿Cómo se puede comprobar que la memoria caché funciona una red virtual?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [¿Se pueden usar redes virtuales con una memoria caché Basic o Estándar?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [¿Por qué se produce un error al crear una caché en Redis en algunas subredes, pero no en otras?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [¿Cuáles son los requisitos de espacio de direcciones de subred?](#what-are-the-subnet-address-space-requirements)
* [¿Funcionarán todas las características al alojar una caché en una red virtual?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>¿Cuáles son algunos de los problemas comunes de configuración incorrecta con Azure Redis Cache y las redes virtuales?
Cuando Azure Redis Cache se hospeda en una red virtual, se usan los puertos de la tabla siguiente. 

>[!IMPORTANT]
>Si los puertos de las siguientes tablas están bloqueados, puede que la memoria caché no funcione correctamente. Tener bloqueados uno o varios de estos puertos es el problema más común de una configuración incorrecta cuando se utiliza Azure Redis Cache en una red virtual.
> 
> 

- [Requisitos de puerto de salida](#outbound-port-requirements)
- [Requisitos de puerto de entrada](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Requisitos de puerto de salida

Existen siete requisitos de puerto de salida.

- Si quiere, se pueden realizar todas las conexiones de salida a Internet a través de un dispositivo de auditoría local de un cliente.
- Tres de los puertos enrutan el tráfico a los puntos de conexión de Azure que funcionan con Azure Storage y Azure DNS.
- Los intervalos de puertos restantes y para las comunicaciones internas de la subred de Redis. No es necesaria ninguna regla de NSG para las comunicaciones internas de la subred de Redis.

| Puertos | Dirección | Protocolo de transporte | Propósito | IP local | Dirección IP remota |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Salida |TCP |Dependencias de Redis en Azure Storage/PKI (Internet) | (Subred de Redis) |* |
| 53 |Salida |TCP/UDP |Dependencias de Redis en DNS (Internet y red virtual) | (Subred de Redis) |* |
| 8443 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) | (Subred de Redis) |
| 10221-10231 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) | (Subred de Redis) |
| 20226 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 13000-13999 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 15000-15999 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |


### <a name="inbound-port-requirements"></a>Requisitos de puerto de entrada

Existen ocho requisitos de intervalo de puertos de entrada. Las solicitudes entrantes de estos intervalos provienen de otros servicios hospedados en la misma red virtual o son comunicaciones internas de la subred de Redis.

| Puertos | Dirección | Protocolo de transporte | Propósito | IP local | Dirección IP remota |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicación del cliente con Redis, equilibrio de carga de Azure | (Subred de Redis) |Virtual Network, Azure Load Balancer |
| 8443 |Entrada |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 8500 |Entrada |TCP/UDP |Equilibrio de carga de Azure | (Subred de Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis), Azure Load Balancer |
| 13000-13999 |Entrada |TCP |Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | (Subred de Redis) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Entrada |TCP |Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | (Subred de Redis) |Virtual Network, Azure Load Balancer |
| 16001 |Entrada |TCP/UDP |Equilibrio de carga de Azure | (Subred de Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos adicionales de conectividad de red virtual

Existen requisitos de conectividad de red para entornos para una instancia de Azure Redis Cache que no pueden cumplirse inicialmente en una red virtual. Azure Redis Cache requiere todos los elementos siguientes para funcionar correctamente cuando se usa en una red virtual.

* Conectividad de red saliente a los puntos de conexión de Azure Storage en todo el mundo. Aquí se incluyen los puntos de conexión ubicados en la misma región que la instancia de Azure Redis Cache, así como los puntos de conexión de almacenamiento ubicados en **otras** regiones de Azure. Los puntos de conexión de Azure Storage se resuelven en los siguientes dominios de DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*. 
* Conectividad de red saliente con *ocsp.msocsp.com*, *mscrl.microsoft.com* y *crl.microsoft.com*. Esta conectividad es necesaria para admitir la funcionalidad SSL.
* La configuración de DNS para la red virtual debe ser capaz de resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores. Se pueden cumplir los requisitos de DNS al asegurar que se configura y se mantiene una infraestructura DNS válida para la red virtual.
* Conectividad de red saliente con los siguientes puntos de conexión de Azure Monitor que se resuelven en los siguientes dominios DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>¿Cómo se puede comprobar que la memoria caché funciona una red virtual?

>[!IMPORTANT]
>Al conectarse a una instancia de Azure Redis Cache que se hospeda en una red virtual, los clientes de caché deben estar en la misma red virtual, incluidas las aplicaciones de prueba o herramientas de hacer ping de diagnóstico.
>
>

Cuando los requisitos de puerto se configuran tal como se describe en la sección anterior, puede comprobar que la memoria caché funciona mediante la realización de los pasos siguientes.

- [Reinicie](cache-administration.md#reboot) todos los nodos de la caché. Si no se pueden alcanzar todas las dependencias de caché necesarias (como se documenta en [Requisitos del puerto de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) y [Requisitos de puerto de salida](cache-how-to-premium-vnet.md#outbound-port-requirements)), la caché no podrá reiniciarse correctamente.
- Cuando se han reiniciado los nodos de la memoria caché (como indica el estado de la caché en Azure Portal), puede realizar las siguientes pruebas:
  - hacer ping en el punto de conexión de caché (con el puerto 6380) desde una máquina que esté dentro de la misma red virtual como la memoria caché, con [tcping](https://www.elifulkerson.com/projects/tcping.php). Por ejemplo: 
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Si la herramienta `tcping` informa de que el puerto está abierto, la memoria caché está disponible para su conexión en los clientes de la red virtual.

  - Otra manera de realizar la prueba consiste en crear un cliente de caché de prueba (que podría ser una sencilla aplicación de consola mediante StackExchange.Redis) que se conecta a la memoria caché y agrega y recupera algunos elementos de la caché. Instale la aplicación de cliente de ejemplo en una máquina virtual que está en la misma red virtual que la caché y ejecútela para comprobar la conectividad en la memoria caché.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>¿Se pueden usar redes virtuales con una memoria caché Basic o Estándar?
Las redes virtuales solo se pueden usar con memorias caché Premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>¿Por qué se produce un error al crear una caché en Redis en algunas subredes, pero no en otras?
Si se implementa una instancia de Azure Redis Cache en una red virtual de Resource Manager, la memoria caché debe estar en una subred dedicada que no contenga ningún otro tipo de recursos. Si se intenta implementar una caché de Azure Redis Cache en una red virtual de Resource Manager en una subred que contiene otros recursos, se produce un error en la implementación. Para poder crear una nueva caché en Redis, es preciso eliminar los recursos existentes en la subred.

Puede implementar varios tipos de recursos en una red virtual con el enfoque clásico, siempre que tenga suficientes direcciones IP disponibles.

### <a name="what-are-the-subnet-address-space-requirements"></a>¿Cuáles son los requisitos de espacio de direcciones de subred?
Azure reserva algunas direcciones IP dentro de cada subred y estas direcciones no se pueden usar. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo, junto con otras tres direcciones usadas para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Además de las direcciones IP que usa la infraestructura de Azure Virtual Network, cada instancia de Redis de la subred usa dos direcciones IP por partición y una dirección IP adicional para el equilibrador de carga. Se considera que una caché no en clúster tiene una partición.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>¿Funcionarán todas las características al alojar una caché en una red virtual?
Cuando la memoria caché forma parte de una red virtual, solo los clientes de la red virtual pueden tener acceso a la memoria caché. Como resultado, las siguientes características de administración de la memoria caché no funcionan en este momento.

* Consola de Redis. Como la consola de Redis se ejecuta en el explorador local, que está fuera de la red virtual, no se puede conectar a la caché.

## <a name="use-expressroute-with-azure-redis-cache"></a>Uso de ExpressRoute con Azure Redis Cache
Los clientes pueden conectar un circuito de [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a su infraestructura de red virtual, lo que permite ampliar la red local a Azure. 

De forma predeterminada, un circuito ExpressRoute recién creado no realiza la tunelización forzada (anuncio de una ruta predeterminada, 0.0.0.0/0) en una red virtual. Como resultado, se permite la conectividad de salida de Internet directamente desde la red virtual y las aplicaciones cliente pueden conectarse a otros puntos de conexión de Azure, incluido Azure Redis Cache.

Pero una configuración de cliente común es usar la tunelización forzada (anunciar una ruta predeterminada), que fuerza a que el tráfico de salida de Internet fluya a nivel local. Este flujo de tráfico interrumpe la conectividad con Azure Redis Cache si el tráfico de salida se bloquea en el entorno local debido a que la instancia de Azure Redis Cache no se puede comunicar con sus dependencias.

La solución es definir una, o varias, rutas definidas por el usuario (UDR) en la subred que contiene el servicio Azure Redis Cache. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la configuración siguiente:

* La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
* El UDR aplicado a la subred que contiene Azure Redis Cache define 0.0.0.0/0 con una ruta de trabajo para tráfico TCP/IP a Internet; por ejemplo, estableciendo el tipo de próximo salto en "Internet".

El efecto combinado de estos pasos es que la UDR a nivel de subred tiene prioridad sobre la tunelización forzada de ExpressRoute, lo que garantiza el acceso saliente a Internet desde el servicio Azure Redis Cache.

La conexión a una instancia de Azure Redis Cache desde una aplicación local mediante ExpressRoute no es un escenario de uso típico debido a motivos de rendimiento (para que el rendimiento sea óptimo, los clientes de Azure Redis Cache deben estar en la misma región que Azure Redis Cache).

>[!IMPORTANT] 
>Las rutas definidas en una UDR **deben** ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplio y como tal puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.

>[!WARNING]  
>Azure Redis Cache no es compatible con las configuraciones de ExpressRoute que **anuncian incorrectamente rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado**. Las configuraciones de ExpressRoute con el emparejamiento público configurado reciben anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian incorrectamente en la ruta de acceso de interconexión privada, el resultado es que se forzará incorrectamente la tunelización de todos los paquetes de red salientes desde la subred de la instancia de Azure Redis Cache a la infraestructura de red local del cliente. Este flujo de red interrumpe Azure Redis Cache. La solución a este problema consiste en detener rutas anunciadas entre la ruta de acceso de interconexión pública y la ruta de acceso de interconexión privada.


Se puede encontrar información de contexto sobre las rutas definidas por el usuario en esta [información general](../virtual-network/virtual-networks-udr-overview.md).

Para más información sobre ExpressRoute, consulte [Información técnica sobre ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>pasos siguientes
Obtenga información acerca de cómo usar más características de la memoria caché del nivel Premium.

* [Introducción al nivel Premium de Azure Redis Cache](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

