---
title: Métricas de Azure Monitor - Métricas admitidas por tipo de recurso | Microsoft Docs
description: Lista de métricas disponibles para cada tipo de recurso con Azure Monitor.
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# Métricas compatibles con Azure Monitor
Azure Monitor proporciona varias maneras de interactuar con las métricas, como la representación en gráficos en el portal, el acceso a ellas a través de la API de REST o consultarlas con PowerShell o la CLI. A continuación se muestra una lista completa de todas las métricas disponibles actualmente en la canalización de métricas de Azure Monitor.

> [!NOTE]
> Otras métricas pueden estar disponibles en el portal o mediante las API heredadas. Esta lista incluye solo las métricas de versión preliminar pública disponibles con la versión preliminar pública de la canalización de métricas consolidado de Azure Monitor.
> 
> 

## Microsoft.Batch/batchAccounts
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| CoreCount |Recuento de núcleos |Percent |Media |Número promedio de núcleos de la cuenta de Batch |
| TotalNodeCount |Recuento de nodos |Percent |Media |Número promedio de nodos de la cuenta de Batch |
| CreatingNodeCount |Recuento de nodos creados |Percent |Media |Número de nodos que se van a crear |
| StartingNodeCount |Recuento de nodos de inicio |Percent |Media |Número de nodos que se van a iniciar |
| WaitingForStartTaskNodeCount |Recuento de nodos esperando a la tarea de inicio |Percent |Media |Número de nodos que esperan a que se complete la tarea de inicio |
| StartTaskFailedNodeCount |Recuento de nodos con error de la tarea de inicio |Percent |Media |Número de nodos con error en la tarea de inicio |
| IdleNodeCount |Recuento de nodos inactivos |Percent |Media |Número de nodos inactivos |
| OfflineNodeCount |Recuento de nodos sin conexión |Percent |Media |Número de nodos sin conexión |
| RebootingNodeCount |Recuento de nodos de reinicio |Percent |Media |Número de nodos de reinicio |
| ReimagingNodeCount |Recuento de nodos de restablecimiento de imagen inicial |Percent |Media |Número de nodos de restablecimiento de imagen inicial |
| RunningNodeCount |Recuento de nodos en ejecución |Percent |Media |Número de nodos en ejecución |
| LeavingPoolNodeCount |Recuento de nodos que salen del grupo |Percent |Media |Número de nodos que abandonan el grupo |
| UnusableNodeCount |Recuento de nodos no utilizables |Percent |Media |Número de nodos inutilizables |
| TaskStartEvent |Eventos de inicio de tarea |Recuento |Total |Número total de tareas que se han iniciado |
| TaskCompleteEvent |Eventos de tarea completada |Recuento |Total |Número total de tareas que se han completado |
| TaskFailEvent |Eventos de error en tareas |Recuento |Total |Número total de tareas que se han completado con errores |
| PoolCreateEvent |Eventos de creación de grupo |Recuento |Total |Número total de grupos que se han creado |
| PoolResizeStartEvent |Eventos de inicio de cambio de tamaño de grupo |Recuento |Total |Número total de cambios de tamaño de grupo que se han iniciado |
| PoolResizeCompleteEvent |Eventos de finalización de cambio de tamaño de grupo |Recuento |Total |Número total de cambios de tamaño de grupo completados |
| PoolDeleteStartEvent |Eventos de inicio de eliminación de grupo |Recuento |Total |Número total de eliminaciones de grupo que se han iniciado |
| PoolDeleteCompleteEvent |Eventos de finalización de eliminaciones de grupo |Recuento |Total |Número total de eliminaciones de grupo que se han completado |

