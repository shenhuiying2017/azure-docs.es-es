<properties 
	pageTitle="Cómo administrar la Caché en Redis de Azure | Microsoft Azure"
	description="Aprenda a realizar tareas de administración como el reinicio y la programación de actualizaciones para la Caché en Redis de Azure"
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
	ms.date="07/13/2016"
	ms.author="sdanie" />

# Administración de la Caché en Redis de Azure

En este tema se describe cómo realizar tareas de administración como el reinicio y la programación de actualizaciones para las instancias de Caché en Redis de Azure.

>[AZURE.IMPORTANT] La configuración y las características que se describen en este artículo solo están disponibles para las cachés de nivel Premium.


## Configuración de administración

La configuración de **Administration** (Admnistración) de Caché en Redis de Azure le permite realizar las siguientes tareas administrativas de la caché premium. Para acceder a la configuración de administración, haga clic en **Settings** (Configuración) o en **All settings** (Toda la configuración) en la hoja de Caché en Redis y desplácese hasta la sección **Administration** (Administración) de la hoja **Settings** (Configuración).

![Administración](./media/cache-administration/redis-cache-administration.png)

-	[Reboot](#reboot)
-	[Programación de actualizaciones](#schedule-updates)

## Reboot

La hoja **Reboot** (Reiniciar) permite reiniciar uno o varios nodos de la caché. De este modo, podrá probar la resistencia de la aplicación en caso de error.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Si tiene una caché premium con la agrupación en clústeres habilitada, puede seleccionar qué particiones de la memoria caché se reiniciarán.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar uno o varios nodos de la caché, seleccione los nodos que prefiera y haga clic en **Reboot** (Reiniciar). Si tiene una caché premium con la agrupación en clústeres habilitada, seleccione las particiones para reiniciar y haga clic en **Reboot** (Reiniciar). Al cabo de unos minutos, los nodos seleccionados se reinician y se vuelven en línea poco tiempo después.

El impacto en las aplicaciones cliente varía en función de los nodos que se reinicien.

-	**Master** (Maestro): cuando el nodo maestro se reinicia, en Caché en Redis de Azure se produce una conmutación por error en el nodo de la réplica y lo promueve al maestro. Durante esta conmutación por error puede haber un breve intervalo en el que se puede producir un error de conexión a la memoria caché.
-	**Slave** (Subordinado): el reinicio de un nodo subordinado no suele afectar a las cachés cliente.
-	**Both master and slave**(Maestro y subordinado): cuando se reinician ambos nodos de la memoria caché se pierden todos los datos de la caché y las conexiones a la caché producen un error hasta que el nodo principal vuelve a estar en línea. Si ha configurado la [persistencia de datos](cache-how-to-premium-persistence.md), se restaurará la copia de seguridad más reciente cuando la caché vuelva a estar en línea. Tenga en cuenta que se perderán las escrituras de caché que se produjeron después de la copia de seguridad más reciente.
-	**Node(s) of a premium cache with clustering enabled** (Nodos de una caché premium con agrupamiento en clústeres habilitado): cuando reinicie los nodos de una caché premium con el agrupamiento en clústeres habilitado, el comportamiento es el mismo que al reiniciar los nodos de una caché sin agrupar.


>[AZURE.IMPORTANT] El reinicio solo está disponible para las memorias caché de nivel premium.

## Preguntas más frecuentes sobre el reinicio

-	[¿Qué nodo que debo reiniciar para probar mi aplicación?](#which-node-should-i-reboot-to-test-my-application)
-	[¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
-	[¿Se pierden los datos de mi memoria caché si reinicio?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-	[¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-	[¿Con qué planes de tarifa se puede usar la funcionalidad de reinicio?](#what-pricing-tiers-can-use-the-reboot-functionality)


### ¿Qué nodo que debo reiniciar para probar mi aplicación?

Para probar la resistencia de la aplicación frente a errores del nodo principal de la memoria caché, reinicie el nodo **Master** (Maestro). Para probar la resistencia de la aplicación frente a errores del nodo secundario, reinicie el nodo **Slave** (Subordinado). Para probar la resistencia de la aplicación frente a errores de toda la caché, reinicie ambos nodos, **Both** (Ambos).

### ¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?

Sí, si reinicia la memoria caché se borran todas las conexiones de cliente. Esto puede resultar útil en caso de que se usen todas las conexiones de cliente, por ejemplo, por un error lógico o un error en la aplicación cliente. Cada plan de tarifa tiene diferentes [límites de conexión de clientes](cache-configure.md#default-redis-server-configuration) para los distintos tamaños y una vez alcanzados, no se aceptan más conexiones de cliente. Reiniciar la memoria caché es una forma de borrar todas las conexiones de cliente.

>[AZURE.IMPORTANT] Si se agotan las conexiones de clientes debido a un error lógico o a un error en el código de cliente, tenga en cuenta que StackExchange.Redis volverá a conectarse automáticamente cuando el nodo de Redis vuelve a conectarse. Si no se resuelve el problema subyacente, las conexiones de cliente continuarán agotándose.

### ¿Se pierden los datos de mi memoria caché si reinicio?

Si reinicia, tanto los nodos **Master** (Maestro) y **Slave** (Subordinado) se pierden todos los datos de la memoria caché (o en esa partición si utiliza una caché premium con el agrupamiento en clústeres habilitado). Si ha configurado la [persistencia de datos](cache-how-to-premium-persistence.md), se restaurará la copia de seguridad más reciente cuando la caché vuelva a estar en línea. Tenga en cuenta que se perderán las escrituras de caché que se han producido después de realizar la copia de seguridad.

Si reinicia solo uno de los nodos, no se suelen perder datos, pero podría pasar. Por ejemplo, si se reinicia el nodo maestro y se está escribiendo en la caché, los datos escritos se pierden. Otro escenario de pérdida de datos se produce al reiniciar un nodo y el otro nodo se dejar de funcionar debido a un error al mismo tiempo. Para más información acerca de posibles causas de pérdida de datos, consulte [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (¿Qué ha ocurrido con mis datos en Redis?).

### ¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?

Sí. Para más información sobre las instrucciones de PowerShell consulte la sección [Reinicio de una caché en Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### ¿Con qué planes de tarifa se puede usar la funcionalidad de reinicio?

El reinicio solo está disponible en el plan de tarifa Premium.

## Programación de actualizaciones

La hoja **Programar actualizaciones** permite designar una ventana de mantenimiento para la memoria caché. Cuando se especifica un período de mantenimiento, las actualizaciones del servidor Redis se realizan en ese intervalo de tiempo. Tenga en cuenta que el período de mantenimiento solo se aplica a las actualizaciones del servidor de Redis y no a las actualizaciones de Azure para el sistema operativo de las máquinas virtuales que hospedan la memoria caché.

![Programación de actualizaciones](./media/cache-administration/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, compruebe los días deseados y especifique la hora de inicio de la ventana de mantenimiento para cada día; a continuación, haga clic en **Aceptar**. Tenga en cuenta que la hora del período de mantenimiento está en formato UTC.

## Preguntas más frecuentes sobre la programación de actualizaciones

-	[¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-	[¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-	[¿Qué planes de tarifa pueden usar la funcionalidad de programación de actualizaciones?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### ¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?

Si no especifica un período de mantenimiento, las actualizaciones pueden realizarse en cualquier momento.

### ¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?

Las actualizaciones del servidor Redis solo se realizan durante el período programado de mantenimiento. El período de mantenimiento no se aplica a las actualizaciones de Azure ni a las actualizaciones del sistema operativo de la máquina virtual.

### ¿Qué planes de tarifa pueden usar la funcionalidad de programación de actualizaciones?

La programación de actualizaciones solo está disponible en el plan de tarifa Premium.

## Pasos siguientes

-	Explore más características del [nivel premium de Caché en Redis de Azure](cache-premium-tier-intro.md).

<!---HONumber=AcomDC_0713_2016-->