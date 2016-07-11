<properties 
	pageTitle="Configuración de Caché en Redis de Azure | Microsoft Azure"
	description="Descripción de la configuración predeterminada de Caché en Redis de Azure y más información sobre cómo configurar las instancias de Caché en Redis de Azure"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="06/29/2016"
	ms.author="sdanie" />

# Configuración de Caché en Redis de Azure

En este tema se describe cómo revisar y actualizar la configuración de las instancias de Caché en Redis de Azure y se trata la configuración predeterminada del servidor Redis para las instancias de Caché en Redis de Azure.

>[AZURE.NOTE] Para obtener más información sobre la configuración y el uso de las características de caché Premium, vea [Cómo configurar la persistencia para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md), [Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md) y [Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

## Configuración de opciones de la memoria caché en Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Caché en Redis de Azure ofrece la siguiente configuración en la hoja **Configuración**.

![Caché en Redis - Configuración](./media/cache-configure/redis-cache-settings.png)



-	[Configuración de soporte técnico y solución de problemas](#support-amp-troubleshooting-settings)
-	[Configuración general](#general-settings)
	-	[Propiedades](#properties)
	-	[Claves de acceso](#access-keys)
	-	[Puertos de acceso](#access-ports)
	-	[Directiva Maxmemory](#maxmemory-policy-and-maxmemory-reserved)
	-	[Configuración avanzada (notificaciones de Keyspace)](#keyspace-notifications-advanced-settings)
	-	[Asesor de caché en Redis](#redis-cache-advisor)
-	[Configuración de escala](#scale-settings)
	-	[Nivel de precios](#pricing-tier)
	-	[Tamaño del Clúster en Redis](#cluster-size)
-	[Configuración de administración de datos](#data-management-settings)
	-	[Persistencia de datos de Redis](#redis-data-persistence)
	-	[Importación/Exportación](#importexport)
-	[Configuración de administración](#administration-settings)
	-	[Reboot](#reboot)
	-	[Programar actualizaciones](#schedule-updates)
-	[Configuración de diagnóstico](#diagnostics-settings)
-	[Configuración de red](#network-settings)
-	[Configuración de administración de recursos](#resource-management-settings)

## Configuración de soporte técnico y solución de problemas

La configuración de la sección **Soporte y solución de problemas** proporciona opciones para resolver problemas con la memoria caché.

![Soporte y solución de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

Haga clic en **Solucionar problemas** para ver problemas comunes y estrategias para resolverlos.

Haga clic en **Registros de auditoría** para ver las acciones realizadas en la memoria caché. También puede usar el filtrado para expandir esta vista e incluir otros recursos. Para obtener más información sobre cómo trabajar con registros de auditoría, consulte [Visualización de eventos y registros de auditoría](../azure-portal/insights-debugging-with-events.md) y [Operaciones de auditoría con Resource Manager](../resource-group-audit.md). Para obtener más información sobre la supervisión de eventos de Caché en Redis de Azure, consulte [Operaciones y alertas](cache-how-to-monitor.md#operations-and-alerts).

El **estado de los recursos** supervisa el recurso e indica si se ejecuta del modo previsto. Para obtener más información sobre el servicio Estado de los recursos de Azure, consulte [Información general sobre Estado de los recursos de Azure](../resource-health/resource-health-overview.md).

Haga clic en **Nueva solicitud de soporte** para abrir una solicitud de soporte técnico para su memoria caché.

## Configuración general

La configuración de la sección **General** permite obtener acceso y configurar la siguiente configuración de la memoria caché.

![Configuración general](./media/cache-configure/redis-cache-general-settings.png)

-	[Propiedades](#properties)
-	[Claves de acceso](#access-keys)
-	[Puertos de acceso](#access-ports)
-	[Directiva Maxmemory](#maxmemory-policy-and-maxmemory-reserved)
-	[Configuración avanzada (notificaciones de Keyspace)](#keyspace-notifications-advanced-settings)
-	[Asesor de caché en Redis](#redis-cache-advisor)

### Propiedades

Haga clic en **Propiedades** para ver información sobre la memoria caché, incluidos los puertos y el extremo de la caché.

![Caché en Redis - Propiedades](./media/cache-configure/redis-cache-properties.png)

### Claves de acceso

Haga clic en **Claves de acceso** para ver o volver a generar las claves de acceso de la memoria caché. Estas claves, junto con el nombre de host y los puertos que aparecen en la hoja **Propiedades**, las usan los clientes que se conectan a la memoria caché.

![Caché en Redis - Claves de acceso](./media/cache-configure/redis-cache-manage-keys.png)

### Puertos de acceso

El acceso no SSL está deshabilitado de forma predeterminada para las nuevas cachés. Para habilitar el puerto no SSL, haga clic en la hoja **Acceder a los puertos** y luego en **No**.

![Caché en Redis - Puertos de acceso](./media/cache-configure/redis-cache-access-ports.png)

### Maxmemory-policy y maxmemory-reserved

Haga clic en **Directiva Maxmemory** para configurar las directivas de memoria para la memoria caché. La opción **maxmemory-policy** configura la directiva de expulsión para la memoria caché y **maxmemory-reserved** configura la memoria reservada para los procesos ajenos a la memoria caché.

![Caché en Redis - Directiva Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

La **directiva Maxmemory** permite elegir entre las siguientes directivas de expulsión.

-	volatile-lru: se trata de la directiva predeterminada.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Para obtener más información sobre las directivas maxmemory, consulte las [directivas de expulsión](http://redis.io/topics/lru-cache#eviction-policies).

La opción **maxmemory-reserved** configura la cantidad de memoria en MB que se reserva para las operaciones ajenas a la memoria caché como, por ejemplo, la replicación durante la conmutación por error. También puede usarse cuando se tiene una alta relación de fragmentación. Esta opción le permite tener una experiencia más coherente de servidor Redis cuando varía la carga. Este valor debe establecerse más alto para cargas de trabajo con muchas operaciones de escritura. Cuando la memoria se reserva para dichas operaciones, no está disponible para el almacenamiento de datos en caché.

>[AZURE.IMPORTANT] La opción **maxmemory-reserved** solo está disponible para las memorias caché de nivel Standard y Premium.

### Notificaciones de espacio de claves (configuración avanzada)

Haga clic en **Configuración avanzada** para configurar las notificaciones de espacio de claves de Redis. Las notificaciones de espacio de claves permiten que los clientes reciban notificaciones cuando se producen determinados eventos.

![Caché en Redis - Configuración avanzada](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Las notificaciones de espacio de claves y el valor **notify-keyspace-event** solo están disponibles para las memorias caché de nivel Standard y Premium.

Para obtener más información, vea [Notificaciones de espacio de claves de Redis](http://redis.io/topics/notifications). Para obtener el código de ejemplo, vea el archivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) en el ejemplo [Hola a todos](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="recommendations"></a>
## Asesor de caché en Redis

La hoja **Recomendaciones** muestra recomendaciones para la memoria caché. Durante las operaciones normales, no se muestra ninguna recomendación.

![Recomendaciones](./media/cache-configure/redis-cache-no-recommendations.png)

Si se produce cualquier condición durante las operaciones de la memoria caché, como un uso elevado de la memoria, el ancho de banda de red o la carga del servidor, se muestra una alerta en la hoja **Caché en Redis**.

![Recomendaciones](./media/cache-configure/redis-cache-recommendations-alert.png)

Puede encontrar más información en la hoja **Recomendaciones**.

![Recomendaciones](./media/cache-configure/redis-cache-recommendations.png)

Puede supervisar estas métricas en las secciones de las secciones [Gráficos de supervisión](cache-how-to-monitor.md#monitoring-charts) y [Gráficos de uso](cache-how-to-monitor.md#usage-charts) de la hoja **Caché en Redis**.

Cada plan de tarifa tiene distintos límites para las conexiones de cliente, memoria y ancho de banda. Si la caché se aproxima a la capacidad máxima para estas métricas durante un período prolongado, se crea una recomendación. Para obtener más información sobre las métricas y los límites revisados mediante la herramienta **Recomendaciones**, consulte la tabla siguiente.

| Métrica de Caché en Redis | Para obtener más información, vea: |
|-------------------------|---------------------------------------------------------------------------|
| Uso de ancho de banda de red | [Rendimiento de la caché: ancho de banda disponible](cache-faq.md#cache-performance) |
| Clientes conectados | [Configuración predeterminada del servidor Redis: maxclients](#maxclients) |
| Carga de servidor | [Gráficos de uso: carga del servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de la memoria | [Rendimiento y tamaño de la memoria caché](cache-faq.md#cache-performance) |

Para actualizar la memoria caché, haga clic en **Actualizar ahora** para cambiar el [plan de tarifa](#pricing-tier) y escalar la memoria caché. Para obtener más información sobre cómo determinar un plan de tarifa, consulte [¿Qué oferta y tamaño de Caché en Redis debo utilizar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## Configuración de escala

La configuración de la sección **Escala** permite obtener acceso y establecer la siguiente configuración de la memoria caché.

![Red](./media/cache-configure/redis-cache-scale.png)

-	[Nivel de precios](#pricing-tier)
-	[Tamaño del Clúster en Redis](#cluster-size)

### Nivel de precios

Haga clic en el **Nivel de precios** para ver o cambiar el plan de tarifa para su memoria caché. Para obtener más información sobre el escalado, vea [Escalado de Caché en Redis de Azure](cache-how-to-scale.md).

![Nivel de precios de Caché en Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### Tamaño del Clúster en Redis

Haga clic en **Tamaño del Clúster en Redis (VERSIÓN PRELIMINAR)** para cambiar el tamaño del clúster para una caché premium en ejecución con la agrupación de clústeres habilitada.

>[AZURE.NOTE] Tenga en cuenta que, a pesar de que el nivel Premium de Caché en Redis de Azure se publicó con disponibilidad general, la característica Tamaño del clúster en Redis está actualmente en la versión preliminar.

![Tamaño del Clúster en Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para cambiar el tamaño del clúster, use el control deslizante o escriba un número entre 1 y 10 en el cuadro de texto **Número de particiones** y haga clic en **Aceptar** para guardar.

>[AZURE.IMPORTANT] La agrupación en clústeres de Redis solo está disponible para las memorias cachés premium. Para más información, vea [Cómo configurar la agrupación en clústeres de Redis para una Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).


## Configuración de administración de datos

La configuración de la sección **Administración de datos** permite obtener acceso a la siguiente configuración de la memoria caché y establecer sus valores.

![Administración de datos](./media/cache-configure/redis-cache-data-management.png)

-	[Persistencia de datos de Redis](#redis-data-persistence)
-	[Importación/Exportación](#importexport)

### Persistencia de datos de Redis

Haga clic en **Persistencia de los datos en Redis** para habilitar, deshabilitar o configurar la persistencia de los datos para la caché premium.

![Persistencia de datos de Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar la persistencia en Redis, haga clic en **Habilitada** para habilitar la copia de seguridad RDB (base de datos de Redis). Para deshabilitar la persistencia en Redis, haga clic en **Deshabilitada**.

Para configurar el intervalo de copia de seguridad, seleccione una **Frecuencia de copia de seguridad** en la lista desplegable. Entre las opciones se incluyen **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** y **24 horas**. Este intervalo empieza la cuenta atrás cuando se completa correctamente la operación de copia de seguridad anterior y se inicia cuando se produce una nueva copia de seguridad.

Haga clic en **Cuenta de almacenamiento** para seleccionar la cuenta de almacenamiento que se va a usar y elija la **Clave principal** o la **Clave secundaria** que se usará en la lista desplegable **Clave de almacenamiento**. Debe elegir una cuenta de almacenamiento en la misma región que la memoria caché y se recomienda una cuenta de **Almacenamiento Premium** porque el almacenamiento premium tiene un mayor rendimiento. En cualquier momento que se vuelva a generar la clave de almacenamiento para su persistencia, debe volver a elegir la clave que quiera en la lista desplegable **Clave de almacenamiento**.

Haga clic en **Aceptar** para guardar la configuración de persistencia.

>[AZURE.IMPORTANT] La persistencia de los datos en Redis solo está disponible para las memorias cachés premium. Para más información, vea [Cómo configurar la persistencia para una Caché en Redis de Azure Premium](cache-how-to-premium-persistence.md).

### Importación/Exportación

Importación/Exportación es una operación de administración de datos de Caché en Redis de Azure que permite importar datos en Caché en Redis de Azure o exportar datos desde Caché en Redis de Azure mediante la importación y exportación de una instantánea de base de datos de Caché en Redis (RDB) desde una caché premium a un blob en páginas en una cuenta de Almacenamiento de Azure. Este método le permite migrar entre diferentes instancias de Caché en Redis de Azure o rellenar la caché de datos antes de su uso.

La importación se puede usar para traer los archivos RDB compatibles de Redis desde cualquier servidor de Redis que se ejecute en cualquier nube o entorno, incluidas las instancias de Redis que se ejecutan en Linux, Windows o cualquier proveedor en la nube como, por ejemplo, Amazon Web Services. La importación de datos supone una manera fácil de crear una caché con datos rellenados previamente. Durante el proceso de importación, Caché en Redis de Azure carga los archivos RDB desde Almacenamiento de Azure en la memoria y, a continuación, inserta las claves en la memoria caché.

La exportación permite exportar los datos almacenados en la Caché en Redis de Azure a archivos RDB compatibles. Puede utilizar esta característica para mover datos desde una instancia de Caché en Redis de Azure a otra o a otro servidor de Redis. Durante el proceso de exportación, se crea un archivo temporal en la máquina virtual que hospeda la instancia del servidor de Caché en Redis de Azure y el archivo se carga en la cuenta de almacenamiento designada. Una vez completada la operación de exportación (de manera correcta o incorrecta), se elimina el archivo temporal.

>[AZURE.IMPORTANT] Importación/Exportación solo está disponible para las memorias caché de nivel premium. Para obtener más información e instrucciones, consulte [Importación y exportación de datos en la Caché en Redis de Azure](cache-how-to-import-export-data.md).


## Configuración de administración

La configuración de la sección **Administración** le permite realizar las siguientes tareas administrativas de la caché premium.

![Administración](./media/cache-configure/redis-cache-administration.png)

-	[Reboot](#reboot)
-	[Programar actualizaciones](#schedule-updates)

>[AZURE.IMPORTANT] La configuración de esta sección solo está disponible para las cachés de nivel Premium.

### Reboot

La hoja **Reiniciar** permite reiniciar uno o varios nodos de la memoria caché. De este modo, podrá probar la resiliencia de la aplicación en caso de error.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Si tiene una caché premium con la agrupación en clústeres habilitada, puede seleccionar qué particiones de la memoria caché se reiniciarán.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar uno o varios nodos de la memoria caché, seleccione los nodos deseados y haga clic en **Reiniciar**. Si tiene una caché premium con la agrupación en clústeres habilitada, seleccione las particiones para reiniciar y, a continuación, haga clic en **Reiniciar**. Después de unos minutos, los nodos seleccionados se reinician y vuelven a estar en línea poco tiempo después.

>[AZURE.IMPORTANT] El reinicio solo está disponible para las memorias caché de nivel premium. Para obtener más información e instrucciones, consulte [Azure Redis Cache administration - Reboot](cache-administration.md#reboot) (Administración de Caché en Redis de Azure - Reinicio).

### Programar actualizaciones

La hoja **Programar actualizaciones** le permite designar una ventana de mantenimiento para las actualizaciones del servidor de Redis de la caché.

>[AZURE.IMPORTANT] Tenga en cuenta que la ventana de mantenimiento solo se aplica a las actualizaciones del servidor de Redis y no a cualquier actualización o actualizaciones de Azure para el sistema operativo de las máquinas virtuales que hospedan la memoria caché.

![Programar actualizaciones](./media/cache-configure/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, compruebe los días deseados y especifique la hora de inicio de la ventana de mantenimiento para cada día; a continuación, haga clic en **Aceptar**. Tenga en cuenta que la hora de la ventana de mantenimiento está en formato UTC.

>[AZURE.IMPORTANT] Las actualizaciones de programación solo están disponibles para las memorias caché de nivel premium. Para obtener más información e instrucciones, consulte [Azure Redis Cache administration - Schedule updates](cache-administration.md#schedule-updates) (Administración de Caché en Redis de Azure - Programar actualizaciones).

## Configuración de diagnóstico

La sección **Diagnóstico** permite configurar los diagnósticos de Caché en Redis.

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

Haga clic en **Diagnóstico** para [configurar la cuenta de almacenamiento](cache-how-to-monitor.md#enable-cache-diagnostics) que se usa para almacenar diagnósticos de caché.

![Caché en Redis - Diagnóstico](./media/cache-configure/redis-cache-diagnostics-settings.png)

Haga clic en **Métricas de Redis** para [ver las métricas](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) de la memoria caché, y en **Reglas de alerta** para [configurar las reglas de alerta](cache-how-to-monitor.md#operations-and-alerts).

Para obtener más información sobre los diagnósticos de Caché en Redis de Azure, consulte [Supervisión de Caché en Redis de Azure](cache-how-to-monitor.md).


## Configuración de red

La configuración de la sección **Red** permite obtener acceso a la siguiente configuración de la memoria caché y establecer sus valores.

![Red](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] La configuración de red virtual solo está disponible para las memorias cachés premium que se configuraron con la compatibilidad de la red virtual durante la creación de la memoria caché. Para obtener información sobre cómo crear una caché premium con compatibilidad de la red virtual y actualizar su configuración, consulte [Cómo configurar la compatibilidad de red virtual para una Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).

## Configuración de administración de recursos

![Administración de recursos](./media/cache-configure/redis-cache-resource-management.png)

La sección **Etiquetas** le ayuda a organizar sus recursos. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

La sección **Bloqueos** permite bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen o modifiquen por error recursos esenciales. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](../resource-group-lock-resources.md).

La sección **Usuarios** del Portal de Azure ofrece compatibilidad con el control de acceso basado en roles (RBAC) con el fin de que las organizaciones satisfagan sus requisitos de administración de acceso de forma simple y precisa. Para obtener más información, consulte [Control de acceso basado en roles en el Portal de Azure](../active-directory/role-based-access-control-configure.md).

Haga clic en **Exportar plantilla** para generar y exportar una plantilla de recursos implementados para futuras implementaciones. Para obtener más información sobre cómo trabajar con plantillas, consulte [Implementación de recursos con las plantillas de Azure Resource Manager](../resource-group-template-deploy.md).

## Configuración predeterminada del servidor Redis

Las nuevas instancias de Caché en Redis de Azure se configuran con los siguientes valores de configuración de Redis predeterminados.

>[AZURE.NOTE] No se puede cambiar la configuración de esta sección con el método `StackExchange.Redis.IServer.ConfigSet`. Si se llama a este método con uno de los comandos de esta sección, se produce una excepción similar a la siguiente:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Es posible configurar aquellos valores que permitan esta opción, como **max-memory-policy**, a través del Portal de Azure o las herramientas de administración de la línea de comandos como la CLI de Azure o PowerShell.

|Configuración|Valor predeterminado|Descripción|
|---|---|---|
|bases de datos|16|El número de bases de datos predeterminado es 16, pero puede configurar otro número en función del plan de tarifa.<sup>1</sup> La base de datos predeterminada es DB 0. Se puede seleccionar otra diferente por conexión mediante `connection.GetDatabase(dbid)`, donde dbid es un número entre `0` y `databases - 1`.|
|maxclients|Depende del plan de tarifa<sup>2</sup>|Se trata del número máximo de clientes conectados que se permiten al mismo tiempo. Una vez alcanzado el límite, Redis cerrará todas las nuevas conexiones y enviará un error de "número máximo de clientes alcanzado".|
|maxmemory-policy|volatile-lru|Directiva Maxmemory es la opción que configura el modo en que Redis seleccionará lo que se debe quitar cuando se alcanza el valor de maxmemory (el tamaño de la oferta de memoria caché que seleccionó al crear la memoria caché). Con Caché en Redis de Azure la opción predeterminada es volatile-lru, que quita las claves con una fecha de expiración definida mediante un algoritmo LRU. Esta opción puede configurarse en el Portal de Azure. Para más información, vea [Maxmemory-policy y maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Los algoritmos LRU y TTL mínimo no son precisos sino aproximados (con el fin de ahorrar memoria), para que también pueda seleccionar el tamaño de muestra para comprobar. Por ejemplo, Redis comprobará de manera predeterminada tres claves y seleccionará la usada menos recientemente.|
|lua-time-limit|5\.000|Tiempo máximo de ejecución de un script Lua en milisegundos. Si se alcanza el tiempo máximo de ejecución, Redis registrará que un script está aún en ejecución una vez transcurrido el tiempo máximo permitido y empezará a responder a las consultas con un error.|
|lua-event-limit|500|Se trata del tamaño máximo de la cola de eventos de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Los límites de búfer de salida de cliente pueden usarse para forzar la desconexión de clientes que, por algún motivo (un motivo habitual es que un cliente de Pub/Sub no puede consumir mensajes tan rápidamente como el publicador los crea), no leen datos del servidor con suficiente rapidez. Para más información, vea [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a> <sup>1</sup>El límite de `databases` es diferente para cada plan de tarifa de Caché en Redis de Azure, y se puede establecer al crear la memoria caché. Si no se especifica la configuración de `databases` al crear la memoria caché, el valor predeterminado es 16.

-	Cachés Basic y Standard
	-	Memoria caché C0 (250 MB): hasta 16 bases de datos
	-	Memoria caché C1 (1 GB): hasta 16 bases de datos
	-	Memoria caché C2 (2,5 GB): hasta 16 bases de datos
	-	Memoria caché C3 (6 GB): hasta 16 bases de datos
	-	Memoria caché C4 (13 GB): hasta 32 bases de datos
	-	Memoria caché C5 (26 GB): hasta 48 bases de datos
	-	Memoria caché C6 (53 GB): hasta 64 bases de datos
-	Cachés Premium
	-	P1 (6 GB - 60 GB): hasta 16 bases de datos
	-	P2 (13 GB - 130 GB): hasta 32 bases de datos
	-	P3 (26 GB - 260 GB): hasta 48 bases de datos
	-	P4 (53 GB - 530 GB): hasta 64 bases de datos
	-   Todas las cachés premium con clúster de Redis habilitado: el clúster de Redis solo admite el uso de la base de datos 0 con el fin de que el límite `databases` para cualquier caché premium con clúster de Redis habilitado sea 1; el comando [Select](http://redis.io/commands/select) no se admite. Para obtener más información, consulte [¿Es necesario realizar algún cambio en mi aplicación cliente para usar la agrupación en clústeres?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] La configuración `databases` solo puede establecerse al crear la memoria caché y solo mediante PowerShell, la CLI u otros clientes de administración. Para ver un ejemplo de configuración de `databases` al crear la memoria caché con PowerShell, consulte [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a> <sup>2</sup>`maxclients` es diferente para cada plan de tarifa de Caché en Redis de Azure.

-	Cachés Basic y Standard
	-	Memoria caché C0 (250 MB): hasta 256 conexiones
	-	Memoria caché C1 (1 GB): hasta 1000 conexiones
	-	Memoria caché C2 (2,5 GB): hasta 2000 conexiones
	-	Memoria caché C3 (6 GB): hasta 5000 conexiones
	-	Memoria caché C4 (13 GB): hasta 10 000 conexiones
	-	Memoria caché C5 (26 GB): hasta 15 000 conexiones
	-	Memoria caché C6 (53 GB): hasta 20 000 conexiones
-	Cachés Premium
	-	P1 (6 GB - 60 GB): hasta 7.500 conexiones
	-	P2 (13 GB - 130 GB): hasta 15.000 conexiones
	-	P3 (26 GB - 260 GB): hasta 30.000 conexiones
	-	P4 (53 GB - 530 GB): hasta 40.000 conexiones

## No se admiten comandos de Redis en Caché en Redis de Azure

>[AZURE.IMPORTANT] Dado que la configuración y la administración de instancias de Caché en Redis de Azure se administra por Microsoft, se deshabilitan los siguientes comandos. Si intenta invocarlos, recibirá un mensaje de error similar a `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	GUARDAR
>-	SHUTDOWN
>-	SLAVEOF
>-	CLÚSTER: los comandos de escritura del clúster están deshabilitados, pero se permiten los comandos de solo lectura del clúster.

Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands).

## Consola de Redis

Puede emitir comandos de forma segura para sus instancias de Caché en Redis de Azure con la **consola de Redis**, que está disponible para las memorias caché Standard y Premium.

>[AZURE.IMPORTANT] La Consola de Redis no funciona con red virtual o agrupación en clústeres.
>
>-	[Red virtual](cache-how-to-premium-vnet.md): cuando la memoria caché forma parte de una red virtual, solo los clientes de la red virtual pueden tener acceso a la memoria caché. Dado que la Consola de Redis usa al cliente de redis-cli.exe hospedado en máquinas virtuales que no forman parte de su red virtual, no se puede conectar a su memoria caché.
>-	[Agrupación en clústeres](cache-how-to-premium-clustering.md): la Consola de Redis usa el cliente de redis-cli.exe que no es compatible con la agrupación en clústeres en este momento. La utilidad redis-cli de la rama [inestable](http://redis.io/download) del repositorio de Redis en GitHub implementa compatibilidad básica cuando se inicia con el conmutador `-c`. Para más información, vea [Jugar con el clúster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) en [http://redis.io](http://redis.io) en el [Tutorial del clúster de Redis](http://redis.io/topics/cluster-tutorial).

Para el acceso a la Consola de Redis, haga clic en **Consola** desde la hoja **Caché en Redis**.

![Consola de Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos con una instancia de la memoria caché, basta con escribir en el comando que desea en la consola.

![Consola de Redis](./media/cache-configure/redis-console.png)

Para obtener una lista de los comandos de Redis deshabilitados para Caché en Redis de Azure, vea la sección anterior [No se admiten comandos de Redis en Caché en Redis de Azure](#redis-commands-not-supported-in-azure-redis-cache). Para más información sobre los comandos de Redis, vea [http://redis.io/commands](http://redis.io/commands).

## Traslado de la memoria caché a una nueva suscripción

Puede trasladar la memoria caché a una nueva suscripción haciendo clic en **Mover**.

![Traslado de Caché en Redis](./media/cache-configure/redis-cache-move.png)

Para obtener información sobre cómo trasladar recursos de un grupo de recursos a otro y de una suscripción a otra, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).

## Pasos siguientes
-	Para más información sobre cómo trabajar con los comandos de Redis, vea [¿Cómo puedo ejecutar comandos de Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=AcomDC_0629_2016-->