## Microsoft.Cache/redis
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| connectedclients |Clientes conectados |Recuento |Máxima | |
| totalcommandsprocessed |Total de operaciones |Recuento |Total | |
| cachehits |Aciertos de caché |Recuento |Total | |
| cachemisses |Errores de caché |Recuento |Total | |
| getcommands |Gets |Recuento |Total | |
| setcommands |Sets |Recuento |Total | |
| evictedkeys |Claves expulsadas |Recuento |Total | |
| totalkeys |Total de claves |Recuento |Máxima | |
| expiredkeys |Claves expiradas |Recuento |Total | |
| usedmemory |Memoria usada |Bytes |Máxima | |
| usedmemoryRss |Memoria RSS usada |Bytes |Máxima | |
| serverLoad |Carga de servidor |Percent |Máxima | |
| cacheWrite |Escritura de caché |BytesPerSecond |Máxima | |
| cacheRead |Lectura de caché |BytesPerSecond |Máxima | |
| percentProcessorTime |CPU |Percent |Máxima | |
| connectedclients0 |Clientes conectados (partición 0) |Recuento |Máxima | |
| totalcommandsprocessed0 |Total de operaciones (partición 0) |Recuento |Total | |
| cachehits0 |Aciertos de caché (partición 0) |Recuento |Total | |
| cachemisses0 |Errores de caché (partición 0) |Recuento |Total | |
| getcommands0 |Obtenciones (partición 0) |Recuento |Total | |
| setcommands0 |Definiciones (partición 0) |Recuento |Total | |
| evictedkeys0 |Claves expulsadas (partición 0) |Recuento |Total | |
| totalkeys0 |Total de claves (nodo 0) |Recuento |Máxima | |
| expiredkeys0 |Claves expiradas (partición 0) |Recuento |Total | |
| usedmemory0 |Memoria usada (partición 0) |Bytes |Máxima | |
| usedmemoryRss0 |Memoria RSS usada (partición 0) |Bytes |Máxima | |
| serverLoad0 |Carga de servidor (partición 0) |Percent |Máxima | |
| cacheWrite0 |Escritura de caché (partición 0) |BytesPerSecond |Máxima | |
| cacheRead0 |Lectura de caché (partición 0) |BytesPerSecond |Máxima | |
| percentProcessorTime0 |CPU (partición 0) |Percent |Máxima | |
| connectedclients1 |Clientes conectados (partición 1) |Recuento |Máxima | |
| totalcommandsprocessed1 |Total de operaciones (partición 1) |Recuento |Total | |
| cachehits1 |Aciertos de caché (partición 1) |Recuento |Total | |
| cachemisses1 |Errores de caché (partición 1) |Recuento |Total | |
| getcommands1 |Obtenciones (partición 1) |Recuento |Total | |
| setcommands1 |Definiciones (partición 1) |Recuento |Total | |
| evictedkeys1 |Claves expulsadas (partición 1) |Recuento |Total | |
| totalkeys1 |Total de claves (nodo 1) |Recuento |Máxima | |
| expiredkeys1 |Claves expiradas (partición 1) |Recuento |Total | |
| usedmemory1 |Memoria usada (partición 1) |Bytes |Máxima | |
| usedmemoryRss1 |Memoria RSS usada (partición 1) |Bytes |Máxima | |
| serverLoad1 |Carga de servidor (partición 1) |Percent |Máxima | |
| cacheWrite1 |Escritura de caché (partición 1) |BytesPerSecond |Máxima | |
| cacheRead1 |Lectura de caché (partición 1) |BytesPerSecond |Máxima | |
| percentProcessorTime1 |CPU (partición 1) |Percent |Máxima | |
| connectedclients2 |Clientes conectados (partición 2) |Recuento |Máxima | |
| totalcommandsprocessed2 |Total de operaciones (partición 2) |Recuento |Total | |
| cachehits2 |Aciertos de caché (partición 2) |Recuento |Total | |
| cachemisses2 |Errores de caché (partición 2) |Recuento |Total | |
| getcommands2 |Obtenciones (partición 2) |Recuento |Total | |
| setcommands2 |Definiciones (partición 2) |Recuento |Total | |
| evictedkeys2 |Claves expulsadas (partición 2) |Recuento |Total | |
| totalkeys2 |Total de claves (nodo 2) |Recuento |Máxima | |
| expiredkeys2 |Claves expiradas (partición 2) |Recuento |Total | |
| usedmemory2 |Memoria usada (partición 2) |Bytes |Máxima | |
| usedmemoryRss2 |Memoria RSS usada (partición 2) |Bytes |Máxima | |
| serverLoad2 |Carga de servidor (partición 2) |Percent |Máxima | |
| cacheWrite2 |Escritura de caché (partición 2) |BytesPerSecond |Máxima | |
| cacheRead2 |Lectura de caché (partición 2) |BytesPerSecond |Máxima | |
| percentProcessorTime2 |CPU (partición 2) |Percent |Máxima | |
| connectedclients3 |Clientes conectados (partición 3) |Recuento |Máxima | |
| totalcommandsprocessed3 |Total de operaciones (partición 3) |Recuento |Total | |
| cachehits3 |Aciertos de caché (partición 3) |Recuento |Total | |
| cachemisses3 |Errores de caché (partición 3) |Recuento |Total | |
| getcommands3 |Obtenciones (partición 3) |Recuento |Total | |
| setcommands3 |Definiciones (partición 3) |Recuento |Total | |
| evictedkeys3 |Claves expulsadas (partición 3) |Recuento |Total | |
| totalkeys3 |Total de claves (nodo 3) |Recuento |Máxima | |
| expiredkeys3 |Claves expiradas (partición 3) |Recuento |Total | |
| usedmemory3 |Memoria usada (partición 3) |Bytes |Máxima | |
| usedmemoryRss3 |Memoria RSS usada (partición 3) |Bytes |Máxima | |
| serverLoad3 |Carga de servidor (partición 3) |Percent |Máxima | |
| cacheWrite3 |Escritura de caché (partición 3) |BytesPerSecond |Máxima | |
| cacheRead3 |Lectura de caché (partición 3) |BytesPerSecond |Máxima | |
| percentProcessorTime3 |CPU (partición 3) |Percent |Máxima | |
| connectedclients4 |Clientes conectados (partición 4) |Recuento |Máxima | |
| totalcommandsprocessed4 |Total de operaciones (partición 4) |Recuento |Total | |
| cachehits4 |Aciertos de caché (partición 4) |Recuento |Total | |
| cachemisses4 |Errores de caché (partición 4) |Recuento |Total | |
| getcommands4 |Obtenciones (partición 4) |Recuento |Total | |
| setcommands4 |Definiciones (partición 4) |Recuento |Total | |
| evictedkeys4 |Claves expulsadas (partición 4) |Recuento |Total | |
| totalkeys4 |Total de claves (nodo 4) |Recuento |Máxima | |
| expiredkeys4 |Claves expiradas (partición 4) |Recuento |Total | |
| usedmemory4 |Memoria usada (partición 4) |Bytes |Máxima | |
| usedmemoryRss4 |Memoria RSS usada (partición 4) |Bytes |Máxima | |
| serverLoad4 |Carga de servidor (partición 4) |Percent |Máxima | |
| cacheWrite4 |Escritura de caché (partición 4) |BytesPerSecond |Máxima | |
| cacheRead4 |Lectura de caché (partición 4) |BytesPerSecond |Máxima | |
| percentProcessorTime4 |CPU (partición 4) |Percent |Máxima | |
| connectedclients5 |Clientes conectados (partición 5) |Recuento |Máxima | |
| totalcommandsprocessed5 |Total de operaciones (partición 5) |Recuento |Total | |
| cachehits5 |Aciertos de caché (partición 5) |Recuento |Total | |
| cachemisses5 |Errores de caché (partición 5) |Recuento |Total | |
| getcommands5 |Obtenciones (partición 5) |Recuento |Total | |
| setcommands5 |Definiciones (partición 5) |Recuento |Total | |
| evictedkeys5 |Claves expulsadas (partición 5) |Recuento |Total | |
| totalkeys5 |Total de claves (nodo 5) |Recuento |Máxima | |
| expiredkeys5 |Claves expiradas (partición 5) |Recuento |Total | |
| usedmemory5 |Memoria usada (partición 5) |Bytes |Máxima | |
| usedmemoryRss5 |Memoria RSS usada (partición 5) |Bytes |Máxima | |
| serverLoad5 |Carga de servidor (partición 5) |Percent |Máxima | |
| cacheWrite5 |Escritura de caché (partición 5) |BytesPerSecond |Máxima | |
| cacheRead5 |Lectura de caché (partición 5) |BytesPerSecond |Máxima | |
| percentProcessorTime5 |CPU (partición 5) |Percent |Máxima | |
| connectedclients6 |Clientes conectados (partición 6) |Recuento |Máxima | |
| totalcommandsprocessed6 |Total de operaciones (partición 6) |Recuento |Total | |
| cachehits6 |Aciertos de caché (partición 6) |Recuento |Total | |
| cachemisses6 |Errores de caché (partición 6) |Recuento |Total | |
| getcommands6 |Obtenciones (partición 6) |Recuento |Total | |
| setcommands6 |Definiciones (partición 6) |Recuento |Total | |
| evictedkeys6 |Claves expulsadas (partición 6) |Recuento |Total | |
| totalkeys6 |Total de claves (nodo 6) |Recuento |Máxima | |
| expiredkeys6 |Claves expiradas (partición 6) |Recuento |Total | |
| usedmemory6 |Memoria usada (partición 6) |Bytes |Máxima | |
| usedmemoryRss6 |Memoria RSS usada (partición 6) |Bytes |Máxima | |
| serverLoad6 |Carga de servidor (partición 6) |Percent |Máxima | |
| cacheWrite6 |Escritura de caché (partición 6) |BytesPerSecond |Máxima | |
| cacheRead6 |Lectura de caché (partición 6) |BytesPerSecond |Máxima | |
| percentProcessorTime6 |CPU (partición 6) |Percent |Máxima | |
| connectedclients7 |Clientes conectados (partición 7) |Recuento |Máxima | |
| totalcommandsprocessed7 |Total de operaciones (partición 7) |Recuento |Total | |
| cachehits7 |Aciertos de caché (partición 7) |Recuento |Total | |
| cachemisses7 |Errores de caché (partición 7) |Recuento |Total | |
| getcommands7 |Obtenciones (partición 7) |Recuento |Total | |
| setcommands7 |Definiciones (partición 7) |Recuento |Total | |
| evictedkeys7 |Claves expulsadas (partición 7) |Recuento |Total | |
| totalkeys7 |Total de claves (nodo 7) |Recuento |Máxima | |
| expiredkeys7 |Claves expiradas (partición 7) |Recuento |Total | |
| usedmemory7 |Memoria usada (partición 7) |Bytes |Máxima | |
| usedmemoryRss7 |Memoria RSS usada (partición 7) |Bytes |Máxima | |
| serverLoad7 |Carga de servidor (partición 7) |Percent |Máxima | |
| cacheWrite7 |Escritura de caché (partición 7) |BytesPerSecond |Máxima | |
| cacheRead7 |Lectura de caché (partición 7) |BytesPerSecond |Máxima | |
| percentProcessorTime7 |CPU (partición 7) |Percent |Máxima | |
| connectedclients8 |Clientes conectados (partición 8) |Recuento |Máxima | |
| totalcommandsprocessed8 |Total de operaciones (partición 8) |Recuento |Total | |
| cachehits8 |Aciertos de caché (partición 8) |Recuento |Total | |
| cachemisses8 |Errores de caché (partición 8) |Recuento |Total | |
| getcommands8 |Obtenciones (partición 8) |Recuento |Total | |
| setcommands8 |Definiciones (partición 8) |Recuento |Total | |
| evictedkeys8 |Claves expulsadas (partición 8) |Recuento |Total | |
| totalkeys8 |Total de claves (nodo 8) |Recuento |Máxima | |
| expiredkeys8 |Claves expiradas (partición 8) |Recuento |Total | |
| usedmemory8 |Memoria usada (partición 8) |Bytes |Máxima | |
| usedmemoryRss8 |Memoria RSS usada (partición 8) |Bytes |Máxima | |
| serverLoad8 |Carga de servidor (partición 8) |Percent |Máxima | |
| cacheWrite8 |Escritura de caché (partición 8) |BytesPerSecond |Máxima | |
| cacheRead8 |Lectura de caché (partición 8) |BytesPerSecond |Máxima | |
| percentProcessorTime8 |CPU (partición 8) |Percent |Máxima | |
| connectedclients9 |Clientes conectados (partición 9) |Recuento |Máxima | |
| totalcommandsprocessed9 |Total de operaciones (partición 9) |Recuento |Total | |
| cachehits9 |Aciertos de caché (partición 9) |Recuento |Total | |
| cachemisses9 |Errores de caché (partición 9) |Recuento |Total | |
| getcommands9 |Obtenciones (partición 9) |Recuento |Total | |
| setcommands9 |Definiciones (partición 9) |Recuento |Total | |
| evictedkeys9 |Claves expulsadas (partición 9) |Recuento |Total | |
| totalkeys9 |Total de claves (nodo 9) |Recuento |Máxima | |
| expiredkeys9 |Claves expiradas (partición 9) |Recuento |Total | |
| usedmemory9 |Memoria usada (partición 9) |Bytes |Máxima | |
| usedmemoryRss9 |Memoria RSS usada (partición 9) |Bytes |Máxima | |
| serverLoad9 |Carga de servidor (partición 9) |Percent |Máxima | |
| cacheWrite9 |Escritura de caché (partición 9) |BytesPerSecond |Máxima | |
| cacheRead9 |Lectura de caché (partición 9) |BytesPerSecond |Máxima | |
| percentProcessorTime9 |CPU (partición 9) |Percent |Máxima | |

