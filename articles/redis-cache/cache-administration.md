---
title: "Administración de Azure Redis Cache | Microsoft Docs"
description: "Aprenda a realizar tareas de administración como el reinicio y la programación de actualizaciones para la Caché en Redis de Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Administración de la Caché en Redis de Azure
En este tema se describe cómo realizar tareas de administración como el [reinicio](#reboot) y la [programación de actualizaciones](#schedule-updates) para las instancias de Azure Redis Cache.

## <a name="reboot"></a>Reboot
La hoja **Reiniciar** permite reiniciar uno o varios nodos de la memoria caché. Esta funcionalidad de reinicio le permite probar la resistencia de la aplicación en caso de que haya un error de un nodo de la caché.

![Reboot](./media/cache-administration/redis-cache-administration-reboot.png)

Seleccione los nodos que se van a reiniciar y haga clic en **Reiniciar**.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Si tiene una caché premium con la agrupación en clústeres habilitada, puede seleccionar qué particiones de la memoria caché se reiniciarán.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar uno o varios nodos de la caché, seleccione los nodos que prefiera y haga clic en **Reboot**(Reiniciar). Si tiene una caché premium con la agrupación en clústeres habilitada, seleccione las particiones para reiniciar y haga clic en **Reboot** (Reiniciar). Al cabo de unos minutos, los nodos seleccionados se reiniciarán y volverán a estar en línea poco tiempo después.

El impacto en las aplicaciones cliente varía en función de los nodos que se reinicien.

* **Master** (Maestro): cuando el nodo maestro se reinicia, en Caché en Redis de Azure se produce una conmutación por error en el nodo de la réplica y lo promueve al maestro. Durante esta conmutación por error puede haber un breve intervalo en el que se puede producir un error de conexión a la memoria caché.
* **Slave** (Subordinado): el reinicio de un nodo subordinado no suele afectar a las cachés cliente.
* **Both master and slave** (Maestro y subordinado): cuando se reinician ambos nodos de la memoria caché se pierden todos los datos de la caché y las conexiones a la caché producen un error hasta que el nodo principal vuelve a estar en línea. Si ha configurado la [persistencia de los datos](cache-how-to-premium-persistence.md), se restaurará la copia de seguridad más reciente cuando la caché vuelva a estar en línea, pero se perderán las escrituras de caché producidas después de la copia de seguridad más reciente.
* **Nodes of a premium cache with clustering enabled** (Nodos de una caché premium con agrupamiento en clústeres habilitado): cuando reinicie uno o varios nodos de una caché premium con el agrupamiento en clústeres habilitado, el comportamiento de los nodos seleccionados es el mismo que al reiniciar el nodo o nodos correspondientes de una caché sin agrupar.

> [!IMPORTANT]
> El reinicio ahora está disponible para todos los planes de tarifas.
> 
> 

## <a name="reboot-faq"></a>Preguntas más frecuentes sobre el reinicio
* [¿Qué nodo que debo reiniciar para probar mi aplicación?](#which-node-should-i-reboot-to-test-my-application)
* [¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
* [¿Se pierden los datos de mi memoria caché si reinicio?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [¿Con qué planes de tarifa se puede usar la funcionalidad de reinicio?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>¿Qué nodo que debo reiniciar para probar mi aplicación?
Para probar la resistencia de la aplicación frente a errores del nodo principal de la memoria caché, reinicie el nodo **Master** (Maestro). Para probar la resistencia de la aplicación frente a errores del nodo secundario, reinicie el nodo **Slave** (Subordinado). Para probar la resistencia de la aplicación frente a errores de toda la caché, reinicie ambos nodos, **Both** (Ambos).

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>¿Puedo reiniciar la memoria caché para borrar las conexiones de cliente?
Sí, si reinicia la memoria caché se borran todas las conexiones de cliente. El reinicio puede resultar útil en caso de que estén agotadas todas las conexiones de cliente debido a un error lógico o a un error en la aplicación cliente. Cada plan de tarifa tiene diferentes [límites de conexión de clientes](cache-configure.md#default-redis-server-configuration) para los distintos tamaños y una vez alcanzados, no se aceptan más conexiones de cliente. Reiniciar la memoria caché es una forma de borrar todas las conexiones de cliente.

> [!IMPORTANT]
> Si reinicia la caché para borrar las conexiones de cliente, StackExchange.Redis vuelve a conectarse automáticamente una vez que el nodo de Redis esté de nuevo en línea. Si no se resuelve el problema subyacente, las conexiones de cliente pueden continuar agotándose.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>¿Se pierden los datos de mi memoria caché si reinicio?
Si reinicia los nodos **Master** (Maestro) y **Slave** (Servidor subordinado), se pierden todos los datos de la caché (o de esa partición si utiliza una caché premium con el agrupamiento en clústeres habilitado). Si ha configurado la [persistencia de los datos](cache-how-to-premium-persistence.md), se restaurará la copia de seguridad más reciente cuando la caché vuelva a estar en línea, pero se perderán las escrituras de caché producidas después de realizar la copia de seguridad.

Si reinicia solo uno de los nodos, no se suelen perder datos, pero podría pasar. Por ejemplo, si se reinicia el nodo maestro y se está escribiendo en la caché, los datos escritos se pierden. Otro escenario de pérdida de datos se produce al reiniciar un nodo y el otro nodo se dejar de funcionar debido a un error al mismo tiempo. Para obtener más información sobre las posibles causas de pérdida de datos, consulte [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (¿Qué ha ocurrido con mis datos en Redis?).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>¿Puedo reiniciar la caché con PowerShell, CLI u otras herramientas de administración?
Sí. Para más información sobre las instrucciones de PowerShell consulte la sección [Reinicio de una caché en Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>¿Con qué planes de tarifa se puede usar la funcionalidad de reinicio?
El reinicio está disponible para todos los planes de tarifas.

## <a name="schedule-updates"></a>Programar actualizaciones
La hoja **Programar actualizaciones** permite designar una ventana de mantenimiento para la memoria caché de nivel Premium. Cuando se especifica un período de mantenimiento, las actualizaciones del servidor Redis se realizan en ese intervalo de tiempo. 

> [!NOTE] 
> El período de mantenimiento solo se aplica a las actualizaciones del servidor de Redis y no a las actualizaciones de Azure o del sistema operativo de las máquinas virtuales que hospedan la caché.
> 
> 

![Programar actualizaciones](./media/cache-administration/redis-schedule-updates.png)

Para especificar una ventana de mantenimiento, compruebe los días deseados, especifique la hora de inicio de la ventana de mantenimiento para cada día y haga clic en **Aceptar**. Tenga en cuenta que la hora de la ventana de mantenimiento está en formato UTC. 

> [!NOTE]
> La ventana de mantenimiento predeterminada para las actualizaciones es de cinco horas. Este valor no es configurable desde Azure Portal, pero puede configurarlo en PowerShell mediante el parámetro `MaintenanceWindow` del cmdlet [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) . Para obtener más información, consulte [¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>Preguntas más frecuentes sobre la programación de actualizaciones
* [¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [¿Qué planes de tarifa pueden usar la funcionalidad de programación de actualizaciones?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>¿Cuándo se realizan las actualizaciones si no se usa la característica de programación de actualizaciones?
Si no especifica un período de mantenimiento, las actualizaciones pueden realizarse en cualquier momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>¿Qué tipo de actualizaciones se actualizan durante el período programado de mantenimiento?
Las actualizaciones del servidor Redis solo se realizan durante el período programado de mantenimiento. La ventana de mantenimiento no se aplica a las actualizaciones de Azure o a las actualizaciones del sistema operativo de la máquina virtual.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>¿Se pueden administrar las actualizaciones programadas con PowerShell, CLI u otras herramientas de administración?
Sí, puede administrar sus actualizaciones programadas con los siguientes cmdlets de PowerShell:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [New-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>¿Qué planes de tarifa pueden usar la funcionalidad de programación de actualizaciones?
La característica **Programación de actualizaciones** solo está disponible en el plan de tarifa Premium.

## <a name="next-steps"></a>Pasos siguientes
* Explore más características del [nivel premium de Azure Redis Cache](cache-premium-tier-intro.md) .

