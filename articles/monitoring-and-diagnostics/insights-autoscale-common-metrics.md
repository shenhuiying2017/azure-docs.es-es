---
title: "Métricas comunes de escalado automático de Azure Monitor | Microsoft Docs"
description: "Aprenda qué métricas se utilizan normalmente para el escalado automático de Servicios en la nube, Máquinas virtuales y Aplicaciones web."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: ancav
ms.openlocfilehash: 240a230d09680672ccd5316470a87d047fab9fd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comunes de escalado automático de Azure Monitor
El escalado automático de Azure Monitor le permite escalar verticalmente y reducir horizontalmente el número de instancias en ejecución, basándose en los datos de telemetría (métricas). Este documento describe las métricas comunes que estaría interesado en usar. En Azure Portal de Cloud Services y granjas de servidores, puede elegir la métrica de recurso por la que se va a escalar. Sin embargo, también puede elegir cualquier métrica de un recurso diferente por la que escalar.

El escalado automático de Azure Monitor solo se aplica a los [conjuntos de escalado de máquinas virtuales](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/) y [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/). Otros servicios de Azure usan distintos métodos de escalado.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Cálculo de métricas de máquinas virtuales basadas en Resource Manager
De forma predeterminada, las máquinas virtuales basadas en Resource Manager y los conjuntos de escalado de máquinas virtuales emiten métricas básicas (de nivel de host). Además, al configurar la recopilación de datos de diagnóstico para máquinas virtuales y conjuntos de escalado de máquinas virtuales de Azure, la extensión de diagnóstico de Azure también emite contadores de rendimiento de SO invitado (lo que normalmente se conoce como "métricas de SO invitado").  Todas estas métricas se usan en reglas de escalado automático.

Puede usar la CLI, API o PoSH `Get MetricDefinitions` para ver las métricas disponibles para el recurso VMSS.

Si está utilizando conjuntos de escalado de máquinas virtuales y no ve una métrica concreta enumerada, entonces es probable que esté *deshabilitada* en la extensión Diagnostics.

Si una métrica concreta no se muestrea o se transfiere a la frecuencia que desea, puede actualizar la configuración de diagnóstico.

Si se cumple cualquiera de los casos anteriores, revise [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](../virtual-machines/windows/ps-extensions-diagnostics.md) sobre PowerShell para configurar y actualizar la extensión Diagnostics de máquina virtual de Azure a fin de habilitar la métrica. Ese artículo también incluye un archivo de configuración de diagnósticos de ejemplo.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas de host para máquinas virtuales Windows y Linux basadas en Resource Manager
Las siguientes métricas de nivel de host se emiten de forma predeterminada para máquinas virtuales y conjuntos de escalado de máquinas virtuales de Azure en instancias de Windows y Linux. Estas métricas describen la máquina virtual de Azure, pero se recopilan desde el host de dicha máquina en lugar de hacerlo a través de agente instalado en la máquina virtual invitada. Puede usar estas métricas en reglas de escalado automático.