## Microsoft.Compute/virtualMachines
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| Porcentaje de CPU |Porcentaje de CPU |Percent |Media |El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente |
| Red interna |Red interna |Bytes |Total |El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante) |
| Red externa |Red externa |Bytes |Total |El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente) |
| Bytes de lectura de disco |Bytes de lectura de disco |Bytes |Total |Número total de bytes que se leen desde el disco durante el período de supervisión |
| Bytes de escritura de disco |Bytes de escritura de disco |Bytes |Total |Número total de bytes que se escriben en el disco durante el período de supervisión |
| Operaciones de lectura de disco por segundo |Operaciones de lectura de disco por segundo |CountPerSecond |Media |E/S por segundo de lectura de disco |
| Operaciones de escritura por segundo en disco |Operaciones de escritura por segundo en disco |CountPerSecond |Media |E/S por segundo de escritura en disco |

## Microsoft.Compute/virtualMachineScaleSets
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| Porcentaje de CPU |Porcentaje de CPU |Percent |Media |El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente |
| Red interna |Red interna |Bytes |Total |El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante) |
| Red externa |Red externa |Bytes |Total |El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente) |
| Bytes de lectura de disco |Bytes de lectura de disco |Bytes |Total |Número total de bytes que se leen desde el disco durante el período de supervisión |
| Bytes de escritura de disco |Bytes de escritura de disco |Bytes |Total |Número total de bytes que se escriben en el disco durante el período de supervisión |
| Operaciones de lectura de disco por segundo |Operaciones de lectura de disco por segundo |CountPerSecond |Media |E/S por segundo de lectura de disco |
| Operaciones de escritura por segundo en disco |Operaciones de escritura por segundo en disco |CountPerSecond |Media |E/S por segundo de escritura en disco |

