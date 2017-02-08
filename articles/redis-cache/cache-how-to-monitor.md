---
title: "Supervisión de Azure Redis Cache | Microsoft Docs"
description: "Obtenga información sobre cómo supervisar el estado y el rendimiento de las instancias de Caché en Redis de Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 65385aa918222837468f88246d0527c22c677ba7
ms.openlocfilehash: 3ef5dcbcb5f8f6a57de575af20028875b9f920ea


---
# <a name="how-to-monitor-azure-redis-cache"></a>Supervisión de Caché en Redis de Azure
Caché en Redis de Azure ofrece varias opciones para la supervisión de instancias de caché. Puede ver las métricas, anclar los gráficos de métricas al panel de inicio, personalizar el intervalo de fecha y hora de los gráficos de supervisión, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplen determinadas condiciones. Estas herramientas permiten supervisar el estado de las instancias de Caché en Redis de Azure y ayudarle a administrar sus aplicaciones de almacenamiento en caché.

Cuando se habilitan los diagnósticos de caché, las métricas para las instancias de Caché en Redis de Azure se recopilan cada 30 segundos aproximadamente y se almacenan de manera que se puedan mostrar en los gráficos de métricas y que las reglas de alerta las puedan evaluar.

Las métricas de caché se recopilan mediante el comando [INFO](http://redis.io/commands/info) de Redis. Para obtener más información sobre los distintos valores INFO que se utilizan para las métricas de caché, consulte [Métricas disponibles e intervalos de informes](#available-metrics-and-reporting-intervals).

Para ver las métricas de caché, [busque](cache-configure.md#configure-redis-cache-settings) la instancia de su memoria caché en [Azure Portal](https://portal.azure.com). Se obtiene acceso a las métricas para las instancias de Caché en Redis de Azure en la hoja **Caché en Redis** .

![Caché en Redis][redis-cache-redis-metrics-blade]

> [!IMPORTANT]
> Si se muestra el mensaje siguiente en la hoja **Métricas de Redis** , siga los pasos de la sección [Habilitación de diagnósticos de caché](#enable-cache-diagnostics) para habilitar diagnósticos de memoria caché.
> 
> `Monitoring may not be enabled. Click here to turn on Diagnostics.`
> 
> 

La hoja **Métricas de Redis** contiene gráficos de **supervisión** en los que se muestran estas métricas. Se puede personalizar cada gráfico agregando o quitando métricas y cambiando el intervalo de informes. Para ver y configurar las operaciones y alertas, la hoja **Redis Cache** cuenta con una sección **Operaciones** en la que se muestran los **eventos** y las **reglas de alerta** de la memoria caché.

## <a name="enable-cache-diagnostics"></a>Habilitación de diagnósticos de caché
Caché en Redis de Azure proporciona la capacidad de almacenar datos de diagnóstico en una cuenta de almacenamiento para que pueda utilizar cualquier herramienta que desee a fin de obtener acceso y procesar los datos directamente. Para recopilar, almacenar y mostrar diagnósticos de caché en el Portal de Azure, se debe configurar una cuenta de almacenamiento. Las memorias caché de la misma región y suscripción comparten la misma cuenta de almacenamiento de información de diagnóstico y, al cambiar de configuración, esta se aplica a todas las cachés de la suscripción que se encuentran en dicha región.

Para habilitar y configurar diagnósticos de caché, vaya a la hoja **Caché en Redis** para la instancia de caché. Si los diagnósticos no están habilitados aún, se muestra un mensaje en lugar de un gráfico de diagnóstico.

![Habilitación de diagnósticos de caché][redis-cache-enable-diagnostics]

Haga clic en el mensaje para abrir la hoja **Métrica** y seleccione **Configuración de diagnóstico** para habilitar y configurar las opciones de diagnóstico de la instancia del servicio de caché.

![Configuración de diagnóstico][redis-cache-diagnostic-settings]

![Configuración de diagnóstico][redis-cache-configure-diagnostics]

Haga clic en el botón **Activar** para habilitar diagnósticos de caché y mostrar la configuración de diagnóstico.

Haga clic en la flecha situada a la derecha de **Cuenta de almacenamiento** para seleccionar una cuenta de almacenamiento que contenga datos de diagnóstico. Para obtener el mejor rendimiento, seleccione una cuenta de almacenamiento en la misma región que la memoria caché.

Después de establecer la configuración de diagnóstico, haga clic en **Guardar** para guardar la configuración. Tenga en cuenta que los cambios pueden tardar unos minutos en surtir efecto.

> [!IMPORTANT]
> Las memorias caché de igual región y suscripción comparten la misma configuración de almacenamiento de diagnóstico y, cuando se cambia de configuración (diagnóstico habilitado o deshabilitado o cambio de cuenta de almacenamiento), esta se aplica a todas las cachés de la suscripción que se encuentran en dicha región.
> 
> 

Para ver las métricas almacenadas, examine las tablas de la cuenta de almacenamiento con nombres que empiezan por `WADMetrics`. Para obtener más información acerca del acceso a las métricas almacenadas fuera del Portal de Azure, consulte el ejemplo [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) (Acceso a datos de supervisión de Caché en Redis).

> [!NOTE]
> Solo se muestran en el Portal de Azure las métricas que se almacenan en la cuenta de almacenamiento seleccionada. Si cambia las cuentas de almacenamiento, los datos de la cuenta de almacenamiento configurada anteriormente siguen estando disponibles para su descarga, pero no se muestran en el Portal de Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponibles e intervalos de informes
Los informes de las métricas de caché se generan utilizando diferentes intervalos, como **Última hora**, **Hoy**, **Semana anterior** y **Personalizado**. La hoja **Métrica** para cada gráfico de métricas muestra los valores promedio, mínimos y máximo para cada métrica del gráfico, y algunas métricas muestran un total para el intervalo de informes. 

Cada métrica incluye dos versiones. Una métrica mide el rendimiento de toda la memoria caché. En el caso de las memorias caché que utilizan la [agrupación en clústeres](cache-how-to-premium-clustering.md), se usa otra versión de la métrica que incluye `(Shard 0-9)` en el nombre para medir el rendimiento de una sola partición de la memoria caché. Por ejemplo, si una memoria caché tiene 4 particiones, `Cache Hits` es la cantidad total de resultados en toda la memoria caché, y `Cache Hits (Shard 3)` refleja simplemente los resultados para esa partición de la memoria caché.

> [!NOTE]
> Incluso cuando la memoria caché está inactiva sin ninguna aplicación de cliente activa conectada, puede que vea alguna actividad de caché, como clientes conectados, uso de memoria y operaciones que se están realizando. Esta actividad es normal durante la operación de una instancia de Caché en Redis de Azure.
> 
> 

| Métrica | Description |
| --- | --- |
| Aciertos de caché |El número de búsquedas de claves correctas durante el intervalo de informes. Este valor se asigna a `keyspace_hits` desde el comando [INFO](http://redis.io/commands/info) de Redis. |
| Errores de caché |El número de búsquedas de claves incorrectas durante el intervalo de informes. Este valor se asigna a `keyspace_misses` desde el comando INFO de Redis. Los errores de caché no significan necesariamente que haya un problema con la memoria caché. Por ejemplo, cuando se utiliza el modelo de programación cache-aside, una aplicación busca un elemento en primer lugar en la memoria caché. Si el elemento no está allí (error de caché), se recupera de la base de datos y se agrega a la caché para la próxima vez. Los errores de caché son un comportamiento normal del modelo de programación cache-aside. Si el número de errores de caché es mayor de lo esperado, examine la lógica de aplicación que rellena y lee de la memoria caché. Si se expulsan los elementos de la memoria caché debido a la presión de memoria, puede haber algunos errores de caché; una métrica mejor para supervisar la presión de memoria sería `Used Memory` o `Evicted Keys`. |
| Clientes conectados |El número de conexiones de clientes a la caché durante el intervalo de informes especificado. Este valor se asigna a `connected_clients` desde el comando INFO de Redis. Cuando se alcanza el [límite de conexión](cache-configure.md#default-redis-server-configuration) , se producirá un error en los intentos de conexión posteriores a la caché. Tenga en cuenta que incluso si no hay ninguna aplicación de cliente activa, puede haber algunas instancias de clientes conectadas debido a procesos y conexiones internos. |
| Claves expulsadas |El número de elementos que se elimina de la caché durante el intervalo de informes especificado debido al límite `maxmemory` . Este valor se asigna a `evicted_keys` desde el comando INFO de Redis. |
| Claves expiradas |El número de elementos expirados de la caché durante el intervalo de informes especificado. Este valor se asigna a `expired_keys` desde el comando INFO de Redis. |
| Gets |El número de operaciones get de la caché durante el intervalo de informes especificado. Este valor es la suma de los siguientes valores de todos los comandos INFO de Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` y `cmdstat_getrange`, y es equivalente a la suma de aciertos y errores de caché durante el intervalo de informes. |
| Carga de servidor de Redis |El porcentaje de ciclos en el que el servidor de Redis está ocupado procesando y no esperando a los mensajes inactivo. Si este contador llega a 100, significa que el servidor de Redis ha llegado a un límite de rendimiento y la CPU no puede procesar el trabajo más rápidamente. Si ve alta carga del servidor de Redis, verá las excepciones de tiempo de espera en el cliente. En este caso, debería considerar la posibilidad de un escalado vertical o el particionamiento de los datos en varias cachés. |
| Sets |El número de operaciones set a la caché durante el intervalo de informes especificado. Este valor es la suma de los siguientes valores de todos los comandos INFO de Redis: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` y `cmdstat_setnx`. |
| Total de operaciones |El número total de comandos procesados por el servidor de caché durante el intervalo de informes especificado. Este valor se asigna a `total_commands_processed` desde el comando INFO de Redis. Tenga en cuenta que cuando se utiliza Caché en Redis de Azure simplemente para Pub/Sub, no habrá ninguna métrica para `Cache Hits`, `Cache Misses`, `Gets` o `Sets`, pero habrá métricas `Total Operations` que reflejan el uso de la memoria caché para operaciones Pub/Sub. |
| Memoria usada |La cantidad de memoria caché usada para pares clave-valor en la memoria caché en MB durante el intervalo de informes especificado. Este valor se asigna a `used_memory` desde el comando INFO de Redis. Esto no incluye los metadatos o la fragmentación. |
| Memoria RSS usada |La cantidad de memoria caché usada en MB durante el intervalo de informes especificado, incluida la fragmentación y los metadatos. Este valor se asigna a `used_memory_rss` desde el comando INFO de Redis. |
| CPU |El uso de CPU del servidor de Caché en Redis de Azure como porcentaje durante el intervalo de informes especificado. Este valor se asigna al contador de rendimiento `\Processor(_Total)\% Processor Time` del sistema operativo. |
| Lectura de caché |La cantidad de datos que se leen de la memoria caché en megabytes por segundo (MB/s) durante el intervalo de informes especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es específica de Redis. **Este valor corresponde al ancho de banda de red que emplea esta caché. Si desea configurar alertas para los límites de ancho de banda de red del lado servidor, hágalo mediante este contador `Cache Read`. Consulte [esta tabla](cache-faq.md#cache-performance) para conocer los límites de ancho de banda de los diferentes tamaños y planes de tarifa de caché.** |
| Escritura de caché |La cantidad de datos que se escriben en la memoria caché en megabytes por segundo (MB/s) durante el intervalo de informes especificado. Este valor se deriva de las tarjetas de interfaz de red que admiten la máquina virtual que hospeda la caché y no es específica de Redis. Este valor corresponde al ancho de banda de red de los datos enviados a la memoria caché desde el cliente. |

## <a name="how-to-view-metrics-and-customize-charts"></a>Cómo ver métricas y personalizar gráficos de métricas
Puede ver una descripción general de las métricas de la caché en la hoja **Métricas de Redis** . Para acceder a la hoja **Métricas de Redis**, seleccione **Toda la configuración** > **Métricas de Redis**.

![Caché en Redis][redis-cache-redis-metrics]

La hoja **Métricas de Redis** contiene los siguientes gráficos.

| Gráfico de métricas de Redis | Métricas mostradas |
| --- | --- |
| Lectura y escritura de caché |Lectura de caché |
| Escritura de caché | |
| Clientes conectados |Clientes conectados |
| Aciertos y errores |Aciertos de caché |
| Errores de caché | |
| Número total de comandos |Total de operaciones |
| Gets y Sets |Gets |
| Sets | |
| Uso de CPU |CPU |
| Uso de la memoria |Memoria usada |
| Memoria RSS usada | |
| Carga de servidor de Redis |Carga de servidor |
| Recuento de claves |Total de claves |
| Claves expulsadas | |
| Claves expiradas | |

Si desea obtener una vista más detallada de las métricas de un gráfico específico y personalizar este gráfico, en la hoja **Métricas de Redis**, haga clic en el gráfico correspondiente para abrir la hoja **Métrica** de dicho gráfico.

![Cuadro de métricas][redis-cache-metric-blade]

Todas las alertas establecidas sobre las métricas que se muestran en un gráfico se incluyen en la parte inferior de la hoja **Métrica** de ese gráfico.

Para agregar o quitar métricas o cambiar el intervalo de informes, elija **Editar gráfico**.

Para agregar o quitar métricas del gráfico, haga clic en la casilla situada junto al nombre de la métrica. Para cambiar el intervalo de informes, haga clic en el intervalo que quiera. Para cambiar el **Tipo de gráfico**, haga clic en el estilo que desee. Una vez realizados los cambios, haga clic en **Guardar**. 

![Editar gráfico][redis-cache-edit-chart]

Si hace clic en **Guardar**, los cambios se mantendrán hasta que salga de la hoja **Métrica**. Cuando regrese en otro momento, volverá a ver la métrica y el intervalo de tiempo originales. Para más información sobre la personalización de gráficos, consulte [Monitor service metrics](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) (Introducción a las métricas de Microsoft Azure).

Para ver las métricas de un período de tiempo concreto en un gráfico, mantenga el mouse sobre una de las barras o los puntos específicos del gráfico que corresponda a la hora que quiera ver, y se mostrarán las métricas de ese intervalo.

![Visualización de detalles del gráfico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Cómo supervisar una caché premium con agrupación en clústeres
Las cachés premium que tienen la [agrupación en clústeres](cache-how-to-premium-clustering.md) habilitada pueden tener hasta 10 particiones. Cada partición tiene su propia métricas y estas métricas se agregan para proporcionar las métricas para la caché como un todo. Cada métrica incluye dos versiones. Una métrica mide el rendimiento de toda la memoria caché y una segunda versión de la métrica que incluye `(Shard 0-9)` en el nombre mide el rendimiento de una sola partición en una memoria caché. Por ejemplo, si una memoria caché tiene 3 particiones, `Cache Hits` es la cantidad total de resultados en toda la memoria caché y `Cache Hits (Shard 2)` refleja simplemente los resultados para esa partición de la memoria caché.

Cada gráfico de supervisión muestra las métricas de nivel superior de la memoria caché junto con las métricas para cada partición de memoria caché.

![Supervisión][redis-cache-premium-monitor]

Al mantener el puntero sobre los puntos de datos se muestran los detalles de ese punto en el tiempo. 

![Supervisión][redis-cache-premium-point-summary]

Los valores mayores suelen ser los valores agregados para la memoria caché, mientras que los valores más pequeños son las métricas individuales para la partición. Tenga en cuenta que en este ejemplo hay tres particiones y los aciertos de caché se distribuyen de manera uniforme entre las particiones.

![Supervisión][redis-cache-premium-point-shard]

Para ver más detalles, haga clic en el gráfico para ver una vista expandida en la hoja **Métrica** .

![Supervisión][redis-cache-premium-chart-detail]

De forma predeterminada cada gráfico incluye el contador de rendimiento de caché de nivel superior, así como los contadores de rendimiento para las particiones individuales. Puede personalizarlos en la hoja **Editar gráfico** .

![Supervisión][redis-cache-premium-edit]

Para obtener más información sobre los contadores de rendimiento disponibles, vea [Métricas disponibles e intervalos de informes](#available-metrics-and-reporting-intervals).

## <a name="operations-and-alerts"></a>Operaciones y alertas
La sección **Operaciones** de la hoja **Redis Cache** contiene las secciones **Eventos** y **Reglas de alerta**.

![Operaciones][redis-cache-operations-events]

Para ver una lista de las operaciones recientes de la memoria caché, haga clic en el gráfico **Eventos**. De este modo, se abrirá la hoja **Eventos**. Algunos ejemplos de operaciones son la recuperación y regeneración de claves de acceso, y la activación y resolución de reglas de alerta. Para más información sobre cada evento, haga clic en el evento en la hoja **Eventos** .

Para más información sobre los eventos, consulte [View events and audit logs](../monitoring-and-diagnostics/insights-debugging-with-events.md) (Consulta de eventos y registros de auditoría).

La sección **Reglas de alerta** muestra el recuento de alertas de la instancia de la caché. Una regla de alerta le permite supervisar la instancia de la caché y recibir un correo electrónico cada vez que el valor de una métrica determinada alcanza el umbral definido en la regla. 

Las reglas de alerta se evalúan cada cinco minutos, aproximadamente, y cuando se activa una regla de alerta, se envían las notificaciones configuradas. Las notificaciones y las activaciones de reglas de alerta no se procesan de forma instantánea: puede haber un retraso de unos minutos hasta que la regla de alerta se active y se envíen las notificaciones.

Las reglas de alerta pueden consultarse y configurarse en la hoja **Métrica** de un gráfico de supervisión concreto o en la hoja **Reglas de alerta**.

Las reglas de alerta tienen las siguientes propiedades.

| Propiedad de regla de alerta | Description |
| --- | --- |
| Recurso |Recurso evaluado por la regla de alerta. Al crear una regla de alerta en una caché de Redis, la memoria caché es el recurso. |
| Nombre |Nombre que identifica de forma única la regla de alerta en la instancia de caché actual. |
| Description |Descripción opcional de la regla de alerta. |
| Métrica |Métrica supervisada por la regla de alerta. Para ver una lista de métricas de caché, consulte Métricas disponibles e intervalos de informes. |
| Condición |Operador de condición para la regla de alerta. Las opciones posibles son: mayor que, mayor o igual que, menor que, menor o igual que |
| Umbral |El valor que se utiliza para comparar con la métrica mediante el operador especificado por la propiedad condition. Dependiendo de la métrica, este valor puede ser bytes por segundo, bytes, % o recuento. |
| Período |Especifica el período en el que se utiliza el valor medio de la métrica para la comparación de la regla de alerta. Por ejemplo, si el período es Durante la última hora, se utiliza para la comparación el valor medio de la métrica en el intervalo de hora anterior. Si desea recibir una notificación al alcanzar el umbral debido a un nivel máximo de actividad, lo adecuado es elegir un periodo más corto. Para recibir una notificación cuando haya una actividad sostenida por encima del umbral, utilice un período más largo. |
| Servicio de correo electrónico y coadministradores |Cuando es true, se envía un correo electrónico al administrador y al coadministrador del servicio cuando la alerta de dispara. |
| Correo electrónico de administrador adicional |Dirección de correo electrónico opcional para un administrador adicional al que notificar cuando se activa la alerta. |

Solo se envía una notificación por cada activación de regla de alerta. Una vez que se supera el umbral de una regla y se envía una notificación, la regla no se vuelve a evaluar hasta que la métrica descienda por debajo del umbral. Si la métrica supera el umbral más tarde, se vuelve a activar la alerta y se envía una nueva notificación.

Para ver todas las reglas de alerta de la instancia de su memoria caché, haga clic en la sección **Reglas de alerta** de la hoja **Redis Cache**. Para ver solo las reglas de alerta que usan una métrica específica, vaya a la hoja **Métrica** del gráfico que contiene esa métrica.

![Las reglas de alertas][redis-cache-alert-rules]

Para agregar una regla de alerta, en la hoja **Métrica** o **Reglas de alerta**, haga clic en **Agregar alerta**. 

En la hoja de la regla **Agregar una alerta**, especifique los criterios de regla correspondientes y haga clic en **Aceptar**. 

![Incorporación de regla de alerta][redis-cache-add-alert]

> [!NOTE]
> Si crea una regla de alerta haciendo clic en la opción **Agregar alerta** de la hoja **Métrica**, en el cuadro desplegable **Métrica**, solo se mostrarán las métricas que aparecen en el gráfico de esa hoja. Si crea una regla de alerta haciendo clic en la opción **Agregar alerta** de la hoja **Reglas de alerta**, en el menú desplegable **Métrica**, se mostrarán todas las métricas de caché.
> 
> 

Al guardar una regla de alerta, esta aparece en la hoja **Reglas de alerta**, así como en la hoja **Métrica** de todos los gráficos en los que se muestra la métrica utilizada en la regla de alerta. Para editar una regla de alerta, haga clic en su nombre para que aparezca la hoja **Editar regla** . En la hoja **Editar regla** puede editar las propiedades de la regla, eliminar o deshabilitar la regla de alerta o volver a habilitarla si se deshabilitó anteriormente.

> [!NOTE]
> Si se realizan cambios en las propiedades de la regla, estos pueden tardar unos instantes en reflejarse en la hoja **Reglas de alerta** o **Métrica**.
> 
> 

Cuando se activa una regla de alerta, se envía un correo electrónico en función de la configuración de dicha alerta. También aparece un icono en la sección **Reglas de alerta** de la hoja **Redis Cache**.

Se considera que una regla de alerta se resuelve cuando la condición de alerta ya no se evalúa como true. Una vez que se resuelve la condición de regla de alerta, el icono de alerta se reemplaza por una marca de verificación. Si necesita más información sobre la activación y resolución de alertas, haga clic en la sección **Eventos** de la hoja **Redis Cache** para consultar los eventos en la hoja **Eventos**.

Para más información sobre las alertas de Azure, consulte [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) (Recepción de notificaciones de alerta).

## <a name="metrics-on-the-redis-cache-blade"></a>Métricas de la hoja Caché en Redis
La hoja **Caché en Redis** muestra las siguientes categorías de métricas.

* [Gráficos de supervisión](#monitoring-charts)
* [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de supervisión
La sección **Supervisión** contiene los gráficos **Aciertos y errores**, **Obtenciones y configuraciones**, **Conexiones** y **Comandos totales**.

![Gráficos de supervisión][redis-cache-monitoring-part]

Los gráficos de **Supervisión** muestran las siguientes métricas.

| Gráfico de supervisión | Métricas de caché |
| --- | --- |
| Aciertos y errores |Aciertos de caché |
| Errores de caché | |
| Gets y Sets |Gets |
| Sets | |
| Conexiones |Clientes conectados |
| Número total de comandos |Total de operaciones |

Para obtener información sobre cómo ver las métricas y personalizar cada gráfico de esta sección, consulte la sección [Cómo ver métricas y personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) , más adelante.

### <a name="usage-charts"></a>Gráficos de uso
La sección **Uso** contiene los gráficos **Carga del servidor Redis**, **Uso de memoria**, **Banda de banda de la red** y **Uso de CPU** y muestra el **plan de tarifa** de la instancia de caché.

![Gráficos de uso][redis-cache-usage-part]

El **Nivel de precios** muestra el nivel de precios de caché y se puede utilizar para [escalar](cache-how-to-scale.md) la memoria caché a un nivel de precios diferente.

Los gráficos de **Uso** muestran las siguientes métricas.

| Gráfico de uso | Métricas de caché |
| --- | --- |
| Carga de servidor de Redis |Carga de servidor |
| Uso de la memoria |Memoria usada |
| Ancho de banda de red |Escritura de caché |
| Uso de CPU |CPU |

Para obtener información sobre cómo ver las métricas y personalizar cada gráfico de esta sección, consulte la sección [Cómo ver métricas y personalizar gráficos de métricas](#how-to-view-metrics-and-customize-charts) , más adelante.

<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png






<!--HONumber=Jan17_HO2-->


