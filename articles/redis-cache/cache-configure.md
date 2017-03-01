---
title: "Configuración de Azure Redis Cache | Microsoft Docs"
description: "Descripción de la configuración predeterminada de Caché en Redis de Azure y más información sobre cómo configurar las instancias de Caché en Redis de Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 296a842157e4ecae1b3700e1d22c56852ffc06a2
ms.openlocfilehash: ad88d998302d264b2a0f607d8408cd12bf833a68
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-configure-azure-redis-cache"></a>Configuración de Caché en Redis de Azure
En este tema se describe cómo revisar y actualizar la configuración de las instancias de Caché en Redis de Azure y se trata la configuración predeterminada del servidor Redis para las instancias de Caché en Redis de Azure.

> [!NOTE]
> Para más información sobre cómo configurar y usar las características de caché premium, consulte [Cómo configurar la persistencia](cache-how-to-premium-persistence.md), [Cómo configurar la agrupación en clústeres un clúster](cache-how-to-premium-clustering.md) y [Cómo configurar la compatibilidad con Virtual Network](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Configuración de opciones de la memoria caché en Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

La configuración de Azure Redis Cache se ve y configura en la hoja **Redis Cache** mediante el **menú Recursos**.

![Caché en Redis - Configuración](./media/cache-configure/redis-cache-settings.png)

Puede ver y configurar las siguientes opciones con el **menú Recursos**.

* [Información general](#overview)
* [Registro de actividad](#activity-log)
* [Control de acceso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnóstico y solución de problemas](#diagnose-and-solve-problems)
* [Configuración](#settings)
    * [Claves de acceso](#access-keys)
    * [Configuración avanzada](#advanced-settings)
    * [Asesor de caché en Redis](#redis-cache-advisor)
    * [Plan de tarifa](#pricing-tier)
    * [Tamaño del Clúster en Redis](#cluster-size)
    * [Persistencia de datos de Redis](#redis-data-persistence)
    * [Programar actualizaciones](#schedule-updates)
    * [Virtual Network](#virtual-network)
    * [Propiedades](#properties)
    * [Bloqueos](#locks)
    * [Script de automatización](#automation-script)
* [Administración](#administration)
    * [Importación de datos](#importexport)
    * [Exportación de datos](#importexport)
    * [Reboot](#reboot)
* [Supervisión](#monitoring)
    * [Métricas de Redis](#redis-metrics)
    * [Reglas de alertas](#alert-rules)
    * [Diagnóstico](#diagnostics)
* [Configuración de soporte técnico y solución de problemas](#support-amp-troubleshooting-settings)
    * [Estado de los recursos](#resource-health)
    * [Nueva solicitud de soporte técnico](#new-support-request)


## <a name="overview"></a>Información general

**Información general** proporciona también con información básica sobre la memoria caché, como el nombre, puertos, plan de tarifa y las métricas de la caché seleccionada.

### <a name="activity-log"></a>Registro de actividades

Haga clic en **Registro de auditoría** para ver las acciones realizadas en la memoria caché. También puede usar el filtrado para expandir esta vista e incluir otros recursos. Para más información sobre el uso de registros de auditoría, consulte [View events and audit logs](../monitoring-and-diagnostics/insights-debugging-with-events.md) (Consulta de eventos y registros de auditoría) y [Audit operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) (Operaciones de auditoría con Resource Manager). Para obtener más información sobre la supervisión de eventos de Caché en Redis de Azure, consulte [Operaciones y alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Control de acceso (IAM)

La sección **Control de acceso (IAM)** ofrece compatibilidad con el control de acceso basado en roles (RBAC) en Azure Portal para ayudar a las organizaciones a satisfacer sus requisitos de administración de acceso de forma simple y precisa. Para más información, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

### <a name="tags"></a>Etiquetas

La sección **Etiquetas** le ayuda a organizar sus recursos. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar y solucionar problemas

Haga clic en **Diagnosticar y solucionar problemas** para ver problemas comunes y estrategias para resolverlos.



## <a name="settings"></a>Settings
La sección **Configuración** permite acceder a los siguientes ajustes de la memoria caché y configurarlos.

![Settings](./media/cache-configure/redis-cache-general-settings.png)

* [Claves de acceso](#access-keys)
* [Configuración avanzada](#advanced-settings)
* [Asesor de caché en Redis](#redis-cache-advisor)
* [Plan de tarifa](#pricing-tier)
* [Tamaño del Clúster en Redis](#cluster-size)
* [Persistencia de datos de Redis](#redis-data-persistence)
* [Programar actualizaciones](#schedule-updates)
* [Virtual Network](#virtual-network)
* [Propiedades](#properties)
* [Bloqueos](#locks)
* [Script de automatización](#automation-script)



### <a name="access-keys"></a>Claves de acceso
Haga clic en **Claves de acceso** para ver o volver a generar las claves de acceso de la memoria caché. Estas claves las usan los clientes que se conectan a la caché.

![Caché en Redis - Claves de acceso](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Configuración avanzada
Los siguientes ajustes se configuran en la hoja **Configuración avanzada**.

* [Puertos de acceso](#access-ports)
* [Maxmemory-policy y maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)
* [Notificaciones de espacio de claves (configuración avanzada)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Puertos de acceso
El acceso no SSL está deshabilitado de forma predeterminada para las nuevas cachés. Para habilitar el puerto no SSL, haga clic en **No** en la opción **Permitir el acceso solo mediante SSL** de la hoja **Configuración avanzada** y seleccione **Guardar**.

![Caché en Redis - Puertos de acceso](./media/cache-configure/redis-cache-access-ports.png)

#### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Maxmemory-policy y maxmemory-reserved
Las opciones **Directiva de memoria máxima** y **maxmemory-reserved** de la hoja **Advanced settings** establecen las directivas de memoria de la memoria caché. La opción **maxmemory-policy** configura la directiva de expulsión de la memoria caché, mientras que la opción **maxmemory-reserved** configura la memoria reservada para procesos ajenos a la memoria caché.

![Caché en Redis - Directiva Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Directiva Maxmemory**: permite elegir entre las siguientes directivas de expulsión:

* `volatile-lru`: este es el valor predeterminado.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para más información sobre las directivas `maxmemory`, consulte [Eviction policies](http://redis.io/topics/lru-cache#eviction-policies) (Directivas de expulsión).

La opción **maxmemory-reserved** configura la cantidad de memoria en MB que se reserva para las operaciones ajenas a la memoria caché como, por ejemplo, la replicación durante la conmutación por error. También puede usarse cuando se tiene una alta relación de fragmentación. Esta opción le permite tener una experiencia más coherente de servidor Redis cuando varía la carga. Este valor debe establecerse más alto para cargas de trabajo con muchas operaciones de escritura. Cuando se reserva memoria para dichas operaciones, no está disponible para el almacenamiento de datos en caché.

> [!IMPORTANT]
> La opción **maxmemory-reserved** solo está disponible para las memorias caché de nivel Standard y Premium.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notificaciones de espacio de claves (configuración avanzada)
Las notificaciones de espacio de claves de Redis se configuran en la hoja **Configuración avanzada** . Las notificaciones de espacio de claves permiten que los clientes reciban notificaciones cuando se producen determinados eventos.

![Caché en Redis - Configuración avanzada](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Las notificaciones de espacio de claves y el valor **notify-keyspace-event** solo están disponibles para las memorias caché de nivel Standard y Premium.
> 
> 

Para obtener más información, vea [Notificaciones de espacio de claves de Redis](http://redis.io/topics/notifications). Para obtener el ejemplo de código, consulte el archivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) del ejemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Asesor de caché en Redis
La hoja **Redis Cache Advisor** muestra recomendaciones para la memoria caché. Durante las operaciones normales, no se muestra ninguna recomendación. 

![Recomendaciones](./media/cache-configure/redis-cache-no-recommendations.png)

Si se produce cualquier problema durante las operaciones de la memoria caché, como un uso elevado de la memoria, el ancho de banda de red o la carga del servidor, se muestra una alerta en la hoja **Caché en Redis** .

![Recomendaciones](./media/cache-configure/redis-cache-recommendations-alert.png)

Puede encontrar más información en la hoja **Recomendaciones** .

![Recomendaciones](./media/cache-configure/redis-cache-recommendations.png)

Estas métricas se pueden supervisar en las secciones [Monitoring charts](cache-how-to-monitor.md#monitoring-charts) (Gráficos de supervisión) y [Usage charts](cache-how-to-monitor.md#usage-charts) (Gráficos de uso) de la hoja **Redis Cache**.

Cada plan de tarifa tiene distintos límites para las conexiones de cliente, memoria y ancho de banda. Si la caché se aproxima a la capacidad máxima para estas métricas durante un período prolongado, se crea una recomendación. Para más información sobre las métricas y los límites que se revisan mediante la herramienta **Recomendaciones**, consulte la tabla siguiente:

| Métrica de Caché en Redis | Más información |
| --- | --- |
| Uso de ancho de banda de red |[Rendimiento de la caché: ancho de banda disponible](cache-faq.md#cache-performance) |
| Clientes conectados |[Configuración predeterminada del servidor Redis: maxclients](#maxclients) |
| Carga de servidor |[Gráficos de uso: carga del servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de la memoria |[Rendimiento y tamaño de la memoria caché](cache-faq.md#cache-performance) |

Para actualizar la memoria caché, haga clic en **Actualizar ahora** para cambiar el [plan de tarifa](#pricing-tier) y escalar la memoria caché. Para más información sobre cómo elegir un plan de tarifa, consulte [¿Qué oferta y tamaño de Redis Caché debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="pricing-tier"></a>Nivel de precios
Haga clic en el **Nivel de precios** para ver o cambiar el plan de tarifa para su memoria caché. Para obtener más información sobre el escalado, vea [Escalado de Caché en Redis de Azure](cache-how-to-scale.md).

![Nivel de precios de Caché en Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamaño del Clúster en Redis
Haga clic en **Tamaño del Clúster en Redis (VERSIÓN PRELIMINAR)** para cambiar el tamaño del clúster para una caché premium en ejecución con la agrupación de clústeres habilitada.

> [!NOTE]
> Tenga en cuenta que, a pesar de que el nivel Premium de Caché en Redis de Azure se publicó con disponibilidad general, la característica Tamaño del clúster en Redis está actualmente en la versión preliminar.
> 
> 

![Tamaño del Clúster en Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para modificar el tamaño del clúster, utilice el control deslizante o especifique un número comprendido entre 1 y 10 en el cuadro de texto **Número de particiones**. Después, haga clic en **Aceptar** para guardar.

> [!IMPORTANT]
> La agrupación en clústeres de Redis solo está disponible para las memorias cachés premium. Para obtener más información, consulte [Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistencia de datos de Redis
Haga clic en **Persistencia de los datos en Redis** para habilitar, deshabilitar o configurar la persistencia de los datos para la caché premium.

![Persistencia de datos de Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar la persistencia en Redis, haga clic en **Habilitada** para habilitar la copia de seguridad RDB (base de datos de Redis). Para deshabilitar la persistencia en Redis, haga clic en **Deshabilitada**.

Para configurar el intervalo de copia de seguridad, seleccione una de las siguientes entradas de **frecuencia de copia de seguridad** de la lista desplegable. 

- **15 minutos**
- **30 minutos**
- **60 minutos**
- **6 horas**
- **12 horas**
- **24 horas**

Este intervalo empieza la cuenta atrás cuando se completa correctamente la operación de copia de seguridad anterior y se inicia cuando se produce una nueva copia de seguridad.

Haga clic en **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento que quiere usar y, en el cuadro desplegable **Clave de almacenamiento**, elija la **clave principal** o la **clave secundaria** que se va a utilizar. Debe elegir una cuenta de almacenamiento en la misma región que la memoria caché y se recomienda una cuenta de **Almacenamiento Premium** porque el almacenamiento premium tiene un mayor rendimiento. En cualquier momento que se vuelva a generar la clave de almacenamiento para su cuenta de persistencia, debe volver a elegir la clave que quiera en la lista desplegable **Clave de almacenamiento**.

Haga clic en **Aceptar** para guardar la configuración de persistencia.

> [!IMPORTANT]
> La persistencia de los datos en Redis solo está disponible para las memorias cachés premium. Para más información, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md).
> 
> 

### <a name="schedule-updates"></a>Programar actualizaciones
La hoja **Programar actualizaciones** permite designar una ventana de mantenimiento para las actualizaciones del servidor de Redis para la caché. 

> [!IMPORTANT]
> El período de mantenimiento solo se aplica a las actualizaciones del servidor de Redis y no a las actualizaciones de Azure o del sistema operativo de las máquinas virtuales que hospedan la caché.
> 
> 

![Programar actualizaciones](./media/cache-configure/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, compruebe los días deseados, especifique la hora de inicio de la ventana de mantenimiento para cada día y haga clic en **Aceptar**. Tenga en cuenta que la hora de la ventana de mantenimiento está en formato UTC. 

> [!IMPORTANT]
> La funcionalidad **Programar actualizaciones** solo está disponible para cachés del nivel Premium. Para más información e instrucciones, consulte [Administración de la Caché en Redis de Azure - Programación de actualizaciones](cache-administration.md#schedule-updates).
> 
> 



## <a name="virtual-network"></a>Virtual Network
La sección **Virtual Network** le permite configurar las opciones de red virtual de la caché. Para más información sobre cómo crear una caché premium con compatibilidad para red virtual y cómo actualizar su configuración, consulte [Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> La configuración de red virtual solo está disponible para las memorias cachés premium que se configuraron con la compatibilidad de la red virtual durante la creación de la memoria caché. 
> 
> 


### <a name="properties"></a>Propiedades
Haga clic en **Propiedades** para ver información sobre la memoria caché, incluidos los puertos y el extremo de la caché.

![Caché en Redis - Propiedades](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueos
La sección **Bloqueos** permite bloquear una suscripción, un grupo de recursos o un recurso para evitar que otros usuarios de la organización eliminen o modifiquen por error recursos críticos. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script de automatización

Haga clic en **Script de automatización** para generar y exportar una plantilla de los recursos implementados para futuras implementaciones. Para más información sobre cómo trabajar con plantillas, consulte [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Configuración de administración
La configuración de la sección **Administración** permite realizar las siguientes tareas administrativas en la memoria caché premium. 

![Administración](./media/cache-configure/redis-cache-administration.png)

* [Importación de datos](#importexport)
* [Exportación de datos](#importexport)
* [Reboot](#reboot)


> [!IMPORTANT]
> La configuración de esta sección solo está disponible para las cachés de nivel Premium.
> 
> 

### <a name="importexport"></a>Importación/Exportación
Import/Export es una operación de administración de datos de Azure Redis Cache que permite importar y exportar datos en la caché mediante la importación y exportación de una instantánea de base de datos de Redis Caché (RDB) desde una caché premium a un blob en páginas en una cuenta de Azure Storage. Este método le permite migrar entre diferentes instancias de Azure Redis Cache o rellenar la caché de datos antes de su uso.

La importación se puede usar para traer los archivos RDB compatibles de Redis desde cualquier servidor de Redis que se ejecute en cualquier nube o entorno, incluidas las instancias de Redis que se ejecutan en Linux, Windows o cualquier proveedor de nube como, por ejemplo, Amazon Web Services. La importación de datos supone una manera fácil de crear una caché con datos rellenados previamente. Durante el proceso de importación, Azure Redis Cache carga los archivos RDB desde Azure Storage en la memoria y, a continuación, inserta las claves en la caché.

La exportación permite exportar los datos almacenados en Azure Redis Cache a archivos RDB compatibles. Puede utilizar esta característica para mover datos desde una instancia de Caché en Redis de Azure a otra o a otro servidor de Redis. Durante el proceso de exportación, se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Azure Redis Cache y el archivo se carga en la cuenta de almacenamiento designada. Una vez completada la operación de exportación (de manera correcta o incorrecta), se elimina el archivo temporal.

> [!IMPORTANT]
> Importación/Exportación solo está disponible para las memorias caché de nivel premium. Para más información e instrucciones, consulte [Importación y exportación de datos en la Caché en Redis de Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reboot
La hoja **Reiniciar** permite reiniciar los nodos de la caché. Esta funcionalidad de reinicio le permite probar la resistencia de la aplicación en caso de que haya un error de un nodo de la caché.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Si tiene una caché premium con la agrupación en clústeres habilitada, puede seleccionar qué particiones de la memoria caché se reiniciarán.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar uno o varios nodos de la caché, seleccione los nodos que prefiera y haga clic en **Reboot**(Reiniciar). Si tiene una caché premium con la agrupación en clústeres habilitada, seleccione las particiones para reiniciar y haga clic en **Reboot**(Reiniciar). Después de unos minutos, los nodos seleccionados se reinician y vuelven a estar en línea poco tiempo después.

> [!IMPORTANT]
> El reinicio solo está disponible para las memorias caché de nivel premium. Para más información e instrucciones, consulte [Administración de Caché en Redis de Azure - Reboot](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Supervisión

La sección **Supervisión** permite configurar los diagnósticos de Redis Cache. Para obtener más información sobre los diagnósticos y la supervisión de Azure Redis Cache, consulte [Supervisión de Azure Redis Cache](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas de Redis](#redis-metrics)
* [Reglas de alertas](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Caché en Redis
Haga clic en **Métricas de Redis** para [ver las métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) de la memoria caché.

### <a name="alert-rules"></a>Las reglas de alertas

Haga clic en **Reglas de alerta** para configurar las alertas basadas en métricas de Redis Cache. Para obtener más información, consulte [Operaciones y alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="diagnostics"></a>Diagnóstico

Haga clic en **Diagnóstico** para [configurar la cuenta de almacenamiento](cache-how-to-monitor.md#enable-cache-diagnostics) que se usa para almacenar los diagnósticos de la memoria caché.

![Caché en Redis - Diagnóstico](./media/cache-configure/redis-cache-diagnostics-settings.png)

## <a name="support--troubleshooting-settings"></a>Configuración de soporte técnico y solución de problemas
La configuración de la sección **Soporte y solución de problemas** proporciona opciones para resolver problemas con la memoria caché.

![Soporte y solución de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Estado de los recursos](#resource-health)
* [Nueva solicitud de soporte técnico](#new-support-request)

### <a name="resource-health"></a>Estado de los recursos
**estado de los recursos** supervisa el recurso e indica si se ejecuta del modo previsto. Para obtener más información sobre el servicio Estado de los recursos de Azure, consulte [Información general sobre Estado de los recursos de Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> El servicio Estado de los recursos no puede actualmente informar sobre el estado de las instancias de Caché en Redis de Azure hospedadas en una red virtual. Para más información, consulte [¿Funcionarán todas las características al alojar una caché en una red virtual?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nueva solicitud de soporte
Haga clic en **Nueva solicitud de soporte técnico** para abrir una solicitud de soporte técnico para la memoria caché.





## <a name="default-redis-server-configuration"></a>Configuración predeterminada del servidor Redis
Las nuevas instancias de Caché en Redis de Azure se configuran con los siguientes valores de configuración de Redis predeterminados.

> [!NOTE]
> No se puede cambiar la configuración de esta sección con el método `StackExchange.Redis.IServer.ConfigSet`. Si se llama a este método con uno de los comandos de esta sección, se produce una excepción similar a la siguiente:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Es posible configurar aquellos valores que permitan esta opción, como **max-memory-policy**, a través de Azure Portal o las herramientas de administración de la línea de comandos como la CLI de Azure o PowerShell.
> 
> 

| Configuración | Valor predeterminado | Descripción |
| --- | --- | --- |
| `databases` |16 |El número predeterminado de bases de datos es 16, pero se puede configurar otro número en función del plan de tarifa.<sup>1</sup> La base de datos predeterminada es DB 0, pero se puede seleccionar otra por conexión mediante `connection.GetDatabase(dbid)`, donde `dbid` es un número entre `0` y `databases - 1`. |
| `maxclients` |Depende del plan de tarifa<sup>2</sup> |Se trata del número máximo de clientes conectados que se permiten al mismo tiempo. Una vez alcanzado el límite, Redis cierra todas las conexiones nuevas y devuelve un error de "número máximo alcanzado de clientes". |
| `maxmemory-policy` |`volatile-lru` |La directiva maxmemory es la configuración de cómo Redis selecciona lo que se debe quitar cuando se alcanza `maxmemory` (el tamaño de la oferta de caché que seleccionó al crear la caché). Con Azure Redis Cache, la configuración predeterminada es `volatile-lru`, que quita las claves con una fecha de expiración definida mediante un algoritmo LRU. Esta opción puede configurarse en el Portal de Azure. Para más información, vea [Maxmemory-policy y maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved). |
| `maxmemory-sample`s |3 |Para ahorrar memoria, los algoritmos LRU y TTL mínimo son algoritmos aproximados en lugar de algoritmos precisos. De forma predeterminada, Redis comprueba tres claves y selecciona la que se ha usado menos recientemente. |
| `lua-time-limit` |5.000 |Tiempo máximo de ejecución de un script Lua en milisegundos. Si se alcanza el tiempo máximo de ejecución, Redis registra que un script está aún en ejecución una vez transcurrido el tiempo máximo permitido y empieza a responder a las consultas con un error. |
| `lua-event-limit` |500 |Tamaño máximo de la cola de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Los límites de búfer de salida de cliente pueden usarse para forzar la desconexión de clientes que, por algún motivo (un motivo habitual es que un cliente de Pub/Sub no puede consumir mensajes tan rápidamente como el publicador los crea), no leen datos del servidor con suficiente rapidez. Para más información, vea [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>El límite de `databases` es diferente en cada plan de tarifa de Azure Redis Cache y puede establecerse al crear la memoria caché. Si no se especifica la configuración de `databases` al crear la memoria caché, el valor predeterminado es 16.

* Cachés Basic y Standard
  * Memoria caché C0 (250 MB): hasta 16 bases de datos
  * Memoria caché C1 (1 GB): hasta 16 bases de datos
  * Memoria caché C2 (2,5 GB): hasta 16 bases de datos
  * Memoria caché C3 (6 GB): hasta 16 bases de datos
  * Memoria caché C4 (13 GB): hasta 32 bases de datos
  * Memoria caché C5 (26 GB): hasta 48 bases de datos
  * Memoria caché C6 (53 GB): hasta 64 bases de datos
* Cachés Premium
  * P1 (6 GB - 60 GB): hasta 16 bases de datos
  * P2 (13 GB - 130 GB): hasta 32 bases de datos
  * P3 (26 GB - 260 GB): hasta 48 bases de datos
  * P4 (53 GB - 530 GB): hasta 64 bases de datos
  * Todas las memorias caché premium con clúster de Redis habilitado: el clúster de Redis solo admite el uso de la base de datos 0 con el fin de que el límite `databases` para cualquier caché premium con clúster de Redis habilitado sea 1; el comando [Select](http://redis.io/commands/select) no se admite. Para más información, consulte [Configuración de Caché en Redis de Azure](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

> [!NOTE]
> Los ajustes `databases` solo se puede configurar al crear la memoria caché y solo mediante PowerShell, la CLI u otros clientes de administración. Para ver un ejemplo de configuración de `databases` al crear la memoria caché mediante PowerShell, consulte [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients` es diferente en cada plan de tarifa de Azure Redis Cache.

* Cachés Basic y Standard
  * Memoria caché C0  (250 MB): hasta 256 conexiones
  * Memoria caché C1 (1 GB): hasta 1000 conexiones
  * Memoria caché C2 (2,5 GB): hasta 2000 conexiones
  * Memoria caché C3 (6 GB): hasta 5000 conexiones
  * Memoria caché C4 (13 GB): hasta 10 000 conexiones
  * Memoria caché C5 (26 GB): hasta 15 000 conexiones
  * Memoria caché C6 (53 GB): hasta 20 000 conexiones
* Cachés Premium
  * P1 (6 GB - 60 GB): hasta 7.500 conexiones
  * P2 (13 GB - 130 GB): hasta 15.000 conexiones
  * P3 (26 GB - 260 GB): hasta 30.000 conexiones
  * P4 (53 GB - 530 GB): hasta 40.000 conexiones

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>No se admiten comandos de Redis en Caché en Redis de Azure
> [!IMPORTANT]
> Dado que Microsoft administra la configuración y la administración de instancias de Azure Redis Cache, los siguientes comandos están deshabilitados. Si intenta invocarlos, recibirá un mensaje de error similar a `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * Guardar
> * SHUTDOWN
> * SLAVEOF
> * CLÚSTER: los comandos de escritura del clúster están deshabilitados, pero se permiten los comandos de solo lectura del clúster.
> 
> 

Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Consola de Redis
Puede emitir comandos de forma segura para sus instancias de Caché en Redis de Azure con la **consola de Redis**, que está disponible para las memorias caché Standard y Premium.

> [!IMPORTANT]
> La Consola de Redis no funciona con redes virtuales, agrupaciones en clústeres y bases de datos distintas de 0. 
> 
> * [Red virtual](cache-how-to-premium-vnet.md) : cuando la memoria caché forma parte de una red virtual, solo los clientes de la red virtual pueden tener acceso a la memoria caché. Dado que la Consola de Redis usa al cliente de redis-cli.exe hospedado en máquinas virtuales que no forman parte de su red virtual, no se puede conectar a su memoria caché.
> * [Agrupación en clústeres](cache-how-to-premium-clustering.md): la consola de Redis usa el cliente de redis-cli.exe, que no es compatible con la agrupación en clústeres en este momento. La utilidad redis-cli de la rama [inestable](http://redis.io/download) del repositorio de Redis que se encuentra en GitHub implementa la compatibilidad básica cuando se inicia con el conmutador `-c`. Para más información, consulte la sección [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Jugar con el clúster) del [tutorial de clústeres de Redis](http://redis.io) que encontrará en [http://redis.io](http://redis.io/topics/cluster-tutorial).
> * La Consola de Redis realiza una nueva conexión a la base de datos 0 cada vez que envía un comando. No se puede utilizar el comando `SELECT` para seleccionar otra base de datos, ya que se restablece a 0 con cada comando. Para obtener información acerca de cómo ejecutar comandos de Redis, incluyendo el cambio de base de datos, consulte [¿Cómo puedo ejecutar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands)
> 
> 

Para acceder a la Consola de Redis, en la hoja **Redis Cache**, haga clic en **Consola**.

![Consola de Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos con una instancia de la memoria caché, basta con escribir en el comando que desea en la consola.

![Consola de Redis](./media/cache-configure/redis-console.png)

Para obtener una lista de los comandos de Redis deshabilitados para Caché en Redis de Azure, vea la sección anterior [No se admiten comandos de Redis en Caché en Redis de Azure](#redis-commands-not-supported-in-azure-redis-cache) . Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Traslado de la memoria caché a una nueva suscripción
Para mover la memoria caché a una nueva suscripción, haga clic en **Mover**.

![Traslado de Caché en Redis](./media/cache-configure/redis-cache-move.png)

Para obtener información acerca de cómo mover recursos de un grupo de recursos a otro y de una suscripción a otra, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo trabajar con los comandos de Redis, consulte [¿Cómo puedo ejecutar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands).