## Microsoft.Compute/virtualMachineScaleSets/virtualMachines
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| Porcentaje de CPU |Porcentaje de CPU |Percent |Media |El porcentaje de unidades de proceso asignadas que las máquinas virtuales usan actualmente |
| Red interna |Red interna |Bytes |Total |El número de bytes recibidos en todas las interfaces de red por las máquinas virtuales (tráfico entrante) |
| Red externa |Red externa |Bytes |Total |El número de bytes enviados en todas las interfaces de red por las máquinas virtuales (tráfico saliente) |
| Bytes de lectura de disco |Bytes de lectura de disco |Bytes |Total |Número total de bytes que se leen desde el disco durante el período de supervisión |
| Bytes de escritura de disco |Bytes de escritura de disco |Bytes |Total |Número total de bytes que se escriben en el disco durante el período de supervisión |
| Operaciones de lectura de disco por segundo |Operaciones de lectura de disco por segundo |CountPerSecond |Media |E/S por segundo de lectura de disco |
| Operaciones de escritura por segundo en disco |Operaciones de escritura por segundo en disco |CountPerSecond |Media |E/S por segundo de escritura en disco |

## Microsoft.Devices/IotHubs
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |Intentos de envío de mensaje de telemetría |Recuento |Total |Número de mensajes de telemetría de dispositivo a la nube que se han intentado enviar a IoT Hub |
| d2c.telemetry.ingress.success |Mensajes de telemetría enviados |Recuento |Total |Número de mensajes de telemetría de dispositivo a la nube enviados correctamente a IoT Hub |
| c2d.commands.egress.complete.success |Comandos completados |Recuento |Total |Número de comandos de dispositivo a la nube que el dispositivo ha completado correctamente |
| c2d.commands.egress.abandon.success |Comandos abandonados |Recuento |Total |Número de comandos de dispositivo a la nube que el dispositivo ha abandonado |
| c2d.commands.egress.reject.success |Comandos rechazados |Recuento |Total |Número de comandos de dispositivo a la nube que el dispositivo ha rechazado |
| devices.totalDevices |Número total de dispositivos |Recuento |Total |Número de dispositivos registrados en IoT Hub |
| devices.connectedDevices.allProtocol |Dispositivos conectados |Recuento |Total |Número de dispositivos conectados a IoT Hub |