- [Métricas de host para máquinas virtuales Windows y Linux basadas en Resource Manager](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Métricas de host para conjuntos de escalado de máquinas virtuales Windows y Linux basadas en Resource Manager](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Métricas de SO invitado para máquinas virtuales Windows basadas en Resource Manager
Cuando crea una nueva máquina virtual en Azure, los diagnósticos se habilitan mediante la extensión Diagnósticos. La extensión de diagnósticos emite un conjunto de métricas realizadas desde dentro de la máquina virtual. Esto significa que puede escalar automáticamente fuera de las métricas que no se emiten de forma predeterminada.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede crear una alerta para las siguientes métricas:

| Nombre de métrica | Unidad |
| --- | --- |
| Procesador(_Total)\% Hora del procesador |Percent |
| \Procesador(_Total)\% de tiempo con privilegios |Percent |
| \Procesador(_Total)\% de tiempo de usuario |Percent |
| \Información del procesador(_Total)\Frecuencia del procesador |Recuento |
| \Sistema\Procesos |Recuento |
| \Proceso(_Total)\Número de subprocesos |Recuento |
| \Proceso(_Total)\Número de identificadores |Recuento |
| \Memoria\% de bytes confirmados en uso |Percent |
| \Memoria\Bytes disponibles |Bytes |
| \Memoria\Bytes confirmados |Bytes |
| \Memoria\Límite de confirmación |Bytes |
| \Memoria\Bytes de bloque paginado |Bytes |
| \Memoria\Bytes de bloque no paginado |Bytes |
| \Disco físico(_Total)\% de tiempo de disco |Percent |
| \Disco físico(_Total)\%Tiempo de lectura de disco |Percent |
| \Disco físico(_Total)\% de tiempo de escritura de disco |Percent |
| \Disco físico(_Total)\Transferencias de disco/s |CountPerSecond |
| \Disco físico(_Total)\Lecturas de disco/s |CountPerSecond |
| \Disco físico(_Total)\Escrituras de disco/s |CountPerSecond |
| \Disco físico(_Total)\Bytes de disco/s |BytesPerSecond |
| \Disco físico(_Total)\Bytes de lectura de disco/s |BytesPerSecond |
| \Disco físico(_Total)\Bytes de escritura de disco/s |BytesPerSecond |
| \Disco físico(_Total)\Promedio Longitud de la cola de disco |Recuento |
| \Disco físico(_Total)\Promedio Longitud de la cola de lectura de disco |Recuento |
| \Disco físico(_Total)\Promedio Longitud de la cola de escritura de disco |Recuento |
| \Disco lógico(_Total)\% de espacio disponible |Percent |
| \Disco lógico(_Total)\Megabytes disponibles |Recuento |

### <a name="guest-os-metrics-linux-vms"></a>Métricas de SO invitado para máquinas virtuales Linux
Cuando crea una máquina virtual en Azure, los diagnósticos se habilitan de forma predeterminada mediante la extensión Diagnósticos.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Puede crear una alerta para las siguientes métricas:

| Nombre de métrica | Unidad |
| --- | --- |
| \Memory\AvailableMemory |Bytes |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |Bytes |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Segundos |
| \PhysicalDisk\AverageWriteTime |Segundos |
| \PhysicalDisk\AverageTransferTime |Segundos |
| \PhysicalDisk\AverageDiskQueueLength |Recuento |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |Recuento |
| \NetworkInterface\PacketsReceived |Recuento |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Recuento |
| \NetworkInterface\TotalTxErrors |Recuento |
| \NetworkInterface\TotalCollisions |Recuento |

## <a name="commonly-used-web-server-farm-metrics"></a>Métricas web comúnmente usadas (granja de servidores)
También puede realizar el escalado automático basándose en métricas de servidor web comunes, como la longitud de cola Http. El nombre de la métrica es **HttpQueueLength**.  En la siguiente sección se muestran las métricas de granja de servidores (Aplicaciones web) disponibles.

### <a name="web-apps-metrics"></a>Métricas de Aplicaciones web
Puede generar una lista de las métricas de Aplicaciones web mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede alertar sobre estas métricas o escalar por las mismas.

| Nombre de métrica | Unidad |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Recuento |
| HttpQueueLength |Recuento |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas de almacenamiento utilizadas comúnmente
Puede escalar por la longitud de la cola de almacenamiento, que es el número de mensajes de dicha cola. La longitud de cola de almacenamiento es una métrica especial y el umbral es el número de mensajes por instancia. Por ejemplo, si hay dos instancias y el umbral está establecido en 100, el escalado se produce cuando el número total de mensajes de la cola es 200. Esto puede ser 100 mensajes por instancia, 120 y 80, o cualquier combinación que sume hasta 200 o más.

Esta configuración la puede realizar en Azure Portal, en la hoja **Configuración**. Para los conjuntos de escalado de máquinas virtuales, puede actualizar la configuración de escalado automático en la plantilla de Resource Manager para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.

Por ejemplo, con una cuenta de almacenamiento clásico el parámetro de escalado automático metricTrigger incluiría lo siguiente:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

En el caso de una cuenta de almacenamiento (no clásica) el metricTrigger incluiría lo siguiente:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas más usadas de Bus de servicio
Puede escalar por la longitud de la cola de Bus de servicio, que es el número de mensajes de dicha cola. La longitud de cola de Service Bus es una métrica especial y el umbral es el número de mensajes por instancia. Por ejemplo, si hay dos instancias y el umbral está establecido en 100, el escalado se produce cuando el número total de mensajes de la cola es 200. Esto puede ser 100 mensajes por instancia, 120 y 80, o cualquier combinación que sume hasta 200 o más.

Para los conjuntos de escalado de máquinas virtuales, puede actualizar la configuración de escalado automático en la plantilla de Resource Manager para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para Bus de servicio, el concepto de grupo de recursos no existe pero Azure Resource Manager crea un grupo de recursos predeterminado por región. El grupo de recursos suele tener el formato 'Default-ServiceBus-[región]'. Por ejemplo, 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast', etc.
>
>
