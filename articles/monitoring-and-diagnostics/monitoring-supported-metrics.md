---
title: "Métricas de Azure Monitor admitidas por tipo de recurso | Microsoft Docs"
description: "Lista de métricas disponibles para cada tipo de recurso con Azure Monitor."
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 673f5a5cd6832adb031ef72ce25f8a1622717cfd
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatibles con Azure Monitor
Azure Monitor proporciona varias maneras de interactuar con las métricas, como la representación en gráficos en el portal, el acceso a ellas a través de la API de REST o consultarlas con PowerShell o la CLI. A continuación se muestra una lista completa de todas las métricas disponibles actualmente en la canalización de métricas de Azure Monitor.

> [!NOTE]
> Otras métricas pueden estar disponibles en el portal o mediante las API heredadas. Esta lista incluye solo las métricas disponibles con la canalización de métricas consolidada de Azure Monitor. Para consultar y obtener acceso a las métricas con dimensiones, use la [api-version 2017-05-01-preview](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|qpu_metric|QPU|Recuento|Media|QPU. Intervalo de 0-100 para S1, 0-200 para S2 y 0-400 para S4|ServerResourceType|
|memory_metric|Memoria|Bytes|Media|Memoria. Intervalo de 0-25 GB para S1, 0-50 GB para S2 y 0-100 GB para S4|ServerResourceType|
|TotalConnectionRequests|Número total de solicitudes de conexión|Recuento|Media|Número total de solicitudes de conexión. Se trata de llegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexiones correctas por segundo|CountPerSecond|Media|Tasa de finalizaciones de conexión correctas.|ServerResourceType|
|TotalConnectionFailures|Número total de errores de conexión|Recuento|Media|Número total de intentos de conexión con error.|ServerResourceType|
|CurrentUserSessions|Sesiones de usuario actuales|Recuento|Media|Número actual de sesiones de usuario establecidas.|ServerResourceType|
|QueryPoolBusyThreads|Subprocesos ocupados de grupo de consultas|Recuento|Media|Número de subprocesos ocupados del grupo de subprocesos de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Longitud de cola de trabajos de grupo de comandos|Recuento|Media|Número de trabajos en la cola del grupo de subprocesos de comandos.|ServerResourceType|
|ProcessingPoolJobQueueLength|Longitud de cola de trabajos de grupo de procesamiento|Recuento|Media|Número de trabajos que no son de E/S en la cola del grupo de subprocesos de procesamiento.|ServerResourceType|
|CurrentConnections|Conexión: conexiones actuales|Recuento|Media|Número actual de conexiones de cliente establecidas.|ServerResourceType|
|CleanerCurrentPrice|Memoria: precio actual de limpieza|Recuento|Media|Precio actual de memoria, USD/byte/tiempo, normalizado a 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memoria: memoria de limpieza reducible|Bytes|Media|Cantidad de memoria, en bytes, que purgará el limpiador en segundo plano.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memoria: memoria de limpieza no reducible|Bytes|Media|Cantidad de memoria, en bytes, que no purgará el limpiador en segundo plano.|ServerResourceType|
|MemoryUsage|Memoria: uso de memoria|Bytes|Media|Uso de memoria del proceso de servidor tal como se usa para calcular el precio de la memoria del limpiador. Es igual al contador Proceso\Bytes privados más el tamaño de los datos asignados a la memoria; se ignora la memoria que ha asignado VertiPaq superior al límite de memoria de VertiPaq.|ServerResourceType|
|MemoryLimitHard|Memoria: límite de memoria física|Bytes|Media|Límite de memoria física del archivo de configuración.|ServerResourceType|
|MemoryLimitHigh|Memoria: límite alto de memoria|Bytes|Media|Límite alto de memoria del archivo de configuración.|ServerResourceType|
|MemoryLimitLow|Memoria: límite bajo de memoria|Bytes|Media|Límite bajo de memoria del archivo de configuración.|ServerResourceType|
|MemoryLimitVertiPaq|Memoria: VertiPaq de límite de memoria|Bytes|Media|Límite en memoria del archivo de configuración.|ServerResourceType|
|Quota|Memoria: cuota|Bytes|Media|Cuota de memoria actual, en bytes. La cuota de memoria también se denomina concesión de memoria o reserva de memoria.|ServerResourceType|
|QuotaBlocked|Memoria: cuota bloqueada|Recuento|Media|Número actual de solicitudes de cuota que están bloqueadas hasta que se liberen otras cuotas de memoria.|ServerResourceType|
|VertiPaqNonpaged|Memoria: VertiPaq no paginado|Bytes|Media|Bytes de memoria bloqueados en el espacio de trabajo para que los use el motor en memoria.|ServerResourceType|
|VertiPaqPaged|Memoria: VertiPaq paginado|Bytes|Media|Bytes de memoria paginada en uso para datos en memoria.|ServerResourceType|
|RowsReadPerSec|Procesamiento: filas leídas por segundo|CountPerSecond|Media|Velocidad de filas leídas de todas las bases de datos relacionales.|ServerResourceType|
|RowsConvertedPerSec|Procesamiento: filas convertidas por segundo|CountPerSecond|Media|Velocidad de filas convertidas durante el procesamiento.|ServerResourceType|
|RowsWrittenPerSec|Procesamiento: filas escritas por segundo|CountPerSecond|Media|Velocidad de filas escritas durante el procesamiento.|ServerResourceType|
|CommandPoolBusyThreads|Subprocesos: subprocesos ocupados del grupo de comandos|Recuento|Media|Número de subprocesos ocupados del grupo de subprocesos de comandos.|ServerResourceType|
|CommandPoolIdleThreads|Subprocesos: subprocesos inactivos del grupo de comandos|Recuento|Media|Número de subprocesos inactivos del grupo de subprocesos de comandos.|ServerResourceType|
|LongParsingBusyThreads|Subprocesos: subprocesos ocupados en análisis largos|Recuento|Media|Número de subprocesos ocupados del grupo de subprocesos de análisis largos.|ServerResourceType|
|LongParsingIdleThreads|Subprocesos: subprocesos inactivos en análisis largos|Recuento|Media|Número de subprocesos inactivos del grupo de subprocesos en análisis largos.|ServerResourceType|
|LongParsingJobQueueLength|Subprocesos: longitud de cola de trabajos en análisis largos|Recuento|Media|Número de trabajos en la cola del grupo de subprocesos en análisis largos.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Subprocesos: subprocesos de trabajo de E/S ocupados del grupo de procesamiento|Recuento|Media|Número de subprocesos que ejecutan trabajos de E/S en el grupo de subprocesos de procesamiento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Subprocesos: subprocesos de trabajo ocupados que no son de E/S del grupo de procesamiento|Recuento|Media|Número de subprocesos que ejecutan trabajos que no son de E/S del grupo de subprocesos de procesamiento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Subprocesos: longitud de cola de trabajos de E/S del grupo de procesamiento|Recuento|Media|Número de trabajos de E/S en la cola del grupo de subprocesos de procesamiento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Subprocesos: subprocesos de trabajo de E/S inactivos del grupo de procesamiento|Recuento|Media|Número de subprocesos inactivos para trabajos de E/S en el grupo de subprocesos de procesamiento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Subprocesos: subprocesos de trabajo inactivos que no son de E/S del grupo de procesamiento|Recuento|Media|Número de subprocesos inactivos del grupo de subprocesos de procesamiento dedicado a trabajos que no son de E/S.|ServerResourceType|
|QueryPoolIdleThreads|Subprocesos: subprocesos inactivos del grupo de consultas|Recuento|Media|Número de subprocesos inactivos para trabajos de E/S en el grupo de subprocesos de procesamiento.|ServerResourceType|
|QueryPoolJobQueueLength|Subprocesos: longitud de cola de trabajos del grupo de consultas|Recuento|Media|Número de trabajos en la cola del grupo de subprocesos de consultas.|ServerResourceType|
|ShortParsingBusyThreads|Subprocesos: subprocesos ocupados en análisis cortos|Recuento|Media|Número de subprocesos ocupados del grupo de subprocesos de análisis cortos.|ServerResourceType|
|ShortParsingIdleThreads|Subprocesos: subprocesos inactivos en análisis cortos|Recuento|Media|Número de subprocesos inactivos del grupo de subprocesos de análisis cortos.|ServerResourceType|
|ShortParsingJobQueueLength|Subprocesos: longitud de cola de trabajos en análisis cortos|Recuento|Media|Número de trabajos en la cola del grupo de subprocesos en análisis cortos.|ServerResourceType|
|memory_thrashing_metric|Paginación excesiva de memoria|Percent|Media|Paginación excesiva media de memoria.|ServerResourceType|
|mashup_engine_qpu_metric|QPU de motor M|Recuento|Media|Uso de QPU por los procesos del motor de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memoria del motor M|Bytes|Media|Uso de memoria por los procesos del motor de mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TotalRequests|Solicitudes de puerta de enlace en total|Recuento|Total|Número de solicitudes de puerta de enlace|Ubicación, Nombre de host|
|SuccessfulRequests|Solicitudes de puerta de enlace correctas|Recuento|Total|Número de solicitudes de puerta de enlace correctas|Ubicación, Nombre de host|
|UnauthorizedRequests|Solicitudes de puerta de enlace no autorizadas|Recuento|Total|Número de solicitudes de puerta de enlace no autorizadas|Ubicación, Nombre de host|
|FailedRequests|Solicitudes de puerta de enlace con error|Recuento|Total|Número de errores en solicitudes de puerta de enlace|Ubicación, Nombre de host|
|OtherRequests|Otras solicitudes de puerta de enlace|Recuento|Total|Número de otras solicitudes de puerta de enlace|Ubicación, Nombre de host|
|Duration|Duración total de las solicitudes de puerta de enlace|Milisegundos|Media|Duración total de las solicitudes de puerta de enlace en milisegundos|Ubicación, Nombre de host|
|Capacity|Capacidad (versión preliminar)|Percent|Máxima|Métrica de uso para el servicio ApiManagement|La ubicación|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TotalJob|Número total de trabajos|Recuento|Total|El número total de trabajos (jobs)|RunbookName, Estado|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|CoreCount|Recuento de núcleos dedicados|Recuento|Total|Número total de núcleos dedicados de la cuenta de Batch|Sin dimensiones|
|TotalNodeCount|Recuento de nodos dedicados|Recuento|Total|Número total de nodos dedicados de la cuenta de Batch|Sin dimensiones|
|LowPriorityCoreCount|Recuento de núcleos de baja prioridad|Recuento|Total|Número total de núcleos de baja prioridad de la cuenta de Batch|Sin dimensiones|
|TotalLowPriorityNodeCount|Recuento de nodos de baja prioridad|Recuento|Total|Número total de nodos de baja prioridad de la cuenta de Batch|Sin dimensiones|
|CreatingNodeCount|Recuento de nodos creados|Recuento|Total|Número de nodos que se van a crear|Sin dimensiones|
|StartingNodeCount|Recuento de nodos de inicio|Recuento|Total|Número de nodos que se van a iniciar|Sin dimensiones|
|WaitingForStartTaskNodeCount|Recuento de nodos esperando a la tarea de inicio|Recuento|Total|Número de nodos que esperan a que se complete la tarea de inicio|Sin dimensiones|
|StartTaskFailedNodeCount|Recuento de nodos con error de la tarea de inicio|Recuento|Total|Número de nodos con error en la tarea de inicio|Sin dimensiones|
|IdleNodeCount|Recuento de nodos inactivos|Recuento|Total|Número de nodos inactivos|Sin dimensiones|
|OfflineNodeCount|Recuento de nodos sin conexión|Recuento|Total|Número de nodos sin conexión|Sin dimensiones|
|RebootingNodeCount|Recuento de nodos de reinicio|Recuento|Total|Número de nodos de reinicio|Sin dimensiones|
|ReimagingNodeCount|Recuento de nodos de restablecimiento de imagen inicial|Recuento|Total|Número de nodos de restablecimiento de imagen inicial|Sin dimensiones|
|RunningNodeCount|Recuento de nodos en ejecución|Recuento|Total|Número de nodos en ejecución|Sin dimensiones|
|LeavingPoolNodeCount|Recuento de nodos que salen del grupo|Recuento|Total|Número de nodos que abandonan el grupo|Sin dimensiones|
|UnusableNodeCount|Recuento de nodos no utilizables|Recuento|Total|Número de nodos inutilizables|Sin dimensiones|
|PreemptedNodeCount|Recuento de nodos con prioridad|Recuento|Total|Número de nodos con prioridad|Sin dimensiones|
|TaskStartEvent|Eventos de inicio de tarea|Recuento|Total|Número total de tareas que se han iniciado|Sin dimensiones|
|TaskCompleteEvent|Eventos de tarea completada|Recuento|Total|Número total de tareas que se han completado|Sin dimensiones|
|TaskFailEvent|Eventos de error en tareas|Recuento|Total|Número total de tareas que se han completado con errores|Sin dimensiones|
|PoolCreateEvent|Eventos de creación de grupo|Recuento|Total|Número total de grupos que se han creado|Sin dimensiones|
|PoolResizeStartEvent|Eventos de inicio de cambio de tamaño de grupo|Recuento|Total|Número total de cambios de tamaño de grupo que se han iniciado|Sin dimensiones|
|PoolResizeCompleteEvent|Eventos de finalización de cambio de tamaño de grupo|Recuento|Total|Número total de cambios de tamaño de grupo completados|Sin dimensiones|
|PoolDeleteStartEvent|Eventos de inicio de eliminación de grupo|Recuento|Total|Número total de eliminaciones de grupo que se han iniciado|Sin dimensiones|
|PoolDeleteCompleteEvent|Eventos de finalización de eliminaciones de grupo|Recuento|Total|Número total de eliminaciones de grupo que se han completado|Sin dimensiones|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|connectedclients|Clientes conectados|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed|Total de operaciones|Recuento|Total||Sin dimensiones|
|cachehits|Aciertos de caché|Recuento|Total||Sin dimensiones|
|cachemisses|Errores de caché|Recuento|Total||Sin dimensiones|
|getcommands|Gets|Recuento|Total||Sin dimensiones|
|setcommands|Sets|Recuento|Total||Sin dimensiones|
|evictedkeys|Claves expulsadas|Recuento|Total||Sin dimensiones|
|totalkeys|Total de claves|Recuento|Máxima||Sin dimensiones|
|expiredkeys|Claves expiradas|Recuento|Total||Sin dimensiones|
|usedmemory|Memoria usada|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss|Memoria RSS usada|Bytes|Máxima||Sin dimensiones|
|serverLoad|Carga de servidor|Percent|Máxima||Sin dimensiones|
|cacheWrite|Escritura de caché|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead|Lectura de caché|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime|CPU|Percent|Máxima||Sin dimensiones|
|connectedclients0|Clientes conectados (partición 0)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed0|Total de operaciones (partición 0)|Recuento|Total||Sin dimensiones|
|cachehits0|Aciertos de caché (partición 0)|Recuento|Total||Sin dimensiones|
|cachemisses0|Errores de caché (partición 0)|Recuento|Total||Sin dimensiones|
|getcommands0|Obtenciones (partición 0)|Recuento|Total||Sin dimensiones|
|setcommands0|Definiciones (partición 0)|Recuento|Total||Sin dimensiones|
|evictedkeys0|Claves expulsadas (partición 0)|Recuento|Total||Sin dimensiones|
|totalkeys0|Total de claves (partición 0)|Recuento|Máxima||Sin dimensiones|
|expiredkeys0|Claves expiradas (partición 0)|Recuento|Total||Sin dimensiones|
|usedmemory0|Memoria usada (partición 0)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss0|Memoria RSS usada (partición 0)|Bytes|Máxima||Sin dimensiones|
|serverLoad0|Carga de servidor (partición 0)|Percent|Máxima||Sin dimensiones|
|cacheWrite0|Escritura de caché (partición 0)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead0|Lectura de caché (partición 0)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime0|CPU (partición 0)|Percent|Máxima||Sin dimensiones|
|connectedclients1|Clientes conectados (partición 1)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed1|Total de operaciones (partición 1)|Recuento|Total||Sin dimensiones|
|cachehits1|Aciertos de caché (partición 1)|Recuento|Total||Sin dimensiones|
|cachemisses1|Errores de caché (partición 1)|Recuento|Total||Sin dimensiones|
|getcommands1|Obtenciones (partición 1)|Recuento|Total||Sin dimensiones|
|setcommands1|Definiciones (partición 1)|Recuento|Total||Sin dimensiones|
|evictedkeys1|Claves expulsadas (partición 1)|Recuento|Total||Sin dimensiones|
|totalkeys1|Total de claves (partición 1)|Recuento|Máxima||Sin dimensiones|
|expiredkeys1|Claves expiradas (partición 1)|Recuento|Total||Sin dimensiones|
|usedmemory1|Memoria usada (partición 1)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss1|Memoria RSS usada (partición 1)|Bytes|Máxima||Sin dimensiones|
|serverLoad1|Carga de servidor (partición 1)|Percent|Máxima||Sin dimensiones|
|cacheWrite1|Escritura de caché (partición 1)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead1|Lectura de caché (partición 1)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime1|CPU (partición 1)|Percent|Máxima||Sin dimensiones|
|connectedclients2|Clientes conectados (partición 2)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed2|Total de operaciones (partición 2)|Recuento|Total||Sin dimensiones|
|cachehits2|Aciertos de caché (partición 2)|Recuento|Total||Sin dimensiones|
|cachemisses2|Errores de caché (partición 2)|Recuento|Total||Sin dimensiones|
|getcommands2|Obtenciones (partición 2)|Recuento|Total||Sin dimensiones|
|setcommands2|Definiciones (partición 2)|Recuento|Total||Sin dimensiones|
|evictedkeys2|Claves expulsadas (partición 2)|Recuento|Total||Sin dimensiones|
|totalkeys2|Total de claves (partición 2)|Recuento|Máxima||Sin dimensiones|
|expiredkeys2|Claves expiradas (partición 2)|Recuento|Total||Sin dimensiones|
|usedmemory2|Memoria usada (partición 2)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss2|Memoria RSS usada (partición 2)|Bytes|Máxima||Sin dimensiones|
|serverLoad2|Carga de servidor (partición 2)|Percent|Máxima||Sin dimensiones|
|cacheWrite2|Escritura de caché (partición 2)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead2|Lectura de caché (partición 2)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime2|CPU (partición 2)|Percent|Máxima||Sin dimensiones|
|connectedclients3|Clientes conectados (partición 3)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed3|Total de operaciones (partición 3)|Recuento|Total||Sin dimensiones|
|cachehits3|Aciertos de caché (partición 3)|Recuento|Total||Sin dimensiones|
|cachemisses3|Errores de caché (partición 3)|Recuento|Total||Sin dimensiones|
|getcommands3|Obtenciones (partición 3)|Recuento|Total||Sin dimensiones|
|setcommands3|Definiciones (partición 3)|Recuento|Total||Sin dimensiones|
|evictedkeys3|Claves expulsadas (partición 3)|Recuento|Total||Sin dimensiones|
|totalkeys3|Total de claves (partición 3)|Recuento|Máxima||Sin dimensiones|
|expiredkeys3|Claves expiradas (partición 3)|Recuento|Total||Sin dimensiones|
|usedmemory3|Memoria usada (partición 3)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss3|Memoria RSS usada (partición 3)|Bytes|Máxima||Sin dimensiones|
|serverLoad3|Carga de servidor (partición 3)|Percent|Máxima||Sin dimensiones|
|cacheWrite3|Escritura de caché (partición 3)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead3|Lectura de caché (partición 3)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime3|CPU (partición 3)|Percent|Máxima||Sin dimensiones|
|connectedclients4|Clientes conectados (partición 4)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed4|Total de operaciones (partición 4)|Recuento|Total||Sin dimensiones|
|cachehits4|Aciertos de caché (partición 4)|Recuento|Total||Sin dimensiones|
|cachemisses4|Errores de caché (partición 4)|Recuento|Total||Sin dimensiones|
|getcommands4|Obtenciones (partición 4)|Recuento|Total||Sin dimensiones|
|setcommands4|Definiciones (partición 4)|Recuento|Total||Sin dimensiones|
|evictedkeys4|Claves expulsadas (partición 4)|Recuento|Total||Sin dimensiones|
|totalkeys4|Total de claves (partición 4)|Recuento|Máxima||Sin dimensiones|
|expiredkeys4|Claves expiradas (partición 4)|Recuento|Total||Sin dimensiones|
|usedmemory4|Memoria usada (partición 4)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss4|Memoria RSS usada (partición 4)|Bytes|Máxima||Sin dimensiones|
|serverLoad4|Carga de servidor (partición 4)|Percent|Máxima||Sin dimensiones|
|cacheWrite4|Escritura de caché (partición 4)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead4|Lectura de caché (partición 4)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime4|CPU (partición 4)|Percent|Máxima||Sin dimensiones|
|connectedclients5|Clientes conectados (partición 5)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed5|Total de operaciones (partición 5)|Recuento|Total||Sin dimensiones|
|cachehits5|Aciertos de caché (partición 5)|Recuento|Total||Sin dimensiones|
|cachemisses5|Errores de caché (partición 5)|Recuento|Total||Sin dimensiones|
|getcommands5|Obtenciones (partición 5)|Recuento|Total||Sin dimensiones|
|setcommands5|Definiciones (partición 5)|Recuento|Total||Sin dimensiones|
|evictedkeys5|Claves expulsadas (partición 5)|Recuento|Total||Sin dimensiones|
|totalkeys5|Total de claves (partición 5)|Recuento|Máxima||Sin dimensiones|
|expiredkeys5|Claves expiradas (partición 5)|Recuento|Total||Sin dimensiones|
|usedmemory5|Memoria usada (partición 5)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss5|Memoria RSS usada (partición 5)|Bytes|Máxima||Sin dimensiones|
|serverLoad5|Carga de servidor (partición 5)|Percent|Máxima||Sin dimensiones|
|cacheWrite5|Escritura de caché (partición 5)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead5|Lectura de caché (partición 5)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime5|CPU (partición 5)|Percent|Máxima||Sin dimensiones|
|connectedclients6|Clientes conectados (partición 6)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed6|Total de operaciones (partición 6)|Recuento|Total||Sin dimensiones|
|cachehits6|Aciertos de caché (partición 6)|Recuento|Total||Sin dimensiones|
|cachemisses6|Errores de caché (partición 6)|Recuento|Total||Sin dimensiones|
|getcommands6|Obtenciones (partición 6)|Recuento|Total||Sin dimensiones|
|setcommands6|Definiciones (partición 6)|Recuento|Total||Sin dimensiones|
|evictedkeys6|Claves expulsadas (partición 6)|Recuento|Total||Sin dimensiones|
|totalkeys6|Total de claves (partición 6)|Recuento|Máxima||Sin dimensiones|
|expiredkeys6|Claves expiradas (partición 6)|Recuento|Total||Sin dimensiones|
|usedmemory6|Memoria usada (partición 6)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss6|Memoria RSS usada (partición 6)|Bytes|Máxima||Sin dimensiones|
|serverLoad6|Carga de servidor (partición 6)|Percent|Máxima||Sin dimensiones|
|cacheWrite6|Escritura de caché (partición 6)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead6|Lectura de caché (partición 6)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime6|CPU (partición 6)|Percent|Máxima||Sin dimensiones|
|connectedclients7|Clientes conectados (partición 7)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed7|Total de operaciones (partición 7)|Recuento|Total||Sin dimensiones|
|cachehits7|Aciertos de caché (partición 7)|Recuento|Total||Sin dimensiones|
|cachemisses7|Errores de caché (partición 7)|Recuento|Total||Sin dimensiones|
|getcommands7|Obtenciones (partición 7)|Recuento|Total||Sin dimensiones|
|setcommands7|Definiciones (partición 7)|Recuento|Total||Sin dimensiones|
|evictedkeys7|Claves expulsadas (partición 7)|Recuento|Total||Sin dimensiones|
|totalkeys7|Total de claves (partición 7)|Recuento|Máxima||Sin dimensiones|
|expiredkeys7|Claves expiradas (partición 7)|Recuento|Total||Sin dimensiones|
|usedmemory7|Memoria usada (partición 7)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss7|Memoria RSS usada (partición 7)|Bytes|Máxima||Sin dimensiones|
|serverLoad7|Carga de servidor (partición 7)|Percent|Máxima||Sin dimensiones|
|cacheWrite7|Escritura de caché (partición 7)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead7|Lectura de caché (partición 7)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime7|CPU (partición 7)|Percent|Máxima||Sin dimensiones|
|connectedclients8|Clientes conectados (partición 8)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed8|Total de operaciones (partición 8)|Recuento|Total||Sin dimensiones|
|cachehits8|Aciertos de caché (partición 8)|Recuento|Total||Sin dimensiones|
|cachemisses8|Errores de caché (partición 8)|Recuento|Total||Sin dimensiones|
|getcommands8|Obtenciones (partición 8)|Recuento|Total||Sin dimensiones|
|setcommands8|Definiciones (partición 8)|Recuento|Total||Sin dimensiones|
|evictedkeys8|Claves expulsadas (partición 8)|Recuento|Total||Sin dimensiones|
|totalkeys8|Total de claves (partición 8)|Recuento|Máxima||Sin dimensiones|
|expiredkeys8|Claves expiradas (partición 8)|Recuento|Total||Sin dimensiones|
|usedmemory8|Memoria usada (partición 8)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss8|Memoria RSS usada (partición 8)|Bytes|Máxima||Sin dimensiones|
|serverLoad8|Carga de servidor (partición 8)|Percent|Máxima||Sin dimensiones|
|cacheWrite8|Escritura de caché (partición 8)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead8|Lectura de caché (partición 8)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime8|CPU (partición 8)|Percent|Máxima||Sin dimensiones|
|connectedclients9|Clientes conectados (partición 9)|Recuento|Máxima||Sin dimensiones|
|totalcommandsprocessed9|Total de operaciones (partición 9)|Recuento|Total||Sin dimensiones|
|cachehits9|Aciertos de caché (partición 9)|Recuento|Total||Sin dimensiones|
|cachemisses9|Errores de caché (partición 9)|Recuento|Total||Sin dimensiones|
|getcommands9|Obtenciones (partición 9)|Recuento|Total||Sin dimensiones|
|setcommands9|Definiciones (partición 9)|Recuento|Total||Sin dimensiones|
|evictedkeys9|Claves expulsadas (partición 9)|Recuento|Total||Sin dimensiones|
|totalkeys9|Total de claves (partición 9)|Recuento|Máxima||Sin dimensiones|
|expiredkeys9|Claves expiradas (partición 9)|Recuento|Total||Sin dimensiones|
|usedmemory9|Memoria usada (partición 9)|Bytes|Máxima||Sin dimensiones|
|usedmemoryRss9|Memoria RSS usada (partición 9)|Bytes|Máxima||Sin dimensiones|
|serverLoad9|Carga de servidor (partición 9)|Percent|Máxima||Sin dimensiones|
|cacheWrite9|Escritura de caché (partición 9)|BytesPerSecond|Máxima||Sin dimensiones|
|cacheRead9|Lectura de caché (partición 9)|BytesPerSecond|Máxima||Sin dimensiones|
|percentProcessorTime9|CPU (partición 9)|Percent|Máxima||Sin dimensiones|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Percent|Media|El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente.|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante).|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente).|Sin dimensiones|
|Disk Read Bytes/Sec|Lectura de disco|BytesPerSecond|Media|Promedio de bytes que se leen desde el disco durante el período de supervisión.|Sin dimensiones|
|Disk Write Bytes/Sec|Escritura de disco|BytesPerSecond|Media|Promedio de bytes que se escriben en el disco durante el período de supervisión.|Sin dimensiones|
|Operaciones de lectura de disco por segundo|Operaciones de lectura de disco por segundo|CountPerSecond|Media|E/S por segundo de lectura de disco.|Sin dimensiones|
|Operaciones de escritura por segundo en disco|Operaciones de escritura por segundo en disco|CountPerSecond|Media|E/S por segundo de escritura en disco.|Sin dimensiones|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TotalCalls|Total de llamadas|Recuento|Total|Número total de llamadas.|Sin dimensiones|
|SuccessfulCalls|Llamadas correctas|Recuento|Total|Número de llamadas correctas.|Sin dimensiones|
|TotalErrors|Total de errores|Recuento|Total|Número total de llamadas con respuesta de error (código de respuesta HTTP 4xx o 5xx).|Sin dimensiones|
|BlockedCalls|Llamadas bloqueadas|Recuento|Total|Número de llamadas que han superado la tasa o el límite de cuota.|Sin dimensiones|
|ServerErrors|Errores del servidor|Recuento|Total|Número de llamadas con error interno del servicio (código de respuesta HTTP 5xx).|Sin dimensiones|
|ClientErrors|Errores de cliente|Recuento|Total|Número de llamadas con error interno del lado cliente (código de respuesta HTTP 4xx).|Sin dimensiones|
|DataIn|Entrada de datos|Bytes|Total|Tamaño de los datos de entrada en bytes.|Sin dimensiones|
|DataOut|Salida de datos|Bytes|Total|Tamaño de los datos de salida en bytes.|Sin dimensiones|
|Latency|Latency|MilliSeconds|Media|Latencia en milisegundos.|Sin dimensiones|
|CharactersTranslated|Caracteres traducidos|Recuento|Total|Número total de caracteres de la solicitud entrante de texto.|Sin dimensiones|
|SpeechSessionDuration|Duración de la sesión de voz|Segundos|Total|Duración total de la sesión de voz en segundos.|Sin dimensiones|
|TotalTransactions|Transacciones totales|Recuento|Total|Número total de transacciones|Sin dimensiones|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Percent|Media|El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|Sin dimensiones|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes que se leen desde el disco durante el período de supervisión|Sin dimensiones|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes que se escriben en el disco durante el período de supervisión|Sin dimensiones|
|Operaciones de lectura de disco por segundo|Operaciones de lectura de disco por segundo|CountPerSecond|Media|E/S por segundo de lectura de disco|Sin dimensiones|
|Operaciones de escritura por segundo en disco|Operaciones de escritura por segundo en disco|CountPerSecond|Media|E/S por segundo de escritura en disco|Sin dimensiones|
|Créditos de CPU restantes|Créditos de CPU restantes|Recuento|Media|Número total de créditos disponibles para irrumpir|Sin dimensiones|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Recuento|Media|Número total de créditos consumidos por la máquina virtual|Sin dimensiones|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Percent|Media|El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|Sin dimensiones|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes que se leen desde el disco durante el período de supervisión|Sin dimensiones|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes que se escriben en el disco durante el período de supervisión|Sin dimensiones|
|Operaciones de lectura de disco por segundo|Operaciones de lectura de disco por segundo|CountPerSecond|Media|E/S por segundo de lectura de disco|Sin dimensiones|
|Operaciones de escritura por segundo en disco|Operaciones de escritura por segundo en disco|CountPerSecond|Media|E/S por segundo de escritura en disco|Sin dimensiones|
|Créditos de CPU restantes|Créditos de CPU restantes|Recuento|Media|Número total de créditos disponibles para irrumpir|Sin dimensiones|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Recuento|Media|Número total de créditos consumidos por la máquina virtual|Sin dimensiones|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Porcentaje de CPU|Porcentaje de CPU|Percent|Media|El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante)|Sin dimensiones|
|Red interna|Red interna|Bytes|Total|El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente)|Sin dimensiones|
|Bytes de lectura de disco|Bytes de lectura de disco|Bytes|Total|Número total de bytes que se leen desde el disco durante el período de supervisión|Sin dimensiones|
|Bytes de escritura de disco|Bytes de escritura de disco|Bytes|Total|Número total de bytes que se escriben en el disco durante el período de supervisión|Sin dimensiones|
|Operaciones de lectura de disco por segundo|Operaciones de lectura de disco por segundo|CountPerSecond|Media|E/S por segundo de lectura de disco|Sin dimensiones|
|Operaciones de escritura por segundo en disco|Operaciones de escritura por segundo en disco|CountPerSecond|Media|E/S por segundo de escritura en disco|Sin dimensiones|
|Créditos de CPU restantes|Créditos de CPU restantes|Recuento|Media|Número total de créditos disponibles para irrumpir|Sin dimensiones|
|Créditos de CPU consumidos|Créditos de CPU consumidos|Recuento|Media|Número total de créditos consumidos por la máquina virtual|Sin dimensiones|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|DCIApiCalls|Llamadas API Customer Insights|Recuento|Total||Sin dimensiones|
|DCIMappingImportOperationSuccessfulLines|Líneas correctas de la operación de importación de asignaciones|Recuento|Total||Sin dimensiones|
|DCIMappingImportOperationFailedLines|Líneas con error de la operación de importación de asignaciones|Recuento|Total||Sin dimensiones|
|DCIMappingImportOperationTotalLines|Líneas totales de la operación de importación de asignaciones|Recuento|Total||Sin dimensiones|
|DCIMappingImportOperationRuntimeInSeconds|Tiempo de ejecución en segundos de la operación de importación de asignaciones|Segundos|Total||Sin dimensiones|
|DCIOutboundProfileExportSucceeded|Exportación correcta del perfil saliente|Recuento|Total||Sin dimensiones|
|DCIOutboundProfileExportFailed|Error de exportación del perfil saliente|Recuento|Total||Sin dimensiones|
|DCIOutboundProfileExportDuration|Duración de la exportación del perfil saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundKpiExportSucceeded|Exportación correcta de KPI saliente|Recuento|Total||Sin dimensiones|
|DCIOutboundKpiExportFailed|Error de exportación de KPI saliente|Recuento|Total||Sin dimensiones|
|DCIOutboundKpiExportDuration|Duración de exportación de KPI saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundKpiExportStarted|Inicio de exportación de KPI saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundKpiRecordCount|Recuento de registros de KPI saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundProfileExportCount|Recuento de exportación de perfil saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundInitialProfileExportFailed|Recuento de exportación de perfil inicial saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundInitialProfileExportSucceeded|Exportación correcta de perfil inicial saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundInitialKpiExportFailed|Error de exportación de KPI inicial saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundInitialKpiExportSucceeded|Exportación correcta de KPI inicial saliente|Segundos|Total||Sin dimensiones|
|DCIOutboundInitialProfileExportDurationInSeconds|Duración en segundos de exportación de perfil inicial saliente|Segundos|Total||Sin dimensiones|
|AdlaJobForStandardKpiFailed|Error en segundos de trabajo de Adla para KPI estándar|Segundos|Total||Sin dimensiones|
|AdlaJobForStandardKpiTimeOut|Tiempo de espera en segundos del trabajo de Adla para KPI estándar|Segundos|Total||Sin dimensiones|
|AdlaJobForStandardKpiCompleted|Tiempo de finalización en segundos del trabajo de Adla para KPI estándar|Segundos|Total||Sin dimensiones|
|ImportASAValuesFailed|Error de recuento de valores ASA de importación|Recuento|Total||Sin dimensiones|
|ImportASAValuesSucceeded|Recuento correcto de valores ASA de importación|Recuento|Total||Sin dimensiones|
|DCIProfilesCount|Recuento de instancias del perfil|Recuento|Último||Sin dimensiones|
|DCIInteractionsPerMonthCount|Recuento de interacciones por mes|Recuento|Último||Sin dimensiones|
|DCIKpisCount|Recuento de KPI|Recuento|Último||Sin dimensiones|
|DCISegmentsCount|Recuento de segmentos|Recuento|Último||Sin dimensiones|
|DCIPredictiveMatchPoliciesCount|Recuento de coincidencias predictivo|Recuento|Último||Sin dimensiones|
|DCIPredictionsCount|Recuento de predicción|Recuento|Último||Sin dimensiones|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|SuccessfulRuns|Ejecuciones correctas|Recuento|Total|Número de ejecuciones correctas.|Sin dimensiones|
|FailedRuns|Ejecuciones con error|Recuento|Total|Número de ejecuciones con error.|Sin dimensiones|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de ejecuciones de canalización erróneas|Recuento|Total||Sin dimensiones|
|PipelineSucceededRuns|Las métricas de ejecuciones de canalización se realizaron correctamente|Recuento|Total||Sin dimensiones|
|ActivityFailedRuns|Métricas de ejecuciones de actividad erróneas|Recuento|Total||Sin dimensiones|
|ActivitySucceededRuns|Métricas de ejecución de actividad realizadas correctamente|Recuento|Total||Sin dimensiones|
|TriggerFailedRuns|Métricas de ejecuciones de desencadenador erróneas|Recuento|Total||Sin dimensiones|
|TriggerSucceededRuns|Métricas de ejecuciones de desencadenador realizadas correctamente|Recuento|Total||Sin dimensiones|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabajos correctos|Recuento|Total|Recuento de trabajos realizados correctamente|Sin dimensiones|
|JobEndedFailure|Trabajos con error|Recuento|Total|Recuento de trabajos con error|Sin dimensiones|
|JobEndedCancelled|Trabajos cancelados|Recuento|Total|Recuento de trabajos cancelados|Sin dimensiones|
|JobAUEndedSuccess|Tiempo AU correcto|Segundos|Total|Tiempo total AU para trabajos realizados correctamente|Sin dimensiones|
|JobAUEndedFailure|Error de tiempo AU|Segundos|Total|Tiempo total AU para trabajos con error|Sin dimensiones|
|JobAUEndedCancelled|Tiempo AU para cancelados|Segundos|Total|Tiempo total AU para trabajos cancelados|Sin dimensiones|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TotalStorage|Almacenamiento total|Bytes|Máxima|Cantidad total de datos almacenados en la cuenta.|Sin dimensiones|
|DataWritten|Datos escritos|Bytes|Total|Cantidad total de datos escritos en la cuenta.|Sin dimensiones|
|DataRead|Datos leídos|Bytes|Total|Cantidad total de datos leídos de la cuenta.|Sin dimensiones|
|WriteRequests|Solicitudes de escritura|Recuento|Total|Número de solicitudes de escritura de datos en la cuenta.|Sin dimensiones|
|ReadRequests|Solicitudes de lectura|Recuento|Total|Número de solicitudes de lectura de datos de la cuenta.|Sin dimensiones|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Sin dimensiones|
|compute_limit|Límite de unidad de proceso|Recuento|Media|Límite de unidad de proceso|Sin dimensiones|
|compute_consumption_percent|Porcentaje de unidad de proceso|Percent|Media|Porcentaje de unidad de proceso|Sin dimensiones|
|memory_percent|Porcentaje de memoria|Percent|Media|Porcentaje de memoria|Sin dimensiones|
|io_consumption_percent|Porcentaje de E/S|Percent|Media|Porcentaje de E/S|Sin dimensiones|
|storage_percent|Porcentaje de almacenamiento|Percent|Media|Porcentaje de almacenamiento|Sin dimensiones|
|storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|Sin dimensiones|
|storage_limit|Límite de almacenamiento|Bytes|Media|Límite de almacenamiento|Sin dimensiones|
|active_connections|Conexiones activas totales|Recuento|Media|Conexiones activas totales|Sin dimensiones|
|connections_failed|Conexiones con errores totales|Recuento|Media|Conexiones con errores totales|Sin dimensiones|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Sin dimensiones|
|compute_limit|Límite de unidad de proceso|Recuento|Media|Límite de unidad de proceso|Sin dimensiones|
|compute_consumption_percent|Porcentaje de unidad de proceso|Percent|Media|Porcentaje de unidad de proceso|Sin dimensiones|
|memory_percent|Porcentaje de memoria|Percent|Media|Porcentaje de memoria|Sin dimensiones|
|io_consumption_percent|Porcentaje de E/S|Percent|Media|Porcentaje de E/S|Sin dimensiones|
|storage_percent|Porcentaje de almacenamiento|Percent|Media|Porcentaje de almacenamiento|Sin dimensiones|
|storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|Sin dimensiones|
|storage_limit|Límite de almacenamiento|Bytes|Media|Límite de almacenamiento|Sin dimensiones|
|active_connections|Conexiones activas totales|Recuento|Media|Conexiones activas totales|Sin dimensiones|
|connections_failed|Conexiones con errores totales|Recuento|Media|Conexiones con errores totales|Sin dimensiones|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Intentos de envío de mensajes de telemetría|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube para enviar a IoT Hub|Sin dimensiones|
|d2c.telemetry.ingress.success|Mensajes de telemetría enviados|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube enviados correctamente a IoT Hub|Sin dimensiones|
|c2d.commands.egress.complete.success|Comandos completados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha completado correctamente|Sin dimensiones|
|c2d.commands.egress.abandon.success|Comandos abandonados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha abandonado|Sin dimensiones|
|c2d.commands.egress.reject.success|Comandos rechazados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha rechazado|Sin dimensiones|
|devices.totalDevices|Número total de dispositivos|Recuento|Total|Número de dispositivos registrados en IoT Hub|Sin dimensiones|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Recuento|Total|Número de dispositivos conectados a IoT Hub|Sin dimensiones|
|d2c.telemetry.egress.success|Mensajes de telemetría entregados|Recuento|Total|Número de veces que los mensajes se han escrito correctamente mensajes en los puntos de conexión (total)|Sin dimensiones|
|d2c.telemetry.egress.dropped|Mensajes descartados|Recuento|Total|Número de mensajes quitados porque el punto de conexión de entrega estaba inactivo|Sin dimensiones|
|d2c.telemetry.egress.orphaned|Mensajes huérfanos|Recuento|Total|El recuento de mensajes no coincide con ninguna ruta, incluida la ruta de reserva|Sin dimensiones|
|d2c.telemetry.egress.invalid|Mensajes no válidos|Recuento|Total|El recuento de mensajes no entregados debido a incompatibilidad con el punto de conexión|Sin dimensiones|
|d2c.telemetry.egress.fallback|Mensajes que coinciden con la condición de reserva|Recuento|Total|Número de mensajes escritos en el punto de conexión de reserva|Sin dimensiones|
|d2c.endpoints.egress.eventHubs|Mensajes entregados a los puntos de conexión de Event Hub|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de Event Hub|Sin dimensiones|
|d2c.endpoints.latency.eventHubs|Latencia de mensajes para los puntos de conexión del Centro de eventos|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de Event Hubs, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.serviceBusQueues|Mensajes entregados a los puntos de conexión de la cola de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de la cola de Service Bus|Sin dimensiones|
|d2c.endpoints.latency.serviceBusQueues|Latencia de mensajes para los puntos de conexión de la cola de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de la cola de Service Bus, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.serviceBusTopics|Mensajes entregados a los puntos de conexión del tema de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión del tema de Service Bus|Sin dimensiones|
|d2c.endpoints.latency.serviceBusTopics|Latencia de mensajes para los puntos de conexión del tema de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión del tema de Service Bus, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.builtIn.events|Mensajes entregados al punto de conexión integrado (mensajes y eventos)|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en el punto de conexión integrado (mensajes y eventos)|Sin dimensiones|
|d2c.endpoints.latency.builtIn.events|Latencia de mensajes para el punto de conexión integrado (mensajes y eventos)|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en el punto de conexión integrado (mensajes/eventos), en milisegundos |Sin dimensiones|
|d2c.endpoints.egress.storage|Mensajes entregados a los puntos de conexión de almacenamiento|Recuento|Total|Número de veces que los mensajes se han escrito correctamente mensajes en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.endpoints.latency.storage|Latencia de mensajes para los puntos de conexión de almacenamiento|Milisegundos|Media|La latencia media entre la entrada de mensajes en la instancia de IoT Hub y la entrada de mensajes en un punto de conexión de almacenamiento, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.storage.bytes|Datos escritos en el almacenamiento|Bytes|Total|Cantidad de datos, en bytes, que se escriben en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.endpoints.egress.storage.blobs|Blobs escritos en el almacenamiento|Recuento|Total|Número de blobs que se escriben en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.twin.read.success|Lecturas gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.read.failure|Lecturas gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.read.size|Tamaño de la respuesta de las lecturas gemelas de dispositivos|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.success|Actualizaciones gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.failure|Actualizaciones gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.size|Tamaño de las actualizaciones gemelas de los dispositivos|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|c2d.methods.success|Invocaciones correctas al método directo|Recuento|Total|El número de todas las llamadas correctas al método directo.|Sin dimensiones|
|c2d.methods.failure|Invocaciones al método directo con error|Recuento|Total|El número de todas las llamadas al método directo con error.|Sin dimensiones|
|c2d.methods.requestSize|Tamaño de la solicitud de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las solicitudes correctas de método directo.|Sin dimensiones|
|c2d.methods.responseSize|Tamaño de la respuesta de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las respuestas correctas de método directo.|Sin dimensiones|
|c2d.twin.read.success|Lecturas gemelas correctas del back-end|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.read.failure|Lecturas gemelas con error del back-end|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.read.size|Tamaño de la respuesta de las lecturas gemelas del back-end|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.success|Actualizaciones gemelas correctas del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.failure|Actualizaciones gemelas con error del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.size|Tamaño de las actualizaciones gemelas del back-end|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|twinQueries.success|Consultas gemelas correctas|Recuento|Total|El recuento de todas las consultas gemelas correctas.|Sin dimensiones|
|twinQueries.failure|Consultas gemelas con error|Recuento|Total|El recuento de todas las consultas gemelas con error.|Sin dimensiones|
|twinQueries.resultSize|Tamaño de resultado de consultas gemelas|Bytes|Media|El valor medio, mínimo y máximo del tamaño del resultado de todas las consultas gemelas correctas.|Sin dimensiones|
|jobs.createTwinUpdateJob.success|Creaciones correctas de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones correctas de trabajos de actualización gemelos.|Sin dimensiones|
|jobs.createTwinUpdateJob.failure|Creaciones con error de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones con error de trabajos de actualización gemelos.|Sin dimensiones|
|jobs.createDirectMethodJob.success|Creaciones correctas de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones correctas de los trabajos de invocación de método directos.|Sin dimensiones|
|jobs.createDirectMethodJob.failure|Creaciones con error de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones con error de los trabajos de invocación de método directos.|Sin dimensiones|
|jobs.listJobs.success|Llamadas correctas para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas correctas para enumerar trabajos.|Sin dimensiones|
|jobs.listJobs.failure|Llamadas con error para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas con error para enumerar trabajos.|Sin dimensiones|
|jobs.cancelJob.success|Cancelaciones de trabajos correctas|Recuento|Total|El recuento de todas las llamadas correctas para cancelar un trabajo.|Sin dimensiones|
|jobs.cancelJob.failure|Cancelaciones de trabajos con error|Recuento|Total|El recuento de todas las llamadas con error para cancelar un trabajo.|Sin dimensiones|
|jobs.queryJobs.success|Consultas de trabajo correctas|Recuento|Total|El recuento de todas las llamadas correctas para consultar trabajos.|Sin dimensiones|
|jobs.queryJobs.failure|Consultas de trabajo con error|Recuento|Total|El recuento de todas las llamadas con error para consultar trabajos.|Sin dimensiones|
|jobs.completed|Trabajos completados|Recuento|Total|El recuento de todos los trabajos completados.|Sin dimensiones|
|jobs.failed|Trabajos con error|Recuento|Total|El recuento de todos los trabajos con error.|Sin dimensiones|
|d2c.telemetry.ingress.sendThrottle|Número de errores de limitación|Recuento|Total|Número de errores de limitación debido a las limitaciones de rendimiento del dispositivo|Sin dimensiones|
|dailyMessageQuotaUsed|Número total de mensajes usados|Recuento|Media|Número de mensajes totales usados hoy. Se trata de un valor acumulado que se restablece en cero a las 00:00 UTC cada día.|Sin dimensiones|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|RegistrationAttempts|Intentos de registro|Recuento|Total|Número de intentos de registro de dispositivos|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivos asignados|Recuento|Total|Número de dispositivos asignados a un IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Intentos de atestación|Recuento|Total|Número de intentos de atestación de dispositivos|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|tasa de uso solicitado|Percent|Media|tasa de uso solicitado|Sin dimensiones|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|tasa de uso solicitado|Percent|Media|tasa de uso solicitado|Sin dimensiones|
|deviceDataUsage|Uso total de datos del dispositivo|Recuento|Total|Bytes transferidos hacia y desde cualquier dispositivo conectado al Centro de IOT|Sin dimensiones|
|d2c.telemetry.ingress.allProtocol|Intentos de envío de mensajes de telemetría|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube para enviar a IoT Hub|Sin dimensiones|
|d2c.telemetry.ingress.success|Mensajes de telemetría enviados|Recuento|Total|Número de mensajes de telemetría de dispositivo a la nube enviados correctamente a IoT Hub|Sin dimensiones|
|c2d.commands.egress.complete.success|Comandos completados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha completado correctamente|Sin dimensiones|
|c2d.commands.egress.abandon.success|Comandos abandonados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha abandonado|Sin dimensiones|
|c2d.commands.egress.reject.success|Comandos rechazados|Recuento|Total|Número de comandos de la nube al dispositivo que el dispositivo ha rechazado|Sin dimensiones|
|devices.totalDevices|Número total de dispositivos|Recuento|Total|Número de dispositivos registrados en IoT Hub|Sin dimensiones|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Recuento|Total|Número de dispositivos conectados a IoT Hub|Sin dimensiones|
|d2c.telemetry.egress.success|Mensajes de telemetría entregados|Recuento|Total|Número de veces que los mensajes se han escrito correctamente mensajes en los puntos de conexión (total)|Sin dimensiones|
|d2c.telemetry.egress.dropped|Mensajes descartados|Recuento|Total|Número de mensajes quitados porque el punto de conexión de entrega estaba inactivo|Sin dimensiones|
|d2c.telemetry.egress.orphaned|Mensajes huérfanos|Recuento|Total|El recuento de mensajes no coincide con ninguna ruta, incluida la ruta de reserva|Sin dimensiones|
|d2c.telemetry.egress.invalid|Mensajes no válidos|Recuento|Total|El recuento de mensajes no entregados debido a incompatibilidad con el punto de conexión|Sin dimensiones|
|d2c.telemetry.egress.fallback|Mensajes que coinciden con la condición de reserva|Recuento|Total|Número de mensajes escritos en el punto de conexión de reserva|Sin dimensiones|
|d2c.endpoints.egress.eventHubs|Mensajes entregados a los puntos de conexión de Event Hub|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de Event Hub|Sin dimensiones|
|d2c.endpoints.latency.eventHubs|Latencia de mensajes para los puntos de conexión del Centro de eventos|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de Event Hubs, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.serviceBusQueues|Mensajes entregados a los puntos de conexión de la cola de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión de la cola de Service Bus|Sin dimensiones|
|d2c.endpoints.latency.serviceBusQueues|Latencia de mensajes para los puntos de conexión de la cola de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión de la cola de Service Bus, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.serviceBusTopics|Mensajes entregados a los puntos de conexión del tema de Service Bus|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en los puntos de conexión del tema de Service Bus|Sin dimensiones|
|d2c.endpoints.latency.serviceBusTopics|Latencia de mensajes para los puntos de conexión del tema de Service Bus|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en un punto de conexión del tema de Service Bus, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.builtIn.events|Mensajes entregados al punto de conexión integrado (mensajes y eventos)|Recuento|Total|Número de veces que los mensajes se han escrito correctamente en el punto de conexión integrado (mensajes y eventos)|Sin dimensiones|
|d2c.endpoints.latency.builtIn.events|Latencia de mensajes para el punto de conexión integrado (mensajes y eventos)|Milisegundos|Media|La latencia media entre la entrada de mensajes en el centro de IoT y la entrada de mensajes en el punto de conexión integrado (mensajes/eventos), en milisegundos |Sin dimensiones|
|d2c.endpoints.egress.storage|Mensajes entregados a los puntos de conexión de almacenamiento|Recuento|Total|Número de veces que los mensajes se han escrito correctamente mensajes en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.endpoints.latency.storage|Latencia de mensajes para los puntos de conexión de almacenamiento|Milisegundos|Media|La latencia media entre la entrada de mensajes en la instancia de IoT Hub y la entrada de mensajes en un punto de conexión de almacenamiento, en milisegundos|Sin dimensiones|
|d2c.endpoints.egress.storage.bytes|Datos escritos en el almacenamiento|Bytes|Total|Cantidad de datos, en bytes, que se escriben en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.endpoints.egress.storage.blobs|Blobs escritos en el almacenamiento|Recuento|Total|Número de blobs que se escriben en los puntos de conexión de almacenamiento|Sin dimensiones|
|d2c.twin.read.success|Lecturas gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.read.failure|Lecturas gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.read.size|Tamaño de la respuesta de las lecturas gemelas de dispositivos|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.success|Actualizaciones gemelas correctas de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.failure|Actualizaciones gemelas con error de los dispositivos|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el dispositivo.|Sin dimensiones|
|d2c.twin.update.size|Tamaño de las actualizaciones gemelas de los dispositivos|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el dispositivo.|Sin dimensiones|
|c2d.methods.success|Invocaciones correctas al método directo|Recuento|Total|El número de todas las llamadas correctas al método directo.|Sin dimensiones|
|c2d.methods.failure|Invocaciones al método directo con error|Recuento|Total|El número de todas las llamadas al método directo con error.|Sin dimensiones|
|c2d.methods.requestSize|Tamaño de la solicitud de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las solicitudes correctas de método directo.|Sin dimensiones|
|c2d.methods.responseSize|Tamaño de la respuesta de las invocaciones a métodos directos|Bytes|Media|El valor medio, mínimo y máximo de todas las respuestas correctas de método directo.|Sin dimensiones|
|c2d.twin.read.success|Lecturas gemelas correctas del back-end|Recuento|Total|El recuento de todas las lecturas gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.read.failure|Lecturas gemelas con error del back-end|Recuento|Total|El recuento de todas las lecturas gemelas con error iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.read.size|Tamaño de la respuesta de las lecturas gemelas del back-end|Bytes|Media|El valor medio, mínimo y máximo de todas las lecturas gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.success|Actualizaciones gemelas correctas del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.failure|Actualizaciones gemelas con error del back-end|Recuento|Total|El recuento de todas las actualizaciones gemelas con error iniciadas por el back-end.|Sin dimensiones|
|c2d.twin.update.size|Tamaño de las actualizaciones gemelas del back-end|Bytes|Media|El tamaño medio, mínimo y máximo de todas las actualizaciones gemelas correctas iniciadas por el back-end.|Sin dimensiones|
|twinQueries.success|Consultas gemelas correctas|Recuento|Total|El recuento de todas las consultas gemelas correctas.|Sin dimensiones|
|twinQueries.failure|Consultas gemelas con error|Recuento|Total|El recuento de todas las consultas gemelas con error.|Sin dimensiones|
|twinQueries.resultSize|Tamaño de resultado de consultas gemelas|Bytes|Media|El valor medio, mínimo y máximo del tamaño del resultado de todas las consultas gemelas correctas.|Sin dimensiones|
|jobs.createTwinUpdateJob.success|Creaciones correctas de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones correctas de trabajos de actualización gemelos.|Sin dimensiones|
|jobs.createTwinUpdateJob.failure|Creaciones con error de trabajos de actualización gemelos|Recuento|Total|El recuento de todas las creaciones con error de trabajos de actualización gemelos.|Sin dimensiones|
|jobs.createDirectMethodJob.success|Creaciones correctas de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones correctas de los trabajos de invocación de método directos.|Sin dimensiones|
|jobs.createDirectMethodJob.failure|Creaciones con error de trabajos de invocación de método|Recuento|Total|El recuento de todas las creaciones con error de los trabajos de invocación de método directos.|Sin dimensiones|
|jobs.listJobs.success|Llamadas correctas para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas correctas para enumerar trabajos.|Sin dimensiones|
|jobs.listJobs.failure|Llamadas con error para enumerar trabajos|Recuento|Total|El recuento de todas las llamadas con error para enumerar trabajos.|Sin dimensiones|
|jobs.cancelJob.success|Cancelaciones de trabajos correctas|Recuento|Total|El recuento de todas las llamadas correctas para cancelar un trabajo.|Sin dimensiones|
|jobs.cancelJob.failure|Cancelaciones de trabajos con error|Recuento|Total|El recuento de todas las llamadas con error para cancelar un trabajo.|Sin dimensiones|
|jobs.queryJobs.success|Consultas de trabajo correctas|Recuento|Total|El recuento de todas las llamadas correctas para consultar trabajos.|Sin dimensiones|
|jobs.queryJobs.failure|Consultas de trabajo con error|Recuento|Total|El recuento de todas las llamadas con error para consultar trabajos.|Sin dimensiones|
|jobs.completed|Trabajos completados|Recuento|Total|El recuento de todos los trabajos completados.|Sin dimensiones|
|jobs.failed|Trabajos con error|Recuento|Total|El recuento de todos los trabajos con error.|Sin dimensiones|
|d2c.telemetry.ingress.sendThrottle|Número de errores de limitación|Recuento|Total|Número de errores de limitación debido a las limitaciones de rendimiento del dispositivo|Sin dimensiones|
|dailyMessageQuotaUsed|Número total de mensajes usados|Recuento|Media|Número de mensajes totales usados hoy. Se trata de un valor acumulado que se restablece en cero a las 00:00 UTC cada día.|Sin dimensiones|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|MetadataRequests|Solicitudes de metadatos|Recuento|Recuento|Recuento de las solicitudes de metadatos. Cosmos DB mantiene la colección de metadatos del sistema para cada cuenta, lo que le permite enumerar las colecciones, las bases de datos y sus configuraciones de forma gratuita.|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Cargo de la solicitud de Mongo|Recuento|Total|Unidades de la solicitud de Mongo consumidas|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Solicitudes de Mongo|Recuento|Recuento|Número de solicitudes de Mongo realizadas|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|TotalRequestUnits|Unidades de solicitud totales|Recuento|Total|Unidades de solicitud consumidas|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|Total de solicitudes|Recuento|Recuento|Número de solicitudes realizadas|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitudes correctas (versión preliminar)|Recuento|Total|Solicitudes correctas de Microsoft.EventHub. (versión preliminar)|EntityName|
|ServerErrors|Errores del servidor. (versión preliminar)|Recuento|Total|Errores del servidor de Microsoft.EventHub. (versión preliminar)|EntityName|
|UserErrors|Errores de usuario. (versión preliminar)|Recuento|Total|Errores de usuario de Microsoft.EventHub. (versión preliminar)|EntityName|
|QuotaExceededErrors|Cuota de errores superada. (versión preliminar)|Recuento|Total|Cuota de errores superada de Microsoft.EventHub. (versión preliminar)|EntityName|
|ThrottledRequests|Solicitudes limitadas. (versión preliminar)|Recuento|Total|Solicitudes limitadas de Microsoft.EventHub. (versión preliminar)|EntityName|
|IncomingRequests|Solicitudes entrantes (versión preliminar)|Recuento|Total|Solicitudes entrantes de Microsoft.EventHub. (versión preliminar)|EntityName|
|IncomingMessages|Mensajes entrantes (versión preliminar)|Recuento|Total|Mensajes entrantes de Microsoft.EventHub. (versión preliminar)|EntityName|
|OutgoingMessages|Mensajes salientes (versión preliminar)|Recuento|Total|Mensajes salientes de Microsoft.EventHub. (versión preliminar)|EntityName|
|IncomingBytes|Bytes entrantes. (versión preliminar)|Bytes|Total|Bytes entrantes de Microsoft.EventHub. (versión preliminar)|EntityName|
|OutgoingBytes|Bytes salientes. (versión preliminar)|Bytes|Total|Bytes salientes de Microsoft.EventHub. (versión preliminar)|EntityName|
|ActiveConnections|ActiveConnections (versión preliminar)|Recuento|Total|Total de conexiones activas de Microsoft.EventHub. (versión preliminar)|EntityName|
|ConnectionsOpened|Conexiones abiertas. (versión preliminar)|Recuento|Total|Conexiones abiertas de Microsoft.EventHub. (versión preliminar)|EntityName|
|ConnectionsClosed|Conexiones cerradas. (versión preliminar)|Recuento|Total|Conexiones cerradas de Microsoft.EventHub. (versión preliminar)|EntityName|
|CaptureBacklog|Capturar el trabajo pendiente. (versión preliminar)|Recuento|Total|Capturar el trabajo pendiente de Microsoft.EventHub. (versión preliminar)|EntityName|
|CapturedMessages|Mensajes capturados. (versión preliminar)|Recuento|Total|Mensajes capturados de Microsoft.EventHub. (versión preliminar)|EntityName|
|CapturedBytes|Bytes capturados. (versión preliminar)|Bytes|Total|Bytes capturados de Microsoft.EventHub. (versión preliminar)|EntityName|
|INREQS|Solicitudes entrantes|Recuento|Total|Total de solicitudes de envío entrantes de un espacio de nombres|Sin dimensiones|
|SUCCREQ|Solicitudes correctas|Recuento|Total|Número total de solicitudes correctas para un espacio de nombres|Sin dimensiones|
|FAILREQ|Solicitudes con error|Recuento|Total|Número total de solicitudes erróneas para un espacio de nombres|Sin dimensiones|
|SVRBSY|Errores de servidor ocupado|Recuento|Total|Errores totales de servidor ocupado para un espacio de nombres|Sin dimensiones|
|INTERR|Errores internos del servidor|Recuento|Total|Errores totales de servidor interno para un espacio de nombres|Sin dimensiones|
|MISCERR|Otros errores|Recuento|Total|Número total de solicitudes erróneas para un espacio de nombres|Sin dimensiones|
|INMSGS|Mensajes entrantes (en desuso)|Recuento|Total|Total de mensajes entrantes para un espacio de nombres. Esta métrica está en desuso. En su lugar, use la métrica Mensajes entrantes|Sin dimensiones|
|EHINMSGS|Mensajes entrantes|Recuento|Total|Total de mensajes entrantes para un espacio de nombres|Sin dimensiones|
|OUTMSGS|Mensajes salientes (en desuso)|Recuento|Total|Total de mensajes salientes para un espacio de nombres. Esta métrica está en desuso. En su lugar, use la métrica Mensajes salientes|Sin dimensiones|
|EHOUTMSGS|Mensajes salientes|Recuento|Total|Total de mensajes salientes para un espacio de nombres|Sin dimensiones|
|EHINMBS|Bytes de entrada (en desuso)|Bytes|Total|Rendimiento de mensajes entrantes del centro de eventos para un espacio de nombres. Esta métrica está en desuso. En su lugar, use la métrica Bytes de entrada|Sin dimensiones|
|EHINBYTES|Bytes de entrada|Bytes|Total|Rendimiento de mensajes entrantes del centro de eventos para un espacio de nombres|Sin dimensiones|
|EHOUTMBS|Bytes de salida (en desuso)|Bytes|Total|Rendimiento de mensajes salientes del centro de eventos para un espacio de nombres. Esta métrica está en desuso. En su lugar, use la métrica Bytes de salida|Sin dimensiones|
|EHOUTBYTES|Bytes de salida|Bytes|Total|Rendimiento de mensajes salientes del centro de eventos para un espacio de nombres|Sin dimensiones|
|EHABL|Mensajes de trabajo pendiente de archivado|Recuento|Total|Mensajes de archivo del centro de eventos en el trabajo pendiente para un espacio de nombres|Sin dimensiones|
|EHAMSGS|Mensajes de archivado|Recuento|Total|Mensajes archivados del centro de eventos en un espacio de nombres|Sin dimensiones|
|EHAMBS|Rendimiento de mensajes de archivado|Bytes|Total|Rendimiento de mensajes archivados del centro de eventos en un espacio de nombres|Sin dimensiones|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de métrica observado|Recuento|Media|El valor calculado por el escalado automático cuando se ejecuta|MetricTriggerSource|
|MetricThreshold|Umbral de métrica|Recuento|Media|El umbral configurado de escalado automático cuando se ejecutó el escalado automático.|MetricTriggerRule|
|ObservedCapacity|Capacidad observada|Recuento|Media|La capacidad informada al escalado automático cuando se ejecutó.|Sin dimensiones|
|ScaleActionsInitiated|Acciones de escalado iniciadas|Recuento|Total|La dirección de la operación de escalado.|ScaleDirection|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Latency|Latency|Milisegundos|Media|Duración de las llamadas a la API|OperationName, OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|RunsStarted|Ejecuciones iniciadas|Recuento|Total|Número de ejecuciones de flujo de trabajo iniciadas|Sin dimensiones|
|RunsCompleted|Ejecuciones completadas|Recuento|Total|Número de ejecuciones de flujo de trabajo completadas|Sin dimensiones|
|RunsSucceeded|Ejecuciones correctas|Recuento|Total|Número de ejecuciones de flujo de trabajo correctas|Sin dimensiones|
|RunsFailed|Ejecuciones con errores|Recuento|Total|Número de ejecuciones de flujo de trabajo con errores|Sin dimensiones|
|RunsCancelled|Ejecuciones canceladas|Recuento|Total|Número de ejecuciones de flujo de trabajo canceladas|Sin dimensiones|
|RunLatency|Latencia de ejecución|Segundos|Media|Latencia de ejecuciones de flujo de trabajo completadas|Sin dimensiones|
|RunSuccessLatency|Latencia de ejecuciones correctas|Segundos|Media|Latencia de ejecuciones de flujo de trabajo correctas|Sin dimensiones|
|RunThrottledEvents|Ejecución de eventos limitados|Recuento|Total|Número de eventos limitados de desencadenador o acciones de flujo de trabajo|Sin dimensiones|
|RunFailurePercentage|Porcentaje de errores de ejecución|Percent|Total|Porcentaje de ejecuciones de flujo de trabajo con errores.|Sin dimensiones|
|ActionsStarted|Acciones iniciadas |Recuento|Total|Número de acciones de flujo de trabajo iniciadas|Sin dimensiones|
|ActionsCompleted|Acciones completadas |Recuento|Total|Número de acciones de flujo de trabajo completadas|Sin dimensiones|
|ActionsSucceeded|Acciones correctas |Recuento|Total|Número de acciones de flujo de trabajo correctas|Sin dimensiones|
|ActionsFailed|Acciones con errores|Recuento|Total|Número de acciones de flujo de trabajo con errores|Sin dimensiones|
|ActionsSkipped|Acciones omitidas |Recuento|Total|Número de acciones de flujo de trabajo omitidas|Sin dimensiones|
|ActionLatency|Latencia de acciones |Segundos|Media|Latencia de acciones de flujo de trabajo completadas|Sin dimensiones|
|ActionSuccessLatency|Latencia de acciones correctas |Segundos|Media|Latencia de acciones de flujo de trabajo correctas|Sin dimensiones|
|ActionThrottledEvents|Eventos limitados de acciones|Recuento|Total|Número de eventos limitados de acciones de flujo de trabajo|Sin dimensiones|
|TriggersStarted|Desencadenadores iniciados |Recuento|Total|Número de desencadenadores de flujo de trabajo iniciados|Sin dimensiones|
|TriggersCompleted|Desencadenadores completados |Recuento|Total|Número de desencadenadores de flujo de trabajo completados|Sin dimensiones|
|TriggersSucceeded|Desencadenadores correctos |Recuento|Total|Número de desencadenadores de flujo de trabajo correctos|Sin dimensiones|
|TriggersFailed|Desencadenadores con errores |Recuento|Total|Número de desencadenadores de flujo de trabajo con errores|Sin dimensiones|
|TriggersSkipped|Desencadenadores omitidos|Recuento|Total|Número de desencadenadores de flujo de trabajo omitidos|Sin dimensiones|
|TriggersFired|Desencadenadores activados |Recuento|Total|Número de desencadenadores de flujo de trabajo activados|Sin dimensiones|
|TriggerLatency|Latencia de desencadenador |Segundos|Media|Latencia de desencadenadores de flujo de trabajo completados|Sin dimensiones|
|TriggerFireLatency|Latencia de desencadenador activado |Segundos|Media|Latencia de desencadenadores de flujo de trabajo activados|Sin dimensiones|
|TriggerSuccessLatency|Latencia de desencadenadores correctos |Segundos|Media|Latencia de desencadenadores de flujo de trabajo correctos|Sin dimensiones|
|TriggerThrottledEvents|Eventos limitados de desencadenadores|Recuento|Total|Número de eventos limitados de desencadenador de flujo de trabajo|Sin dimensiones|
|BillableActionExecutions|Ejecuciones de acciones facturables|Recuento|Total|Número de ejecuciones de acciones de flujo de trabajo que se facturan.|Sin dimensiones|
|BillableTriggerExecutions|Ejecuciones del desencadenador facturable|Recuento|Total|Número de ejecuciones del desencadenador de flujo de trabajo que se factura.|Sin dimensiones|
|TotalBillableExecutions|Total de ejecuciones facturables|Recuento|Total|Número de ejecuciones de flujo de trabajo que se factura.|Sin dimensiones|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|BytesSentRate|Bytes enviados|Recuento|Total|Número de bytes de la interfaz de red enviados|Sin dimensiones|
|BytesReceivedRate|Bytes recibidos|Recuento|Total|Número de bytes de la interfaz de red recibidos|Sin dimensiones|
|PacketsSentRate|Paquetes enviados|Recuento|Total|Número de paquetes de la interfaz de red enviados|Sin dimensiones|
|PacketsReceivedRate|Paquetes recibidos|Recuento|Total|Número de paquetes de la interfaz de red recibidos|Sin dimensiones|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidad de VIP|Recuento|Media|Disponibilidad de extremos de VIP, en función de los resultados de sondeo|VipAddress, VipPort|
|DipAvailability|Disponibilidad de DIP|Recuento|Media|Disponibilidad de extremos de DIP, en función de los resultados de sondeo|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Recuento de bytes|Recuento|Total|Número total de bytes transmitidos en un período|VipAddress, VipPort, Direction|
|PacketCount|Recuento de paquetes|Recuento|Total|Número total de paquetes transmitidos en un período|VipAddress, VipPort, Direction|
|SYNCount|Recuento de SYN|Recuento|Total|Número total de paquetes SYN transmitidos en un período|VipAddress, VipPort, Direction|
|SnatConnectionCount|Recuento de conexiones SNAT|Recuento|Total|Número total de conexiones SNAT nuevas creadas en un período|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|PacketsInDDoS|Paquetes entrantes de DDoS|CountPerSecond|Máxima|Paquetes entrantes de DDoS|Sin dimensiones|
|PacketsDroppedDDoS|Paquetes entrantes de DDoS descartados|CountPerSecond|Máxima|Paquetes entrantes de DDoS descartados|Sin dimensiones|
|PacketsForwardedDDoS|Paquetes entrantes de DDoS reenviados|CountPerSecond|Máxima|Paquetes entrantes de DDoS reenviados|Sin dimensiones|
|TCPPacketsInDDoS|Paquetes TCP entrantes de DDoS|CountPerSecond|Máxima|Paquetes TCP entrantes de DDoS|Sin dimensiones|
|TCPPacketsDroppedDDoS|Paquetes TCP entrantes de DDoS descartados|CountPerSecond|Máxima|Paquetes TCP entrantes de DDoS descartados|Sin dimensiones|
|TCPPacketsForwardedDDoS|Paquetes TCP entrantes de DDoS reenviados|CountPerSecond|Máxima|Paquetes TCP entrantes de DDoS reenviados|Sin dimensiones|
|UDPPacketsInDDoS|Paquetes UDP entrantes de DDoS|CountPerSecond|Máxima|Paquetes UDP entrantes de DDoS|Sin dimensiones|
|UDPPacketsDroppedDDoS|Paquetes UDP entrantes de DDoS descartados|CountPerSecond|Máxima|Paquetes UDP entrantes de DDoS descartados|Sin dimensiones|
|UDPPacketsForwardedDDoS|Paquetes UDP entrantes de DDoS reenviados|CountPerSecond|Máxima|Paquetes UDP entrantes de DDoS reenviados|Sin dimensiones|
|BytesInDDoS|Bytes de entrada de DDoS|BytesPerSecond|Máxima|Bytes de entrada de DDoS|Sin dimensiones|
|BytesDroppedDDoS|Bytes de entrada de DDoS descartados|BytesPerSecond|Máxima|Bytes de entrada de DDoS descartados|Sin dimensiones|
|BytesForwardedDDoS|Bytes de entrada de DDoS reenviados|BytesPerSecond|Máxima|Bytes de entrada de DDoS reenviados|Sin dimensiones|
|TCPBytesInDDoS|Bytes de entrada TCP de DDoS|BytesPerSecond|Máxima|Bytes de entrada TCP de DDoS|Sin dimensiones|
|TCPBytesDroppedDDoS|Bytes de entrada TCP de DDoS descartados|BytesPerSecond|Máxima|Bytes de entrada TCP de DDoS descartados|Sin dimensiones|
|TCPBytesForwardedDDoS|Bytes de entrada TCP de DDoS reenviados|BytesPerSecond|Máxima|Bytes de entrada TCP de DDoS reenviados|Sin dimensiones|
|UDPBytesInDDoS|Bytes de entrada UDP de DDoS|BytesPerSecond|Máxima|Bytes de entrada UDP de DDoS|Sin dimensiones|
|UDPBytesDroppedDDoS|Bytes de entrada UDP de DDoS descartados|BytesPerSecond|Máxima|Bytes de entrada UDP de DDoS descartados|Sin dimensiones|
|UDPBytesDroppedDDoS|Bytes de entrada UDP de DDoS reenviados|BytesPerSecond|Máxima|Bytes de entrada UDP de DDoS reenviados|Sin dimensiones|
|IfUnderDDoSAttack|Bajo ataque de DDoS o no|Recuento|Máxima|Bajo ataque de DDoS o no|Sin dimensiones|
|DDoSTriggerTCPPackets|Paquetes entrantes TCP para desencadenar la mitigación de DDoS|CountPerSecond|Máxima|Paquetes entrantes TCP para desencadenar la mitigación de DDoS|Sin dimensiones|
|DDoSTriggerUDPPackets|Paquetes entrantes UDP para desencadenar la mitigación de DDoS|CountPerSecond|Máxima|Paquetes entrantes UDP para desencadenar la mitigación de DDoS|Sin dimensiones|
|DDoSTriggerSYNPackets|Paquetes entrantes SYN para desencadenar la mitigación de DDoS|CountPerSecond|Máxima|Paquetes entrantes SYN para desencadenar la mitigación de DDoS|Sin dimensiones|
|VipAvailability|Disponibilidad|Recuento|Media|Media de disponibilidad de dirección IP dentro del período|Port|
|ByteCount|Recuento de bytes|Recuento|Total|Número total de bytes transmitidos en un período|Port, Direction|
|PacketCount|Recuento de paquetes|Recuento|Total|Número total de paquetes transmitidos en un período|Port, Direction|
|SynCount|Recuento de SYN|Recuento|Total|Número total de paquetes SYN transmitidos en un período|Port, Direction|

## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|PacketsInDroppedVMProtection|Paquetes entrantes descartados para la protección de máquinas virtuales|CountPerSecond|Media|Paquetes entrantes descartados para la protección de máquinas virtuales|Sin dimensiones|
|PacketsInDroppedVMProtection|Paquetes salientes descartados para la protección de máquinas virtuales|CountPerSecond|Media|Paquetes salientes descartados para la protección de máquinas virtuales|Sin dimensiones|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Throughput|Throughput|BytesPerSecond|Total|Número de bytes por segundo que ha ofrecido Application Gateway|Sin dimensiones|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Ancho de banda de túnel|BytesPerSecond|Media|Media de ancho de banda de un túnel en bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de salida de túnel|Bytes|Total|Bytes de salida de un túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de salida de túnel|Bytes|Total|Bytes de entrada de un túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Paquetes de salida de túnel|Recuento|Total|Recuento de paquetes salientes de un túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Paquetes de entrada de túnel|Recuento|Total|Recuento de paquetes entrantes de un túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de salida|Recuento|Total|Recuento de paquetes de salida con error de coincidencia del selector de tráfico de un túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Colocación de paquetes con error de coincidencia del selector de tráfico de túnel de entrada|Recuento|Total|Recuento de paquetes entrantes con error de coincidencia del selector de tráfico de un túnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Media|Bits que ingresan a Azure por segundo|Sin dimensiones|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Media|Bits que egresan de Azure por segundo|Sin dimensiones|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas por punto de conexión devueltas|Recuento|Total|Número de veces que se devolvió un punto de conexión de Traffic Manager en el período dado|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Estado de punto de conexión por punto de conexión|Recuento|Máxima|1 si el estado de sondeo de un punto de conexión es “Habilitado”, 0 en caso contrario.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|registration.all|Operaciones de registro|Recuento|Total|El recuento de todas las operaciones de registro correctas (creaciones, actualizaciones, consultas y eliminaciones). |Sin dimensiones|
|registration.create|Operaciones de creación de registros|Recuento|Total|El recuento de todas las creaciones de registros correctas.|Sin dimensiones|
|registration.update|Operaciones de actualización de registros|Recuento|Total|El recuento de todas las actualizaciones de registros correctas.|Sin dimensiones|
|registration.get|Operaciones de lectura de registros|Recuento|Total|El recuento de todas las consultas de registros correctas.|Sin dimensiones|
|registration.delete|Operaciones de eliminación de registros|Recuento|Total|El recuento de todas las eliminaciones de registros correctas.|Sin dimensiones|
|incoming|Mensajes entrantes|Recuento|Total|El recuento de todas las llamadas a la API de envío. |Sin dimensiones|
|incoming.scheduled|Notificaciones push programadas enviadas|Recuento|Total|Notificaciones push programadas canceladas|Sin dimensiones|
|incoming.scheduled.cancel|Notificaciones push programadas canceladas|Recuento|Total|Notificaciones push programadas canceladas|Sin dimensiones|
|scheduled.pending|Notificaciones programadas pendientes|Recuento|Total|Notificaciones programadas pendientes|Sin dimensiones|
|installation.all|Operaciones de administración de instalaciones|Recuento|Total|Operaciones de administración de instalaciones|Sin dimensiones|
|installation.get|Obtener operaciones de instalación|Recuento|Total|Obtener operaciones de instalación|Sin dimensiones|
|installation.upsert|Crear o actualizar operaciones de instalación|Recuento|Total|Crear o actualizar operaciones de instalación|Sin dimensiones|
|installation.patch|Aplicar revisión a operaciones de instalación|Recuento|Total|Aplicar revisión a operaciones de instalación|Sin dimensiones|
|installation.delete|Eliminar operaciones de instalación|Recuento|Total|Eliminar operaciones de instalación|Sin dimensiones|
|outgoing.allpns.success|Notificaciones correctas|Recuento|Total|El recuento de todas las notificaciones correctas.|Sin dimensiones|
|outgoing.allpns.invalidpayload|Errores de carga útil|Recuento|Total|El recuento de las inserciones que provocaron un error porque el PNS devolvió un error de carga útil incorrecta.|Sin dimensiones|
|outgoing.allpns.pnserror|Errores del sistema de notificación externo|Recuento|Total|El recuento de inserciones que provocaron un error porque se produjo un problema al comunicarse con el PNS (excluye los problemas de autenticación).|Sin dimensiones|
|outgoing.allpns.channelerror|Errores de canal|Recuento|Total|El recuento de inserciones que provocaron un error porque el canal no era válido no está asociado a la aplicación correcta limitada o expirada.|Sin dimensiones|
|outgoing.allpns.badorexpiredchannel|Errores de canal incorrecto o expirado|Recuento|Total|El recuento de inserciones que provocaron un error porque el canal/token/registrationId del registro ha expirado o no es válido.|Sin dimensiones|
|outgoing.wns.success|Notificaciones de WNS correctas|Recuento|Total|El recuento de todas las notificaciones correctas.|Sin dimensiones|
|outgoing.wns.invalidcredentials|Errores de autorización de WNS (credenciales no válidas)|Recuento|Total|El recuento de inserciones que provocaron un error porque el PNS no aceptó las credenciales proporcionadas o las credenciales están bloqueadas. (Windows Live no reconoce las credenciales).|Sin dimensiones|
|outgoing.wns.badchannel|Error de canal incorrecto de WNS|Recuento|Total|El recuento de inserciones que provocaron un error porque no se reconoció ChannelURI en el registro (estado de WNS: 404 No encontrado).|Sin dimensiones|
|outgoing.wns.expiredchannel|Error de canal expirado de WNS|Recuento|Total|El recuento de inserciones que provocaron un error porque ChannelURI expiró (estado de WNS: 410 Ya no existe).|Sin dimensiones|
|outgoing.wns.throttled|Notificaciones limitadas de WNS|Recuento|Total|El recuento de inserciones que provocaron un error porque WNS limita esta aplicación (estado de WNS: 406 No aceptable).|Sin dimensiones|
|outgoing.wns.tokenproviderunreachable|Errores de autorización de WNS (inaccesible)|Recuento|Total|No se puede acceder a Windows Live.|Sin dimensiones|
|outgoing.wns.invalidtoken|Errores de autorización de WNS (token no válido)|Recuento|Total|El token que se proporciona a WNS no es válido (estado de WNS: 401 No autorizado).|Sin dimensiones|
|outgoing.wns.wrongtoken|Errores de autorización de WNS (token erróneo)|Recuento|Total|El token que se proporciona a WNS es válido, pero para otra aplicación (estado de WNS: 403 Prohibido). Esto puede ocurrir si la instancia de ChannelURI en el registro está asociada a otra aplicación. Compruebe que la aplicación cliente está asociada a la misma aplicación cuyas credenciales están en el centro de notificaciones.|Sin dimensiones|
|outgoing.wns.invalidnotificationformat|Formato de notificación no válido de WNS|Recuento|Total|El formato de la notificación no es válido (estado de WNS: 400). Tenga en cuenta que WNS no rechaza todas las cargas útiles no válidas.|Sin dimensiones|
|outgoing.wns.invalidnotificationsize|Error de tamaño de notificación no válido de WNS|Recuento|Total|La carga útil de la notificación es demasiado grande (estado de WNS: 413).|Sin dimensiones|
|outgoing.wns.channelthrottled|Canal de WNS limitado|Recuento|Total|La notificación se descartó porque la instancia de ChannelURI del registro está limitada (encabezado de respuesta WNS: X-WNS-NotificationStatus:channelThrottled).|Sin dimensiones|
|outgoing.wns.channeldisconnected|Canal de WNS desconectado|Recuento|Total|La notificación se descartó porque la instancia de ChannelURI del registro está limitada (encabezado de respuesta WNS: X-WNS-DeviceConnectionStatus: desconectado).|Sin dimensiones|
|outgoing.wns.dropped|Notificaciones descartadas de WNS|Recuento|Total|La notificación se descartó porque la instancia de ChannelURI del registro está limitada (X-WNS-NotificationStatus: descartado pero no X-WNS-DeviceConnectionStatus: desconectado).|Sin dimensiones|
|outgoing.wns.pnserror|Errores de WNS|Recuento|Total|La notificación no se entregó debido a errores de comunicación con WNS.|Sin dimensiones|
|outgoing.wns.authenticationerror|Errores de autenticación de WNS|Recuento|Total|La notificación no se entregó debido a errores de comunicación con Windows Live, credenciales no válidas o token erróneo.|Sin dimensiones|
|outgoing.apns.success|Notificaciones de APNS correctas|Recuento|Total|El recuento de todas las notificaciones correctas.|Sin dimensiones|
|outgoing.apns.invalidcredentials|Errores de autorización de APNS|Recuento|Total|El recuento de inserciones que provocaron un error porque el PNS no aceptó las credenciales proporcionadas o las credenciales están bloqueadas.|Sin dimensiones|
|outgoing.apns.badchannel|Error de canal incorrecto de APNS|Recuento|Total|El recuento de inserciones que provocaron un error porque el token no es válido (código de estado de APNS: 8).|Sin dimensiones|
|outgoing.apns.expiredchannel|Error de canal expirado de APNS|Recuento|Total|El recuento de tokens invalidados por el canal de comentarios de APNS.|Sin dimensiones|
|outgoing.apns.invalidnotificationsize|Error de tamaño de notificación no válido de APNS|Recuento|Total|El recuento de inserciones que provocaron un error porque la carga útil era demasiado grande (código de estado de APNS: 7).|Sin dimensiones|
|outgoing.apns.pnserror|Errores de APNS|Recuento|Total|El recuento de inserciones que provocaron un error debido a errores de comunicación con APNS.|Sin dimensiones|
|outgoing.gcm.success|Notificaciones de GCM correctas|Recuento|Total|El recuento de todas las notificaciones correctas.|Sin dimensiones|
|outgoing.gcm.invalidcredentials|Errores de autorización de GCM (credenciales no válidas)|Recuento|Total|El recuento de inserciones que provocaron un error porque el PNS no aceptó las credenciales proporcionadas o las credenciales están bloqueadas.|Sin dimensiones|
|outgoing.gcm.badchannel|Error de canal incorrecto de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque la instancia de registrationId del registro no se ha reconocido (resultado de GCM: registro no válido).|Sin dimensiones|
|outgoing.gcm.expiredchannel|Error de canal expirado de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque la instancia de registrationId del registro ha expirado (resultado de GCM: NotRegistered).|Sin dimensiones|
|outgoing.gcm.throttled|Notificaciones limitadas de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque GCM limitó esta aplicación (código de estado de GCM: 501-599 o resultado: no disponible).|Sin dimensiones|
|outgoing.gcm.invalidnotificationformat|Formato de notificación no válido de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque la carga útil no tenía el formato correcto (resultado de GCM: InvalidDataKey o InvalidDataKey o InvalidTtl).|Sin dimensiones|
|outgoing.gcm.invalidnotificationsize|Error de tamaño de notificación no válido de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque la carga útil era demasiado grande (resultado de GCM: MessageTooBig).|Sin dimensiones|
|outgoing.gcm.wrongchannel|Error de canal incorrecto de GCM|Recuento|Total|El recuento de inserciones que provocaron un error porque la instancia de registrationId del registro no está asociado a la aplicación actual (resultado de GCM: InvalidPackageName).|Sin dimensiones|
|outgoing.gcm.pnserror|Errores de GCM|Recuento|Total|El recuento de inserciones que provocaron un error debido a errores de comunicación con GCM.|Sin dimensiones|
|outgoing.gcm.authenticationerror|Errores de autenticación de GCM|Recuento|Total|El recuento de inserciones que provocaron un error debido a que el PNS no aceptó las credenciales proporcionadas, las credenciales están bloqueadas o el Id. del remitente no está configurado correctamente en la aplicación (resultado de GCM: MismatchedSenderId).|Sin dimensiones|
|outgoing.mpns.success|Notificaciones de MPNS correctas|Recuento|Total|El recuento de todas las notificaciones correctas.|Sin dimensiones|
|outgoing.mpns.invalidcredentials|Credenciales no válidas de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error porque el PNS no aceptó las credenciales proporcionadas o las credenciales están bloqueadas.|Sin dimensiones|
|outgoing.mpns.badchannel|Error de canal incorrecto de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error porque no se reconoció ChannelURI en el registro (estado de MPNS: 404 No encontrado).|Sin dimensiones|
|outgoing.mpns.throttled|Notificaciones limitadas de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error porque MPNS limita esta aplicación (MPNS de WNS: 406 No aceptable).|Sin dimensiones|
|outgoing.mpns.invalidnotificationformat|Formato de notificación no válido de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error porque la carga útil de la notificación era demasiado grande.|Sin dimensiones|
|outgoing.mpns.channeldisconnected|Canal de MPNS desconectado|Recuento|Total|El recuento de inserciones que provocaron un error porque se desconectó la instancia ChannelURI en el registro (estado de MPNS: 412 No encontrado).|Sin dimensiones|
|outgoing.mpns.dropped|Notificaciones descartadas de MPNS|Recuento|Total|El recuento de inserciones que MPNS ha descartado (encabezado de respuesta MPNS: X-NotificationStatus: QueueFull o Suppressed).|Sin dimensiones|
|outgoing.mpns.pnserror|Errores de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error debido a errores de comunicación con MPNS.|Sin dimensiones|
|outgoing.mpns.authenticationerror|Errores de autenticación de MPNS|Recuento|Total|El recuento de inserciones que provocaron un error porque el PNS no aceptó las credenciales proporcionadas o las credenciales están bloqueadas.|Sin dimensiones|
|notificationhub.pushes|Todas las notificaciones salientes|Recuento|Total|Todas las notificaciones salientes del Centro de notificaciones|Sin dimensiones|
|incoming.all.requests|Todas las solicitudes entrantes|Recuento|Total|Solicitudes entrantes totales de un centro de notificaciones|Sin dimensiones|
|incoming.all.failedrequests|Todas las solicitudes entrantes con error|Recuento|Total|Solicitudes entrantes totales con error de un centro de notificaciones|Sin dimensiones|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Recuento|Total|Valor correcto de ListenerConnections de Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Recuento|Total|ClientError en ListenerConnections para Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Recuento|Total|ServerError en ListenerConnections para Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Recuento|Total|Valor correcto de SenderConnections para Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Recuento|Total|ClientError en SenderConnections para Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Recuento|Total|ServerError en SenderConnections para Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Recuento|Total|Valor total de ListenerConnections para Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Recuento|Total|Solicitudes totales de SenderConnections para Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Recuento|Total|Valor total de ActiveConnections para Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Recuento|Total|Valor total de ActiveListeners para Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Recuento|Total|Valor total de BytesTransferred para Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Recuento|Total|Valor total de ListenerDisconnects para Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Recuento|Total|Valor total de SenderDisconnects para Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|SearchLatency|Latencia de búsqueda|Segundos|Media|Promedio de latencia de búsqueda para el servicio de búsqueda|Sin dimensiones|
|SearchQueriesPerSecond|Consultas de búsqueda por segundo|CountPerSecond|Media|Consultas de búsqueda por segundo para el servicio de búsqueda|Sin dimensiones|
|ThrottledSearchQueriesPercentage|Porcentaje de consultas de búsqueda limitadas|Percent|Media|Porcentaje de consultas de búsqueda limitadas para el servicio de búsqueda|Sin dimensiones|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitudes correctas (versión preliminar)|Recuento|Total|Número total de solicitudes correctas para un espacio de nombres (versión preliminar)|EntityName|
|ServerErrors|Errores del servidor. (versión preliminar)|Recuento|Total|Errores del servidor para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|UserErrors|Errores de usuario. (versión preliminar)|Recuento|Total|Errores de usuario para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|ThrottledRequests|Solicitudes limitadas. (versión preliminar)|Recuento|Total|Solicitudes limitadas para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|IncomingRequests|Solicitudes entrantes (versión preliminar)|Recuento|Total|Solicitudes entrantes para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|IncomingMessages|Mensajes entrantes (versión preliminar)|Recuento|Total|Mensajes entrantes para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|OutgoingMessages|Mensajes salientes (versión preliminar)|Recuento|Total|Mensajes salientes para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|ActiveConnections|ActiveConnections (versión preliminar)|Recuento|Total|Total de conexiones activas para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|ConnectionsOpened|Conexiones abiertas. (versión preliminar)|Recuento|Total|Conexiones abiertas para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|ConnectionsClosed|Conexiones cerradas. (versión preliminar)|Recuento|Total|Conexiones cerradas para Microsoft.ServiceBus. (versión preliminar)|EntityName|
|CPUXNS|Uso de CPU por espacio de nombres|Percent|Máxima|Métrica de uso de CPU de espacio de nombres premium de Service Bus|Sin dimensiones|
|WSXNS|Uso de tamaño de memoria por espacio de nombres|Percent|Máxima|Métrica de uso de memoria de espacio de nombres premium de Service Bus|Sin dimensiones|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Sin dimensiones|
|physical_data_read_percent|Porcentaje de E/S de datos|Percent|Media|Porcentaje de E/S de datos|Sin dimensiones|
|log_write_percent|Porcentaje de E/S de registro|Percent|Media|Porcentaje de E/S de registro|Sin dimensiones|
|dtu_consumption_percent|Porcentaje de DTU|Percent|Media|Porcentaje de DTU|Sin dimensiones|
|storage|Tamaño total de base de datos|Bytes|Máxima|Tamaño total de base de datos|Sin dimensiones|
|connection_successful|Conexiones correctas|Recuento|Total|Conexiones correctas|Sin dimensiones|
|connection_failed|Conexiones con errores|Recuento|Total|Conexiones con errores|Sin dimensiones|
|blocked_by_firewall|Bloqueado por el firewall|Recuento|Total|Bloqueado por el firewall|Sin dimensiones|
|deadlock|Interbloqueos|Recuento|Total|Interbloqueos|Sin dimensiones|
|storage_percent|Porcentaje de tamaño de base de datos|Percent|Máxima|Porcentaje de tamaño de base de datos|Sin dimensiones|
|xtp_storage_percent|Porcentaje de almacenamiento de OLTP en memoria|Percent|Media|Porcentaje de almacenamiento de OLTP en memoria|Sin dimensiones|
|workers_percent|Porcentaje de trabajos|Percent|Media|Porcentaje de trabajos|Sin dimensiones|
|sessions_percent|Porcentaje de sesiones|Percent|Media|Porcentaje de sesiones|Sin dimensiones|
|dtu_limit|Límite de DTU|Recuento|Media|Límite de DTU|Sin dimensiones|
|dtu_used|DTU utilizada|Recuento|Media|DTU utilizada|Sin dimensiones|
|dwu_limit|Límite de DWU|Recuento|Máxima|Límite de DWU|Sin dimensiones|
|dwu_consumption_percent|Porcentaje de DWU|Percent|Máxima|Porcentaje de DWU|Sin dimensiones|
|dwu_used|DWU utilizada|Recuento|Máxima|DWU utilizada|Sin dimensiones|
|dw_cpu_percent|Porcentaje de CPU de nivel de nodo de almacenamiento de datos|Percent|Media|Porcentaje de CPU de nivel de nodo de almacenamiento de datos|dw_logical_node_id|
|dw_physical_data_read_percent|Porcentaje de E/S de datos de nivel de nodo de almacenamiento de datos|Percent|Media|Porcentaje de E/S de datos de nivel de nodo de almacenamiento de datos|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Sin dimensiones|
|database_cpu_percent|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|DatabaseResourceId|
|physical_data_read_percent|Porcentaje de E/S de datos|Percent|Media|Porcentaje de E/S de datos|Sin dimensiones|
|database_physical_data_read_percent|Porcentaje de E/S de datos|Percent|Media|Porcentaje de E/S de datos|DatabaseResourceId|
|log_write_percent|Porcentaje de E/S de registro|Percent|Media|Porcentaje de E/S de registro|Sin dimensiones|
|database_log_write_percent|Porcentaje de E/S de registro|Percent|Media|Porcentaje de E/S de registro|DatabaseResourceId|
|dtu_consumption_percent|Porcentaje de DTU|Percent|Media|Porcentaje de DTU|Sin dimensiones|
|database_dtu_consumption_percent|Porcentaje de DTU|Percent|Media|Porcentaje de DTU|DatabaseResourceId|
|storage_percent|Porcentaje de almacenamiento|Percent|Media|Porcentaje de almacenamiento|Sin dimensiones|
|workers_percent|Porcentaje de trabajos|Percent|Media|Porcentaje de trabajos|Sin dimensiones|
|database_workers_percent|Porcentaje de trabajos|Percent|Media|Porcentaje de trabajos|DatabaseResourceId|
|sessions_percent|Porcentaje de sesiones|Percent|Media|Porcentaje de sesiones|Sin dimensiones|
|database_sessions_percent|Porcentaje de sesiones|Percent|Media|Porcentaje de sesiones|DatabaseResourceId|
|eDTU_limit|Límite de eDTU|Recuento|Media|Límite de eDTU|Sin dimensiones|
|storage_limit|Límite de almacenamiento|Bytes|Media|Límite de almacenamiento|Sin dimensiones|
|eDTU_used|eDTU utilizada|Recuento|Media|eDTU utilizada|Sin dimensiones|
|storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|Sin dimensiones|
|database_storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|DatabaseResourceId|
|xtp_storage_percent|Porcentaje de almacenamiento de OLTP en memoria|Percent|Media|Porcentaje de almacenamiento de OLTP en memoria|Sin dimensiones|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|dtu_consumption_percent|Porcentaje de DTU|Percent|Media|Porcentaje de DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Porcentaje de DTU|Percent|Media|Porcentaje de DTU|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|ElasticPoolResourceId|
|database_storage_used|Almacenamiento utilizado|Bytes|Media|Almacenamiento utilizado|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|UsedCapacity|Capacidad usada|Bytes|Media|Capacidad usada de la cuenta|Sin dimensiones|
|Transacciones|Transacciones|Recuento|Total|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuesta.|ResponseType, GeoType, ApiName|
|Entrada|Entrada|Bytes|Total|La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure.|GeoType, ApiName|
|Salida|Salida|Bytes|Total|La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables.|GeoType, ApiName|
|SuccessServerLatency|Latencia del servidor correcta|Milisegundos|Media|La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latencia de E2E correcta|Milisegundos|Media|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|GeoType, ApiName|
|Disponibilidad|Disponibilidad|Percent|Media|El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el valor deTotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|BlobCapacity|Capacidad de Blob|Bytes|Media|La cantidad de almacenamiento que ha utilizado el Blob service de la cuenta de almacenamiento, en bytes.|BlobType|
|BlobCount|Recuento de blobs|Recuento|Media|El número de blobs en el Blob service de la cuenta de almacenamiento.|BlobType|
|ContainerCount|Recuento de contenedores de blobs|Recuento|Media|El número de contenedores en el Blob service de la cuenta de almacenamiento.|Sin dimensiones|
|Transacciones|Transacciones|Recuento|Total|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuesta.|ResponseType, GeoType, ApiName|
|Entrada|Entrada|Bytes|Total|La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure.|GeoType, ApiName|
|Salida|Salida|Bytes|Total|La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables.|GeoType, ApiName|
|SuccessServerLatency|Latencia del servidor correcta|Milisegundos|Media|La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latencia de E2E correcta|Milisegundos|Media|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|GeoType, ApiName|
|Disponibilidad|Disponibilidad|Percent|Media|El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el valor deTotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|TableCapacity|Capacidad de Table|Bytes|Media|La cantidad de almacenamiento que ha utilizado el servicio Table de la cuenta de almacenamiento, en bytes.|Sin dimensiones|
|TableCount|Recuento de tablas|Recuento|Media|El número de tablas en el servicio Table de la cuenta de almacenamiento.|Sin dimensiones|
|TableEntityCount|Recuento de entidades de Table|Recuento|Media|El número de entidades de tabla en el servicio Table de la cuenta de almacenamiento.|Sin dimensiones|
|Transacciones|Transacciones|Recuento|Total|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuesta.|ResponseType, GeoType, ApiName|
|Entrada|Entrada|Bytes|Total|La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure.|GeoType, ApiName|
|Salida|Salida|Bytes|Total|La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables.|GeoType, ApiName|
|SuccessServerLatency|Latencia del servidor correcta|Milisegundos|Media|La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latencia de E2E correcta|Milisegundos|Media|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|GeoType, ApiName|
|Disponibilidad|Disponibilidad|Percent|Media|El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el valor deTotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|QueueCapacity|Capacidad de Queue|Bytes|Media|La cantidad de almacenamiento que ha utilizado el Queue service de la cuenta de almacenamiento, en bytes.|Sin dimensiones|
|QueueCount|Recuento de colas|Recuento|Media|El número de colas en el Queue service de la cuenta de almacenamiento.|Sin dimensiones|
|QueueMessageCount|Recuento de mensajes de Queue|Recuento|Media|El número aproximado de mensajes de cola en el Queue service de la cuenta de almacenamiento.|Sin dimensiones|
|Transacciones|Transacciones|Recuento|Total|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuesta.|ResponseType, GeoType, ApiName|
|Entrada|Entrada|Bytes|Total|La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure.|GeoType, ApiName|
|Salida|Salida|Bytes|Total|La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables.|GeoType, ApiName|
|SuccessServerLatency|Latencia del servidor correcta|Milisegundos|Media|La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latencia de E2E correcta|Milisegundos|Media|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|GeoType, ApiName|
|Disponibilidad|Disponibilidad|Percent|Media|El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el valor deTotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|FileCapacity|Capacidad de File|Bytes|Media|La cantidad de almacenamiento que ha utilizado el servicio File de la cuenta de almacenamiento, en bytes.|Sin dimensiones|
|FileCount|Recuento de archivos|Recuento|Media|El número de archivos en el servicio File de la cuenta de almacenamiento.|Sin dimensiones|
|FileShareCount|Recuento de recursos compartidos de archivos|Recuento|Media|El número de recursos compartidos de archivos en el servicio File de la cuenta de almacenamiento.|Sin dimensiones|
|Transacciones|Transacciones|Recuento|Total|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. Use la dimensión ResponseType para el número de diferentes tipos de respuesta.|ResponseType, GeoType, ApiName|
|Entrada|Entrada|Bytes|Total|La cantidad de datos de entrada, en bytes. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure.|GeoType, ApiName|
|Salida|Salida|Bytes|Total|La cantidad de datos de salida, en bytes. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables.|GeoType, ApiName|
|SuccessServerLatency|Latencia del servidor correcta|Milisegundos|Media|La latencia media utilizada por Azure Storage para procesar una solicitud correcta, en milisegundos. Este valor no incluye la latencia de red especificada en AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latencia de E2E correcta|Milisegundos|Media|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada, en milisegundos. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|GeoType, ApiName|
|Disponibilidad|Disponibilidad|Percent|Media|El porcentaje de disponibilidad para el servicio de almacenamiento o la operación de API especificada. Para calcular la disponibilidad hay que tomar el valor deTotalBillableRequests y dividirlo por el número de solicitudes aplicables, incluidas aquellas que generaron errores inesperados. Todos los errores inesperados reducen la disponibilidad del servicio de almacenamiento o de la operación de API especificada.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|ResourceUtilization|SU % uso|Percent|Máxima|SU % uso|Sin dimensiones|
|InputEvents|Eventos de entrada|Recuento|Total|Eventos de entrada|Sin dimensiones|
|InputEventBytes|Bytes del evento de entrada|Bytes|Total|Bytes del evento de entrada|Sin dimensiones|
|LateInputEvents|Eventos de entrada retrasada|Recuento|Total|Eventos de entrada retrasada|Sin dimensiones|
|OutputEvents|Eventos de salida|Recuento|Total|Eventos de salida|Sin dimensiones|
|ConversionErrors|Errores de conversión de datos|Recuento|Total|Errores de conversión de datos|Sin dimensiones|
|Errors|Errores de tiempo de ejecución|Recuento|Total|Errores de tiempo de ejecución|Sin dimensiones|
|DroppedOrAdjustedEvents|Eventos de fuera de orden|Recuento|Total|Eventos de fuera de orden|Sin dimensiones|
|AMLCalloutRequests|Solicitudes de función|Recuento|Total|Solicitudes de función|Sin dimensiones|
|AMLCalloutFailedRequests|Solicitudes de función con errores|Recuento|Total|Solicitudes de función con errores|Sin dimensiones|
|AMLCalloutInputEvents|Eventos de función|Recuento|Total|Eventos de función|Sin dimensiones|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|CpuPercentage|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Instance|
|MemoryPercentage|Porcentaje de memoria|Percent|Media|Porcentaje de memoria|Instance|
|DiskQueueLength|Longitud de la cola de disco|Recuento|Media|Longitud de la cola de disco|Instance|
|HttpQueueLength|Longitud de la cola HTTP|Recuento|Media|Longitud de la cola HTTP|Instance|
|BytesReceived|Entrada de datos|Bytes|Total|Entrada de datos|Instance|
|BytesSent|Salida de datos|Bytes|Total|Salida de datos|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (se excluye Functions)

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|CpuTime|Tiempo de CPU|Segundos|Total|Tiempo de CPU|Instance|
|Requests|Requests|Recuento|Total|Requests|Instance|
|BytesReceived|Entrada de datos|Bytes|Total|Entrada de datos|Instance|
|BytesSent|Salida de datos|Bytes|Total|Salida de datos|Instance|
|Http101|Http 101|Recuento|Total|Http 101|Instance|
|Http2xx|Http 2xx|Recuento|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Recuento|Total|Http 3xx|Instance|
|Http401|Http 401|Recuento|Total|Http 401|Instance|
|Http403|Http 403|Recuento|Total|Http 403|Instance|
|Http404|Http 404|Recuento|Total|Http 404|Instance|
|Http406|Http 406|Recuento|Total|Http 406|Instance|
|Http4xx|Http 4xx|Recuento|Total|Http 4xx|Instance|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|Instance|
|MemoryWorkingSet|Espacio de trabajo de memoria|Bytes|Media|Espacio de trabajo de memoria|Instance|
|AverageMemoryWorkingSet|Espacio de trabajo de memoria promedio|Bytes|Media|Espacio de trabajo de memoria promedio|Instance|
|AverageResponseTime|Tiempo de respuesta promedio|Segundos|Media|Tiempo de respuesta promedio|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|BytesReceived|Entrada de datos|Bytes|Total|Entrada de datos|Instance|
|BytesSent|Salida de datos|Bytes|Total|Salida de datos|Instance|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|Instance|
|MemoryWorkingSet|Espacio de trabajo de memoria|Bytes|Media|Espacio de trabajo de memoria|Instance|
|AverageMemoryWorkingSet|Espacio de trabajo de memoria promedio|Bytes|Media|Espacio de trabajo de memoria promedio|Instance|
|FunctionExecutionUnits|Unidades de ejecución de función|Recuento|Total|Unidades de ejecución de función|Instance|
|FunctionExecutionCount|Recuento de ejecución de funciones|Recuento|Total|Recuento de ejecución de funciones|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|CpuTime|Tiempo de CPU|Segundos|Total|Tiempo de CPU|Instance|
|Requests|Requests|Recuento|Total|Requests|Instance|
|BytesReceived|Entrada de datos|Bytes|Total|Entrada de datos|Instance|
|BytesSent|Salida de datos|Bytes|Total|Salida de datos|Instance|
|Http101|Http 101|Recuento|Total|Http 101|Instance|
|Http2xx|Http 2xx|Recuento|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Recuento|Total|Http 3xx|Instance|
|Http401|Http 401|Recuento|Total|Http 401|Instance|
|Http403|Http 403|Recuento|Total|Http 403|Instance|
|Http404|Http 404|Recuento|Total|Http 404|Instance|
|Http406|Http 406|Recuento|Total|Http 406|Instance|
|Http4xx|Http 4xx|Recuento|Total|Http 4xx|Instance|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|Instance|
|MemoryWorkingSet|Espacio de trabajo de memoria|Bytes|Media|Espacio de trabajo de memoria|Instance|
|AverageMemoryWorkingSet|Espacio de trabajo de memoria promedio|Bytes|Media|Espacio de trabajo de memoria promedio|Instance|
|AverageResponseTime|Tiempo de respuesta promedio|Segundos|Media|Tiempo de respuesta promedio|Instance|
|FunctionExecutionUnits|Unidades de ejecución de función|Recuento|Media|Unidades de ejecución de función|Instance|
|FunctionExecutionCount|Recuento de ejecución de funciones|Recuento|Media|Recuento de ejecución de funciones|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|Requests|Requests|Recuento|Total|Requests|Instance|
|BytesReceived|Entrada de datos|Bytes|Total|Entrada de datos|Instance|
|BytesSent|Salida de datos|Bytes|Total|Salida de datos|Instance|
|Http101|Http 101|Recuento|Total|Http 101|Instance|
|Http2xx|Http 2xx|Recuento|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Recuento|Total|Http 3xx|Instance|
|Http401|Http 401|Recuento|Total|Http 401|Instance|
|Http403|Http 403|Recuento|Total|Http 403|Instance|
|Http404|Http 404|Recuento|Total|Http 404|Instance|
|Http406|Http 406|Recuento|Total|Http 406|Instance|
|Http4xx|Http 4xx|Recuento|Total|Http 4xx|Instance|
|Http5xx|Errores de servidor HTTP|Recuento|Total|Errores de servidor HTTP|Instance|
|AverageResponseTime|Tiempo de respuesta promedio|Segundos|Media|Tiempo de respuesta promedio|Instance|
|CpuPercentage|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Instance|
|MemoryPercentage|Porcentaje de memoria|Percent|Media|Porcentaje de memoria|Instance|
|DiskQueueLength|Longitud de la cola de disco|Recuento|Media|Longitud de la cola de disco|Instance|
|HttpQueueLength|Longitud de la cola HTTP|Recuento|Media|Longitud de la cola HTTP|Instance|
|ActiveRequests|Solicitudes activas|Recuento|Total|Solicitudes activas|Instance|
|TotalFrontEnds|Número total de front-ends|Recuento|Media|Número total de front-ends|Instance|
|SmallAppServicePlanInstances|Trabajos pequeños del plan de App Service|Recuento|Media|Trabajos pequeños del plan de App Service|Instance|
|MediumAppServicePlanInstances|Trabajos medianos del plan de App Service|Recuento|Media|Trabajos medianos del plan de App Service|Instance|
|LargeAppServicePlanInstances|Trabajos grandes del plan de App Service|Recuento|Media|Trabajos grandes del plan de App Service|Instance|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nombre de métrica para mostrar|Unidad|Tipo de agregación|DESCRIPCIÓN|Dimensiones|
|---|---|---|---|---|---|
|WorkersTotal|Número total de trabajos|Recuento|Media|Número total de trabajos|Instance|
|WorkersAvailable|Trabajos disponibles|Recuento|Media|Trabajos disponibles|Instance|
|WorkersUsed|Trabajos usados|Recuento|Media|Trabajos usados|Instance|
|CpuPercentage|Porcentaje de CPU|Percent|Media|Porcentaje de CPU|Instance|
|MemoryPercentage|Porcentaje de memoria|Percent|Media|Porcentaje de memoria|Instance|

## <a name="next-steps"></a>pasos siguientes
* [Lea información sobre las métricas en Azure Monitor](monitoring-overview-metrics.md)
* [Creación de alertas basadas en métricas](insights-receive-alert-notifications.md)
* [Exportación de métricas a cuentas de almacenamiento, Event Hubs o Log Analytics](monitoring-overview-of-diagnostic-logs.md)