## Microsoft.EventHub/namespaces
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| INREQS |Solicitudes entrantes |Recuento |Total | |
| SUCCREQ |Solicitudes correctas |Recuento |Total | |
| FAILREQ |Solicitudes con error |Recuento |Total | |
| SVRBSY |Errores de servidor ocupado |Recuento |Total | |
| INTERR |Errores internos del servidor |Recuento |Total | |
| MISCERR |Otros errores |Recuento |Total | |
| INMSGS |Mensajes entrantes |Recuento |Total | |
| OUTMSGS |Mensajes salientes |Recuento |Total | |
| EHINMBS |Bytes entrantes por segundo |BytesPerSecond |Total | |
| EHOUTMBS |Bytes salientes por segundo |BytesPerSecond |Total | |
| EHABL |Mensajes de trabajo pendiente de archivado |Recuento |Total | |
| EHAMSGS |Mensajes de archivado |Recuento |Total | |
| EHAMBS |Rendimiento de mensajes de archivado |BytesPerSecond |Total | |

## Microsoft.Logic/workflows
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| RunsStarted |Ejecuciones iniciadas |Recuento |Total |Número de ejecuciones de flujo de trabajo iniciadas |
| RunsCompleted |Ejecuciones completadas |Recuento |Total |Número de ejecuciones de flujo de trabajo completadas |
| RunsSucceeded |Ejecuciones correctas |Recuento |Total |Número de ejecuciones de flujo de trabajo correctas |
| RunsFailed |Ejecuciones con errores |Recuento |Total |Número de ejecuciones de flujo de trabajo con errores |
| RunsCancelled |Ejecuciones canceladas |Recuento |Total |Número de ejecuciones de flujo de trabajo canceladas |
| RunLatency |Latencia de ejecución |Segundos |Media |Latencia de ejecuciones de flujo de trabajo completadas |
| RunSuccessLatency |Latencia de ejecuciones correctas |Segundos |Media |Latencia de ejecuciones de flujo de trabajo correctas |
| RunThrottledEvents |Ejecución de eventos limitados |Recuento |Total |Número de eventos limitados de desencadenador o acciones de flujo de trabajo |
| ActionsStarted |Acciones iniciadas |Recuento |Total |Número de acciones de flujo de trabajo iniciadas |
| ActionsCompleted |Acciones completadas |Recuento |Total |Número de acciones de flujo de trabajo completadas |
| ActionsSucceeded |Acciones correctas |Recuento |Total |Número de acciones de flujo de trabajo correctas |
| ActionsFailed |Acciones con errores |Recuento |Total |Número de acciones de flujo de trabajo con errores |
| ActionsSkipped |Acciones omitidas |Recuento |Total |Número de acciones de flujo de trabajo omitidas |
| ActionLatency |Latencia de acciones |Segundos |Media |Latencia de acciones de flujo de trabajo completadas |
| ActionSuccessLatency |Latencia de acciones correctas |Segundos |Media |Latencia de acciones de flujo de trabajo correctas |
| ActionThrottledEvents |Eventos limitados de acciones |Recuento |Total |Número de eventos limitados de acciones de flujo de trabajo |
| TriggersStarted |Desencadenadores iniciados |Recuento |Total |Número de desencadenadores de flujo de trabajo iniciados |
| TriggersCompleted |Desencadenadores completados |Recuento |Total |Número de desencadenadores de flujo de trabajo completados |
| TriggersSucceeded |Desencadenadores correctos |Recuento |Total |Número de desencadenadores de flujo de trabajo correctos |
| TriggersFailed |Desencadenadores con errores |Recuento |Total |Número de desencadenadores de flujo de trabajo con errores |
| TriggersSkipped |Desencadenadores omitidos |Recuento |Total |Número de desencadenadores de flujo de trabajo omitidos |
| TriggersFired |Desencadenadores activados |Recuento |Total |Número de desencadenadores de flujo de trabajo activados |
| TriggerLatency |Latencia de desencadenador |Segundos |Media |Latencia de desencadenadores de flujo de trabajo completados |
| TriggerFireLatency |Latencia de desencadenador activado |Segundos |Media |Latencia de desencadenadores de flujo de trabajo activados |
| TriggerSuccessLatency |Latencia de desencadenadores correctos |Segundos |Media |Latencia de desencadenadores de flujo de trabajo correctos |
| TriggerThrottledEvents |Eventos limitados de desencadenadores |Recuento |Total |Número de eventos limitados de desencadenador de flujo de trabajo |

