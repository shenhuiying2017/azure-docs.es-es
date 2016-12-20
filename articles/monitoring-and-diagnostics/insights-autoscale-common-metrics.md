---
title: "Métricas comunes de escalado automático de Azure Monitor | Microsoft Docs"
description: "Aprenda qué métricas se utilizan normalmente para el escalado automático de Servicios en la nube, Máquinas virtuales y Aplicaciones web."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 8d5f8dd454741f5946d6a2c265ce67808abdac9e


---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comunes de escalado automático de Azure Monitor
El escalado automático de Azure Monitor le permite escalar verticalmente y reducir horizontalmente el número de instancias en ejecución, basándose en los datos de telemetría (métricas). Este documento describe las métricas comunes que estaría interesado en usar. En el Portal de Azure para Servicios en la nube y granjas de servidores, puede elegir la métrica de recurso por la que se va a escalar. Sin embargo, también puede elegir cualquier métrica de un recurso diferente por la que escalar.

Estos son los detalles sobre cómo buscar y enumerar las métricas por las que desea escalar. Lo siguiente se aplica también para escalar conjuntos de escalado de máquina virtual.

## <a name="compute-metrics"></a>Cálculo de métricas
De forma predeterminada, VM v2 de Azure incluye una extensión Diagnostics configurada con las siguientes métricas activadas.

* [Métricas de invitado para VM v2 con Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
* [Métricas de invitado para VM v2 con Linux](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Puede usar la CLI, API o PoSH `Get MetricDefinitions` para ver las métricas disponibles para el recurso VMSS. 

Si está utilizando conjuntos de escalado de máquinas virtuales y no ve una métrica concreta enumerada, entonces es probable que esté *deshabilitada* en la extensión Diagnostics.

Si una métrica concreta no se muestrea o se transfiere a la frecuencia que desea, puede actualizar la configuración de diagnóstico.

Si se cumple cualquiera de los casos anteriores, revise [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sobre PowerShell para configurar y actualizar la extensión Diagnostics de VM de Azure para habilitar la métrica. Ese artículo también incluye un archivo de configuración de diagnósticos de ejemplo.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Cálculo de métricas para VM v2 con Windows como un SO invitado
Cuando crea una nueva VM (v2) en Azure, los diagnósticos se habilitan mediante la extensión Diagnostics.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede crear una alerta para las siguientes métricas.

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

### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Cálculo de métricas para VM v2 con Linux como un SO invitado
Cuando crea una nueva VM (v2) en Azure, los diagnósticos se habilitan de forma predeterminada mediante la extensión Diagnostics.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Puede crear una alerta para las siguientes métricas.

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
Puede escalar por la longitud de la cola de almacenamiento, que es el número de mensajes de dicha cola. La longitud de cola de almacenamiento es una métrica especial y el umbral aplicado será el número de mensajes por instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escalará cuando el número total de mensajes de la cola sea 200. Por ejemplo, 100 mensajes por instancia.

Puede configurar esto en Azure Portal, en la hoja **Configuración**. Para los conjuntos de escalado de máquinas virtuales, puede actualizar la configuración de escalado automático en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.

Por ejemplo, con una cuenta de almacenamiento clásico el parámetro de escalado automático metricTrigger incluiría lo siguiente:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

En el caso de una cuenta de almacenamiento (no clásica) el metricTrigger incluiría lo siguiente:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas más usadas de Bus de servicio
Puede escalar por la longitud de la cola de Bus de servicio, que es el número de mensajes de dicha cola. La longitud de cola de Bus de servicio es una métrica especial y el umbral aplicado será el número de mensajes por instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escalará cuando el número total de mensajes de la cola sea 200. Por ejemplo, 100 mensajes por instancia.

Para los conjuntos de escalado de máquinas virtuales, puede actualizar la configuración de escalado automático en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

> [!NOTE]
> Para Bus de servicio, el concepto de grupo de recursos no existe pero Azure Resource Manager crea un grupo de recursos predeterminado por región. El grupo de recursos suele tener el formato 'Default-ServiceBus-[región]'. Por ejemplo, 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast', etc.
> 
> 




<!--HONumber=Nov16_HO3-->


