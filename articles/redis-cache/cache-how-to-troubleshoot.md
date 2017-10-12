---
title: "Solución de problemas de Azure Redis Cache | Microsoft Docs"
description: "Aprenda más sobre cómo resolver problemas comunes con Caché en Redis de Azure."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
ms.openlocfilehash: 2e9d1b644f1e80c7d916a261a6c47fcc11a1ffe0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Solución de problemas de Caché en Redis de Azure
Este artículo proporciona orientación para solucionar problemas de las siguientes categorías de problemas de Caché en Redis de Azure.

* [Solución de problemas de lado cliente](#client-side-troubleshooting) : esta sección proporciona instrucciones acerca de cómo identificar y resolver problemas causados por la aplicación que se conecta a Caché en Redis de Azure.
* [Solución de problemas de lado servidor](#server-side-troubleshooting) : esta sección proporciona instrucciones acerca de cómo identificar y resolver problemas causados en el lado de servidor de Caché en Redis de Azure.
* [Excepciones de tiempo de espera de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) : esta sección proporciona información sobre cómo solucionar problemas al usar el cliente StackExchange.Redis.

> [!NOTE]
> Algunos de los pasos de solución de problemas de esta guía incluyen instrucciones para ejecutar comandos de Redis y supervisar diversas métricas de rendimiento. Para más información e instrucciones, consulte los artículos de la sección [Información adicional](#additional-information) .
> 
> 

## <a name="client-side-troubleshooting"></a>Solución de problemas de lado cliente
Esta sección describe problemas que se producen debido a una condición en la aplicación cliente.

* [Presión de memoria en el cliente](#memory-pressure-on-the-client)
* [Ráfagas de tráfico](#burst-of-traffic)
* [Uso elevado de la CPU del cliente](#high-client-cpu-usage)
* [Ancho de banda del lado cliente agotado](#client-side-bandwidth-exceeded)
* [Tamaño de solicitud/respuesta grande](#large-requestresponse-size)
* [¿Qué ha ocurrido con mis datos en Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Presión de memoria en el cliente
#### <a name="problem"></a>Problema
La presión de memoria en el equipo cliente genera todo tipo de problemas de rendimiento que pueden retrasar el procesamiento de los datos enviados por la instancia de Redis sin ningún retraso. Cuando se alcanza la presión de memoria, normalmente el sistema tiene que paginar los datos de la memoria física a la memoria virtual que está en el disco. Estos *errores de página* hacen que el sistema se ralentice considerablemente.

#### <a name="measurement"></a>Medición
1. Supervise el uso de memoria en el equipo para asegurarse de que no exceda la memoria disponible. 
2. Supervise el contador de rendimiento `Page Faults/Sec` . La mayoría de los sistemas tendrá algunos errores de página, incluso durante el funcionamiento normal; por tanto, vigile los picos en este contador de rendimiento de errores de página que corresponden a tiempos de espera agotados.

#### <a name="resolution"></a>Resolución
Actualice el cliente a un tamaño de máquina virtual del cliente mayor, con más memoria, o bien profundice en los patrones de uso de la memoria para reducir el consumo de memoria.

### <a name="burst-of-traffic"></a>Ráfagas de tráfico
#### <a name="problem"></a>Problema
Las ráfagas de tráfico se combinan con una mala configuración de `ThreadPool` pueden provocar retrasos en el procesamiento de datos ya enviados por el servidor Redis, pero que aún no se han consumido en el lado cliente.

#### <a name="measurement"></a>Medición
Supervise cómo sus estadísticas de `ThreadPool` cambian a lo largo del tiempo mediante un código [como este](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). También puede mirar el mensaje `TimeoutException` de StackExchange.Redis. Aquí tiene un ejemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

En el mensaje anterior, hay varios problemas que son interesantes:

1. Observe que en la sección `IOCP` y la sección `WORKER` tiene un valor de `Busy` que es mayor que el valor de `Min`. Esto significa que es necesario justar la configuración de `ThreadPool` .
2. También puede ver `in: 64221`. Esto indica que se han recibido 64211 bytes en la capa de sockets del kernel, pero que la aplicación (por ejemplo, StackExchange.Redis) aún no los ha leído. Normalmente, esto significa que la aplicación no está leyendo los datos de la red con la rapidez con la que el servidor se los envía.

#### <a name="resolution"></a>Resolución
Configure las [opciones de ThreadPool](https://gist.github.com/JonCole/e65411214030f0d823cb) para asegurarse de que su grupo de subprocesos se escalará verticalmente a gran velocidad en escenarios de ráfaga.

### <a name="high-client-cpu-usage"></a>Uso elevado de la CPU del cliente
#### <a name="problem"></a>Problema
El uso elevado de la CPU en el cliente es una indicación de que el sistema no puede realizar todo el trabajo que se ha solicitado. Esto significa que el cliente puede producir un error al procesar una respuesta de Redis de manera puntual aunque Redis envíe la respuesta muy rápidamente.

#### <a name="measurement"></a>Medición
Supervise el uso de CPU en todo el sistema mediante el Portal de Azure o el contador de rendimiento asociado. Tenga cuidado para no supervisar la CPU de un *proceso* , porque un solo proceso puede tener un uso de CPU bajo, al tiempo que el uso de CPU general del sistema es elevado. Busque picos de uso de CPU que correspondan a tiempos de espera agotados. Como resultado de un uso de CPU alto, también puede ver valores de `in: XXX` en mensajes de error de `TimeoutException`, como se describe en la sección [Ráfagas de tráfico](#burst-of-traffic).

> [!NOTE]
> StackExchange.Redis 1.1.603 y versiones posteriores incluyen la métrica `local-cpu` en mensajes de error de `TimeoutException`. Asegúrese de usar la versión más reciente del [paquete StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Hay errores que se solucionan constantemente en el código que lo hacen más solido frente a tiempos de espera agotados, por lo que es importante tener la versión más reciente.
> 
> 

#### <a name="resolution"></a>Resolución
Actualice a un tamaño de máquina virtual mayor, con más capacidad de CPU, o bien investigue qué está causando picos de CPU. 

### <a name="client-side-bandwidth-exceeded"></a>Ancho de banda del lado cliente agotado
#### <a name="problem"></a>Problema
Los equipos cliente con diferentes tamaños tienen limitaciones en el ancho de banda de red disponible. Si el cliente supera el ancho de banda disponible, los datos no se procesarán en el lado cliente con la rapidez con la que el servidor se los envía. Esto puede producir tiempos de espera agotados.

#### <a name="measurement"></a>Medición
Supervise cómo cambia el uso del ancho de banda a lo largo del tiempo mediante código [como este](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tenga en cuenta que este código puede no ejecutarse correctamente en algunos entornos con permisos restringidos (como sitios web de Azure).

#### <a name="resolution"></a>Resolución
Aumente el tamaño de la máquina virtual del cliente o reduzca el consumo del ancho de banda de red.

### <a name="large-requestresponse-size"></a>Tamaño de solicitud/respuesta grande
#### <a name="problem"></a>Problema
Una solicitud/respuesta grande puede provocar tiempos de espera agotados. Por ejemplo, suponga que el valor del tiempo de espera configurado en el cliente es de 1 segundo. La aplicación solicita dos claves (p. ej. 'A' y 'B') al mismo tiempo (con la misma conexión de red física). La mayoría de los clientes admiten la "canalización" de las solicitudes, por lo que ambas solicitudes, 'A' y 'B', se envían a través de la conexión al servidor una tras otra sin tener que esperar a las respuestas. El servidor devolverá las respuestas en el mismo orden. Si la respuesta 'A' es grande bastante, puede consumir la mayor parte del tiempo de espera para las solicitudes posteriores. 

En el siguiente ejemplo se muestra este escenario. En este escenario, las solicitudes 'A' y 'B' se envían rápidamente, el servidor empieza a enviar las respuestas 'A' y 'B' rápidamente, pero debido a los tiempos de transferencia de datos, 'B' se atasca detrás de la otra solicitud y agota el tiempo, aunque el servidor respondió con rapidez.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medición
Esto es difícil de medir. Básicamente, debe instrumentar el código del cliente para realizar un seguimiento de las respuestas y solicitudes grandes. 

#### <a name="resolution"></a>Resolución
1. Redis está optimizado para un gran número de valores pequeños, en lugar de unos pocos valores grandes. La mejor solución es dividir los datos en valores menores relacionados. Consulte la publicación [What is the ideal value size range for redis? Is 100KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) (¿Cuál es el intervalo de tamaño de valor ideal para redis? ¿100 KB es demasiado grande?) para ver detalles de por qué se recomiendan valores más pequeños.
2. Aumente el tamaño de la máquina virtual (para el cliente y el servidor de caché Redis) para obtener mayores funcionalidades de ancho de banda, reduciendo los tiempos de transferencia de datos para respuestas mayores. Tenga en cuenta que obtener más ancho de banda solo en el servidor o solo en el cliente puede no ser suficiente. Mida el uso del ancho de banda y compárela con las funcionalidades del tamaño de la máquina virtual que tiene actualmente.
3. Aumente el número de objetos `ConnectionMultiplexer` que usa y las solicitudes de round robin a través de diferentes conexiones.

### <a name="what-happened-to-my-data-in-redis"></a>¿Qué ha ocurrido con mis datos en Redis?
#### <a name="problem"></a>Problema
Esperaba que ciertos datos estuvieran en la instancia de Caché en Redis de Azure, pero no parecen estar ahí.

#### <a name="resolution"></a>Resolución
Consulte [¿Qué ha ocurrido con mis datos en Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para ver posibles causas y soluciones.

## <a name="server-side-troubleshooting"></a>Solución de problemas de lado servidor
Esta sección describe problemas que se producen debido a una condición en el servidor de caché.

* [Presión de memoria en el servidor](#memory-pressure-on-the-server)
* [Uso elevado de la CPU/carga de servidor](#high-cpu-usage-server-load)
* [Ancho de banda del lado servidor agotado](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Presión de memoria en el servidor
#### <a name="problem"></a>Problema
La presión de memoria en el servidor genera todo tipo de problemas de rendimiento que pueden retrasar el procesamiento de solicitudes. Cuando se alcanza la presión de memoria, normalmente el sistema tiene que paginar los datos de la memoria física a la memoria virtual que está en el disco. Estos *errores de página* hacen que el sistema se ralentice considerablemente. Hay varias causas posibles de esta presión de memoria: 

1. Ha llenado totalmente la memoria caché con datos. 
2. Redis está viendo una fragmentación de memoria alta; normalmente debida al almacenamiento de objetos grandes (Redis está optimizado para objetos pequeños; consulte [What is the ideal value size range for redis? Is 100KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) (¿Cuál es el intervalo de tamaño de valor ideal para redis? ¿100 KB es demasiado grande?) para ver detalles. 

#### <a name="measurement"></a>Medición
Redis expone dos métricas que pueden ayudarle a identificar este problema. La primera es `used_memory` y la otra es `used_memory_rss`. [Estas métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) están disponibles en Azure Portal o mediante el comando [INFO de Redis](http://redis.io/commands/info).

#### <a name="resolution"></a>Resolución
Hay varios cambios posibles que puede hacer para ayudar a mantener un uso de memoria correcto:

1. [Configurar una directiva de memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) y establecer tiempos de expiración de las claves. Tenga en cuenta que esto puede no ser suficiente si tiene fragmentación.
2. [Configurar un valor rmaxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) lo suficientemente grande como para compensar la fragmentación de la memoria.
3. Dividir los objetos grandes que están en la memoria caché en objetos relacionados más pequeños.
4. [Escalar](cache-how-to-scale.md) a un tamaño de memoria caché mayor.
5. Si usa una [memoria caché Premium con un clúster de Redis habilitado](cache-how-to-premium-clustering.md), puede [aumentar el número de particiones](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Uso elevado de la CPU/carga de servidor
#### <a name="problem"></a>Problema
El uso elevado de la CPU puede significar que el lado cliente no puede procesar una respuesta de Redis de manera puntual, aunque Redis envió la respuesta muy rápidamente.

#### <a name="measurement"></a>Medición
Supervise el uso de CPU en todo el sistema mediante el Portal de Azure o el contador de rendimiento asociado. Tenga cuidado para no supervisar la CPU de un *proceso* , porque un solo proceso puede tener un uso de CPU bajo, al tiempo que el uso de CPU general del sistema es elevado. Busque picos de uso de CPU que correspondan a tiempos de espera agotados.

#### <a name="resolution"></a>Resolución
[Escale](cache-how-to-scale.md) a un nivel de memoria caché mayor, con más capacidad de CPU o investigue qué causando los picos de la CPU. 

### <a name="server-side-bandwidth-exceeded"></a>Ancho de banda del lado servidor agotado
#### <a name="problem"></a>Problema
Las instancias de memoria caché con diferentes tamaños tienen limitaciones en el ancho de banda de red disponible. Si el servidor supera el ancho de banda disponible, los datos no se enviará al cliente con la misma rapidez. Esto puede producir tiempos de espera agotados.

#### <a name="measurement"></a>Medición
Puede supervisar la métrica `Cache Read` , que es la cantidad de datos que se leen de la memoria caché en megabytes por segundo (MB/s) durante el intervalo de informes especificado. Este valor corresponde al ancho de banda de red utilizado por esta memoria caché. Si desea configurar alertas para los límites de ancho de banda de red del lado servidor, puede crearlos mediante este contador `Cache Read` . Compare sus lecturas con los valores de [esta tabla](cache-faq.md#cache-performance) para conocer los límites de ancho de banda observados para los diferentes tamaños y planes de tarifa de caché.

#### <a name="resolution"></a>Resolución
Si está constantemente cerca del ancho de banda máximo observado para el tamaño de caché y el plan de tarifa, considere la posibilidad de [escalar](cache-how-to-scale.md) a un plan de tarifa o a un tamaño que tengan más ancho de banda de red; para ello, use los valores de [esta tabla](cache-faq.md#cache-performance) como guía.

## <a name="stackexchangeredis-timeout-exceptions"></a>Excepciones de tiempo de espera de StackExchange.Redis
StackExchange.Redis usa la opción de configuración `synctimeout` para operaciones sincrónicas, y tiene un valor predeterminado de 1000 ms. Si no se completa una llamada sincrónica en el tiempo estipulado, el cliente de StackExchange.Redis genera un error de tiempo de espera similar al ejemplo siguiente.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Este mensaje de error contiene métricas que pueden indicarle la causa y la posible solución del problema. La tabla siguiente contiene detalles acerca de las métricas del mensaje de error.

| Métrica del mensaje de error | Detalles |
| --- | --- |
| inst |En el último intervalo de tiempo se han emitido 0 comandos. |
| mgr |El administrador del socket está realizando `socket.select` lo que significa que está solicitando al sistema operativo que indique un socket que tiene algo que hacer; básicamente: el lector no está leyendo activamente de la red porque cree que no hay nada que hacer. |
| queue |Hay un total de 73 operaciones en curso. |
| qu |6 de las operaciones en curso están en la cola de no enviados, y aún no se han escrito en la red de salida. |
| qs |67 de las operaciones en curso se han enviado al servidor, pero aún no hay una respuesta disponible. La respuesta podría ser `Not yet sent by the server` o `sent by the server but not yet processed by the client.`. |
| qc |0 de las operaciones en curso han visto respuestas, pero aún no se han marcado como completadas debido a la espera en el bucle de finalización. |
| wr |Hay un escritor activo (lo que significa que 6 solicitudes sin enviar no se omiten) bytes/activewriters. |
| bucear |No hay ningún lector activo y hay cero bytes disponibles para su lectura en los bytes/activereaders de NIC. |

### <a name="steps-to-investigate"></a>Pasos para investigar
1. Como procedimiento recomendado, asegúrese de usar el patrón siguiente para conectarse al usar al cliente de StackExchange.Redis.

    ```c#
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ````

    Para más información, consulte [Uso de Caché en Redis de Azure](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Asegúrese de que la Caché en Redis de Azure y la aplicación cliente están en la misma región de Azure. Por ejemplo, podría obtener tiempos de espera agotados cuando la memoria caché está en este de EE. UU., pero el cliente está en oeste de EE. UU. y no se completa la solicitud en el intervalo `synctimeout`, o bien podría obtener tiempos de espera agotados cuando realiza la depuración en el equipo de desarrollo local. 
   
    Se recomienda encarecidamente que la memoria caché y el cliente estén en la misma región de Azure. Si tiene un escenario que incluye llamadas entre regiones, debe establecer el intervalo `synctimeout` a un valor mayor que el intervalo predeterminado de 1000 ms mediante la inclusión de una propiedad `synctimeout` en la cadena de conexión. En el ejemplo siguiente se muestra un fragmento de cadena de conexión de caché de StackExchange.Redis con un `synctimeout` de 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Asegúrese de usar la versión más reciente del [paquete StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Hay errores que se solucionan constantemente en el código que lo hacen más solido frente a tiempos de espera agotados, por lo que es importante tener la versión más reciente.
3. Si hay solicitudes que dependen de las limitaciones de ancho de banda en el servidor o el cliente, tardarán más en completarse y generarán tiempos de espera agotados. Para ver si el tiempo de espera agotado se debe al ancho de banda de red del servidor, consulte [Ancho de banda del lado servidor agotado](#server-side-bandwidth-exceeded). Para ver si el tiempo de espera agotado se debe al ancho de banda de red del cliente, consulte [Ancho de banda del lado cliente agotado](#client-side-bandwidth-exceeded).
4. ¿la CPU está limitada en el servidor o en el cliente?
   
   * Compruebe si la CPU le está limitando en el cliente, lo que podría hacer que la solicitud no se pueda procesar en el intervalo `synctimeout` y causar un tiempo de espera agotado. Cambiar a un tamaño de cliente mayor o distribuir la carga puede ayudar a controlarlo. 
   * Supervise si la `CPU`[métrica de rendimiento de caché](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) para comprobar si la CPU le está limitando en el servidor. Las solicitudes entrantes mientras Redis está limitado por la CPU pueden provocar que dichas solicitudes agoten el tiempo de espera. Para solucionar este problema, puede distribuir la carga entre varias particiones en una memoria caché premium o actualizar a un tamaño o plan de tarifa mayor. Para más información, consulte [Ancho de banda del lado servidor agotado](#server-side-bandwidth-exceeded).
5. ¿Hay comandos que tardan mucho tiempo en procesarse en el servidor? Los comandos que tardan mucho tiempo en procesarse en el servidor de Redis pueden hacer que se agote el tiempo de espera. Algunos ejemplos de comandos que tardan mucho en ejecutarse son `mget` con un gran número de claves, `keys *` o scripts de LUA mal escritos. Puede conectarse a la instancia de Azure Redis Cache con el cliente redis-cli o usar la [consola de Redis](cache-configure.md#redis-console) y ejecutar el comando [SlowLog](http://redis.io/commands/slowlog) para ver si hay solicitudes que tardan más de lo esperado. El servidor de Redis y StackExchange.Redis están optimizados para muchas solicitudes pequeñas, en lugar de menos solicitudes de gran tamaño. Dividir los datos en fragmentos menores puede mejorar las cosas aquí. 
   
    Para información acerca de cómo conectarse al punto de conexión SSL de Caché en Redis de Azure con redis-cli y stunnel, consulte La publicación del blog [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Anuncio de proveedor de estado de sesión ASP.NET para la versión preliminar de Redis). Para más información, consulte [SlowLog](http://redis.io/commands/slowlog).
6. Una carga alta del servidor de Redis puede causar tiempos de espera agotados. Puede supervisar la carga del servidor con la `Redis Server Load`[métrica de rendimiento de caché](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Una carga del servidor de 100 (valor máximo) significa que el servidor de redis ha estado ocupado procesando solicitudes, sin tiempo de inactividad. Para ver si ciertas solicitudes ocupan toda la funcionalidad del servidor, ejecute el comando SlowLog, como se describe en el párrafo anterior. Para más información, consulte [Uso elevado de la CPU/carga de servidor](#high-cpu-usage-server-load).
7. ¿Ha habido cualquier otro evento en el lado cliente que puede haber causado una señalización visual de red? Compruebe en el cliente (web, rol de trabajo o máquina virtual de Iaas) si se ha producido un evento como el escalado vertical o la reducción vertical del número de instancias de cliente, la implementación de una nueva versión del cliente o está habilitado el escalado automático. En nuestras pruebas hemos encontrado que el escalado automático o el escalado vertical o la reducción vertical pueden hacer que se pierda la conectividad de red saliente durante varios segundos. El código de StackExchange.Redis es resistente a dichos eventos y se volverá a conectar. Durante este tiempo de reconexión, las solicitudes en la cola pueden agotar el tiempo de espera.
8. ¿Ha habido una solicitud grande antes de varias solicitudes pequeñas en la Caché en Redis que agotó el tiempo de espera? El parámetro `qs` en el error mensaje indica cuántas solicitudes se enviaron del cliente al servidor, pero que aún no han procesado una respuesta. Este valor puede seguir creciendo, ya que StackExchange.Redis usa una sola conexión de TCP y solo puede leer una respuesta cada vez. Aunque la primera operación ha agotado el tiempo de espera, no impide que los datos se envíen al o del servidor, y que se bloqueen otras solicitudes hasta que termine, provocando tiempos de espera agotados. Una solución es reducir la posibilidad de tiempos de espera agotados, garantizando que la memoria caché sea lo suficientemente grande para la carga de trabajo y dividiendo los valores grandes en fragmentos menores. Otra posible solución es utilizar un grupo de objetos `ConnectionMultiplexer` en el cliente y elegir el parámetro `ConnectionMultiplexer` con menos carga al enviar una solicitud nueva. Esto debería evitar que un único tiempo de espera haga que otras solicitudes también agoten el tiempo de espera.
9. Si está utilizando `RedisSessionStateprovider`, asegúrese de que haber configurado correctamente el tiempo de espera de reintentos. `retrytimeoutInMilliseconds` debe ser superior a `operationTimeoutinMilliseonds`; de lo contrario, no se producirá ningún reintento. En el siguiente ejemplo, `retrytimeoutInMilliseconds` se establece en 3000. Para más información, consulte [Proveedor de estado de sesión de ASP.NET para Azure Redis Cache](cache-aspnet-session-state-provider.md) y [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Uso de los parámetros de configuración del proveedor de estado de sesión y el proveedor de caché de resultados).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Compruebe el uso de la memoria en el servidor de Azure Redis Cache mediante la [supervisión](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) de `Used Memory RSS` y `Used Memory`. Si hay una directiva de expulsión implementada, Redis empieza a expulsar las claves cuando `Used_Memory` alcanza el tamaño de la memoria caché. Idealmente, `Used Memory RSS` solo debe ser ligeramente mayor que `Used memory`. Una gran diferencia significa que no hay fragmentación de memoria (interna o externa). Cuando `Used Memory RSS` es menor que `Used Memory`, significa que el sistema operativo ha intercambiado parte de la memoria caché. Si esto ocurre, que puede esperar latencias importantes. Dado que Redis no tiene control sobre cómo se asignan sus asignaciones a páginas de memoria, a menudo, un parámetro `Used Memory RSS` alto suele ser el resultado de un pico de uso de memoria. Cuando Redis libera la memoria, la memoria se devuelve al asignador, y el asignador puede devolver la memoria al sistema o no hacerlo. Puede haber una discrepancia entre el valor de `Used Memory` y el consumo de memoria comunicado por el sistema operativo. Esto puede deberse al hecho de que Redis ha usado y liberado la memoria, pero no la ha devuelto al sistema. Puede realizar los pasos siguientes para ayudar a mitigar los problemas de memoria.
   
   * Actualizar la memoria caché a un tamaño mayor para funcionar con limitaciones de memoria en el sistema.
   * Establecer tiempos de expiración de las claves para que los valores antiguos se expulsen de forma proactiva.
   * Supervisar la métrica de la memoria caché `used_memory_rss` . Cuando este valor se aproxima al tamaño de la memoria caché, es probable que empiece a ver problemas de rendimiento. Distribuir los datos entre varias particiones si utiliza una caché premium, o actualizar a un tamaño de caché mayor.
   
   Para obtener más información, consulte [Presión de memoria en el servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Información adicional
* [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [¿Cómo puedo ejecutar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Supervisión de Caché en Redis de Azure](cache-how-to-monitor.md)