## Microsoft.Network/applicationGateways
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| Rendimiento |Rendimiento |BytesPerSecond |Media | |

## Microsoft.Search/searchServices
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| Latency |Latency |Segundos |Media |Promedio de latencia para el servicio de búsqueda |
| SearchLatency |Latencia de búsqueda |Segundos |Media |Promedio de latencia de búsqueda para el servicio de búsqueda |
| SearchQueriesPerSecond |Consultas de búsqueda por segundo |CountPerSecond |Media |Consultas de búsqueda por segundo para el servicio de búsqueda |
| ThrottledSearchQueriesPercentage |Porcentaje de consultas de búsqueda limitadas |Percent |Media |Porcentaje de consultas de búsqueda limitadas para el servicio de búsqueda |

## Microsoft.ServiceBus/namespaces
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| CPUXNS |Uso de CPU por espacio de nombres |Percent |Máxima | |
| WSXNS |Uso de tamaño de memoria por espacio de nombres |Percent |Máxima | |

## Microsoft.Sql/servers/databases
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| cpu\_percent |Porcentaje de CPU |Percent |Máxima |Porcentaje de CPU |
| physical\_data\_read\_percent |Porcentaje de E/S de datos |Percent |Máxima |Porcentaje de E/S de datos |
| log\_write\_percent |Porcentaje de E/S de registro |Percent |Máxima |Porcentaje de E/S de registro |
| dtu\_consumption\_percent |Porcentaje de DTU |Percent |Máxima |Porcentaje de DTU |
| storage |Tamaño total de base de datos |Bytes |Máxima |Tamaño total de base de datos |
| connection\_successful |Conexiones correctas |Recuento |Total |Conexiones correctas |
| connection\_failed |Conexiones con errores |Recuento |Total |Conexiones con errores |
| blocked\_by\_firewall |Bloqueado por el firewall |Recuento |Total |Bloqueado por el firewall |
| deadlock |Interbloqueos |Recuento |Total |Interbloqueos |
| storage\_percent |Porcentaje de tamaño de base de datos |Percent |Máxima |Porcentaje de tamaño de base de datos |
| xtp\_storage\_percent |Porcentaje de almacenamiento de OLTP en memoria (versión preliminar) |Percent |Máxima |Porcentaje de almacenamiento de OLTP en memoria (versión preliminar) |
| workers\_percent |Porcentaje de trabajos |Percent |Máxima |Porcentaje de trabajos |
| sessions\_percent |Porcentaje de sesiones |Percent |Máxima |Porcentaje de sesiones |
| dtu\_limit |Límite de DTU |Recuento |Máxima |Límite de DTU |
| dtu\_used |DTU utilizada |Recuento |Máxima |DTU utilizada |
| service\_level\_objective |Objetivo de nivel de servicio de la base de datos |Recuento |Total |Objetivo de nivel de servicio de la base de datos |
| dwu\_limit |Límite de DWU |Recuento |Máxima |Límite de DWU |
| dwu\_consumption\_percent |Porcentaje de DWU |Percent |Máxima |Porcentaje de DWU |
| dwu\_used |DWU utilizada |Recuento |Máxima |DWU utilizada |

## Microsoft.Sql/servers/elasticPools
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| cpu\_percent |Porcentaje de CPU |Percent |Máxima |Porcentaje de CPU |
| physical\_data\_read\_percent |Porcentaje de E/S de datos |Percent |Máxima |Porcentaje de E/S de datos |
| log\_write\_percent |Porcentaje de E/S de registro |Percent |Máxima |Porcentaje de E/S de registro |
| dtu\_consumption\_percent |Porcentaje de DTU |Percent |Máxima |Porcentaje de DTU |
| storage\_percent |Porcentaje de almacenamiento |Percent |Máxima |Porcentaje de almacenamiento |
| workers\_percent |Porcentaje de trabajos |Percent |Máxima |Porcentaje de trabajos |
| sessions\_percent |Porcentaje de sesiones |Percent |Máxima |Porcentaje de sesiones |
| eDTU\_limit |Límite de eDTU |Recuento |Máxima |Límite de eDTU |
| storage\_limit |Límite de almacenamiento |Bytes |Máxima |Límite de almacenamiento |
| eDTU\_used |eDTU utilizada |Recuento |Máxima |eDTU utilizada |
| storage\_used |Almacenamiento utilizado |Bytes |Máxima |Almacenamiento utilizado |

## Microsoft.StreamAnalytics/streamingjobs
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| ResourceUtilization |SU % uso |Percent |Máxima |SU % uso |
| InputEvents |Eventos de entrada |Bytes |Total |Eventos de entrada |
| InputEventBytes |Bytes del evento de entrada |Bytes |Total |Bytes del evento de entrada |
| LateInputEvents |Eventos de entrada retrasada |Bytes |Total |Eventos de entrada retrasada |
| OutputEvents |Eventos de salida |Bytes |Total |Eventos de salida |
| ConversionErrors |Errores de conversión de datos |Bytes |Total |Errores de conversión de datos |
| Errors |Errores de tiempo de ejecución |Bytes |Total |Errores de tiempo de ejecución |
| DroppedOrAdjustedEvents |Eventos de fuera de orden |Bytes |Total |Eventos de fuera de orden |
| AMLCalloutRequests |Solicitudes de función |Bytes |Total |Solicitudes de función |
| AMLCalloutFailedRequests |Solicitudes de función con errores |Bytes |Total |Solicitudes de función con errores |
| AMLCalloutInputEvents |Eventos de función |Bytes |Total |Eventos de función |

## Microsoft.Web/serverfarms
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| CpuPercentage |Porcentaje de CPU |Percent |Media |Porcentaje de CPU |
| MemoryPercentage |Porcentaje de memoria |Percent |Media |Porcentaje de memoria |
| DiskQueueLength |Longitud de la cola de disco |Recuento |Total |Longitud de la cola de disco |
| HttpQueueLength |Longitud de la cola HTTP |Recuento |Total |Longitud de la cola HTTP |
| BytesReceived |Entrada de datos |Bytes |Total |Entrada de datos |
| BytesSent |Salida de datos |Bytes |Total |Salida de datos |

## Microsoft.Web/sites
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| CpuTime |Tiempo de CPU |Segundos |Total |Tiempo de CPU |
| Solicitudes |Solicitudes |Recuento |Total |Solicitudes |
| BytesReceived |Entrada de datos |Bytes |Total |Entrada de datos |
| BytesSent |Salida de datos |Bytes |Total |Salida de datos |
| Http2xx |Http 2xx |Recuento |Total |Http 2xx |
| Http3xx |Http 3xx |Recuento |Total |Http 3xx |
| Http401 |Http 401 |Recuento |Total |Http 401 |
| Http403 |Http 403 |Recuento |Total |Http 403 |
| Http404 |Http 404 |Recuento |Total |Http 404 |
| Http406 |Http 406 |Recuento |Total |Http 406 |
| Http4xx |Http 4xx |Recuento |Total |Http 4xx |
| Http5xx |Errores de servidor HTTP |Recuento |Total |Errores de servidor HTTP |
| MemoryWorkingSet |Espacio de trabajo de memoria |Bytes |Total |Espacio de trabajo de memoria |
| AverageMemoryWorkingSet |Espacio de trabajo de memoria promedio |Bytes |Media |Espacio de trabajo de memoria promedio |
| AverageResponseTime |Tiempo de respuesta promedio |Segundos |Media |Tiempo de respuesta promedio |

## Microsoft.Web/sites/slots
| Métrica | Nombre de métrica para mostrar | Unidad | Tipo de agregación | Description |
| --- | --- | --- | --- | --- |
| CpuTime |Tiempo de CPU |Segundos |Total |Tiempo de CPU |
| Solicitudes |Solicitudes |Recuento |Total |Solicitudes |
| BytesReceived |Entrada de datos |Bytes |Total |Entrada de datos |
| BytesSent |Salida de datos |Bytes |Total |Salida de datos |
| Http2xx |Http 2xx |Recuento |Total |Http 2xx |
| Http3xx |Http 3xx |Recuento |Total |Http 3xx |
| Http401 |Http 401 |Recuento |Total |Http 401 |
| Http403 |Http 403 |Recuento |Total |Http 403 |
| Http404 |Http 404 |Recuento |Total |Http 404 |
| Http406 |Http 406 |Recuento |Total |Http 406 |
| Http4xx |Http 4xx |Recuento |Total |Http 4xx |
| Http5xx |Errores de servidor HTTP |Recuento |Total |Errores de servidor HTTP |
| MemoryWorkingSet |Espacio de trabajo de memoria |Bytes |Total |Espacio de trabajo de memoria |
| AverageMemoryWorkingSet |Espacio de trabajo de memoria promedio |Bytes |Media |Espacio de trabajo de memoria promedio |
| AverageResponseTime |Tiempo de respuesta promedio |Segundos |Media |Tiempo de respuesta promedio |

## Pasos siguientes
* [Lea información sobre las métricas en Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md#monitoring-sources)
* [Creación de alertas basadas en métricas](insights-receive-alert-notifications.md)
* [Exportación de métricas a cuentas de almacenamiento, Event Hubs o Log Analytics](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

<!---HONumber=AcomDC_0928_2016-->