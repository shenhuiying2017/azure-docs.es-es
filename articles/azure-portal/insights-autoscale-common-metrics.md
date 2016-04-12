<properties
	pageTitle="Azure Insights: Métricas comunes de escalado automático de Azure Insights. | Microsoft Azure"
	description="Aprenda qué métricas se utilizan normalmente para el escalado automático de Servicios en la nube, Máquinas virtuales y Aplicaciones web."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Métricas comunes de escalado automático de Azure Insights

El escalado automático de Azure Insights le permite escalar verticalmente y reducir horizontalmente el número de instancias en ejecución, basándose en los datos de telemetría (métricas). Este documento describe las métricas comunes que estaría interesado en usar. En el Portal de Azure para Servicios en la nube y granjas de servidores, puede elegir la métrica de recurso por la que se va a escalar. Sin embargo, también puede elegir cualquier métrica de un recurso diferente por la que escalar.

Estos son los detalles sobre cómo buscar y enumerar las métricas por las que desea escalar. Lo siguiente se aplica también para escalar conjuntos de escalado de máquina virtual.

## Cálculo de métricas
De forma predeterminada, VM v2 de Azure incluye una extensión Diagnostics configurada con las siguientes métricas activadas.

- [Métricas de invitado para VM v2 con Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métricas de invitado para VM v2 con Linux](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Puede usar la CLI, API o PoSH `Get MetricDefinitions` para ver las métricas disponibles para el recurso VMSS.

Si está utilizando conjuntos de escalado de VM y no ve una métrica concreta enumerada, entonces es probable que esté *deshabilitada* en la extensión Diagnostics.

Si una métrica concreta no se muestrea o se transfiere a la frecuencia que desea, puede actualizar la configuración de diagnóstico.

Si se cumple cualquiera de los casos anteriores, revise [Use PowerShell to enable Azure Diagnostics in a virtual machine running Windows (Usar PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows)](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) sobre PowerShell para configurar y actualizar la extensión Diagnostics de VM de Azure para habilitar la métrica. Ese artículo también incluye un archivo de configuración de diagnósticos de ejemplo.

### Cálculo de métricas para VM v2 con Windows como un SO invitado

Cuando crea una nueva VM (v2) en Azure, los diagnósticos se habilitan mediante la extensión Diagnostics.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede crear una alerta para las siguientes métricas.

|Nombre de métrica|	Unidad|
|---|---|
|\\Processor(\_Total)\\% \\ % de tiempo de procesador | PERCENT| |\\Processor (\_Total) \\ % de tiempo privilegiado | PERCENT| |\\Processor (\_Total) \\ % de tiempo de usuario | Frecuencia de Percent| |\\Processor información (\_Total) \\Processor | Count| |\\System\\Processes| Count| |\\Process (\_Total) \\Thread Count| Recuento de Count| |\\Process (\_Total) \\Handle | Count| |\\Memory\\% Bytes confirmados en uso | PERCENT| |\\Memory\\Available Bytes| Bytes de |\\Memory\\Committed bytes| | Bytes| |\\Memory\\Commit Limit| Bytes| |\\Memory\\Pool paginado Bytes| Bytes| |\\Memory\\Pool Bytes| de bloque no paginado Bytes| |\\PhysicalDisk (\_Total) \\ % Time| de disco PERCENT| |\\PhysicalDisk (\_Total) \\ Time| de lectura de disco de % PERCENT| |\\PhysicalDisk (\_Total) \\ Time| de escritura en disco % PERCENT| |\\PhysicalDisk (\_Total) \\Disk de s. | CountPerSecond| |\\PhysicalDisk (\_Total) \\Disk/seg. | CountPerSecond| |\\PhysicalDisk (\_Total) \\Disk/seg. | CountPerSecond| |\\PhysicalDisk (\_Total) \\Disk Bytes/seg. | BytesPerSecond| |\\PhysicalDisk (\_Total) \\Disk Bytes de lectura/sec| BytesPerSecond| |\\PhysicalDisk (\_Total) \\Disk Bytes escritos/s | BytesPerSecond| |\\PhysicalDisk (\_Total) \\Avg. Disk Queue Length| Count| |\\PhysicalDisk(\_Total)\\Avg. Disk Read Queue Length| Count| |\\PhysicalDisk(\_Total)\\Avg. Longitud de cola de escritura en disco | Count| |\\LogicalDisk (\_Total) \\ % libre Space| PERCENT| |\\LogicalDisk (\_Total) \\Free Megabytes| Count|



### Cálculo de métricas para VM v2 con Linux como un SO invitado

Cuando crea una nueva VM (v2) en Azure, los diagnósticos se habilitan de forma predeterminada mediante la extensión Diagnostics.

Puede generar una lista de las métricas mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Puede crear una alerta para las siguientes métricas.

|Nombre de métrica|	Unidad|
|---|---|
|\\Memory\\AvailableMemory |Bytes|
|\\Memory\\PercentAvailableMemory|	Percent|
|\\Memory\\UsedMemory|	Bytes|
|\\Memory\\PercentUsedMemory|	Percent|
|\\Memory\\PercentUsedByCache |Percent|
|\\Memory\\PagesPerSec|	CountPerSecond|
|\\Memory\\PagesReadPerSec|	CountPerSecond|
|\\Memory\\PagesWrittenPerSec |CountPerSecond|
|\\Memory\\AvailableSwap |Bytes|
|\\Memory\\PercentAvailableSwap|	Percent|
|\\Memory\\UsedSwap |Bytes|
|\\Memory\\PercentUsedSwap|	Percent|
|\\Processor\\PercentIdleTime|	Percent|
|\\Processor\\PercentUserTime|	Percent|
|\\Processor\\PercentNiceTime |Percent|
|\\Processor\\PercentPrivilegedTime |Percent|
|\\Processor\\PercentInterruptTime|	Percent|
|\\Processor\\PercentDPCTime|	Percent|
|\\Processor\\PercentProcessorTime |Percent|
|\\Processor\\PercentIOWaitTime |Percent|
|\\PhysicalDisk\\BytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\ReadBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\WriteBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\TransfersPerSecond |CountPerSecond|
|\\PhysicalDisk\\ReadsPerSecond |CountPerSecond|
|\\PhysicalDisk\\WritesPerSecond |CountPerSecond|
|\\PhysicalDisk\\AverageReadTime|	Segundos|
|\\PhysicalDisk\\AverageWriteTime |Segundos|
|\\PhysicalDisk\\AverageTransferTime|	Segundos|
|\\PhysicalDisk\\AverageDiskQueueLength|	Recuento|
|\\NetworkInterface\\BytesTransmitted |Bytes|
|\\NetworkInterface\\BytesReceived |Bytes|
|\\NetworkInterface\\PacketsTransmitted |Recuento|
|\\NetworkInterface\\PacketsReceived |Recuento|
|\\NetworkInterface\\BytesTotal |Bytes|
|\\NetworkInterface\\TotalRxErrors|	Recuento|
|\\NetworkInterface\\TotalTxErrors|	Recuento|
|\\NetworkInterface\\TotalCollisions|	Recuento|




## Métricas web comúnmente usadas (granja de servidores)

También puede realizar el escalado automático basándose en métricas de servidor web comunes, como la longitud de cola Http. Su nombre de métrica **HttpQueueLength**. En la siguiente sección se muestran las métricas de granja de servidores (Aplicaciones web) disponibles.

### Métricas de Aplicaciones web

Puede generar una lista de las métricas de Aplicaciones web mediante el siguiente comando en PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Puede alertar sobre estas métricas o escalar por las mismas.

|Nombre de métrica|	Unidad|
|---|---|
|CpuPercentage|	Percent|
|MemoryPercentage |Percent|
|DiskQueueLength|	Recuento|
|HttpQueueLength|	Recuento|
|BytesReceived|	Bytes|
|BytesSent|	Bytes|


## Métricas de almacenamiento utilizadas comúnmente
Puede escalar por la longitud de la cola de almacenamiento, que es el número de mensajes de dicha cola. La longitud de cola de almacenamiento es una métrica especial y el umbral aplicado será el número de mensajes por instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escalará cuando el número total de mensajes de la cola sea 200. Por ejemplo, 100 mensajes por instancia.

Puede configurar esto en el Portal de Azure en la hoja **Configuración**: Para los conjuntos de escalado de VM, puede actualizar la configuración Escalado automático en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## Métricas más usadas de Bus de servicio

Puede escalar por la longitud de la cola de Bus de servicio, que es el número de mensajes de dicha cola. La longitud de cola de Bus de servicio es una métrica especial y el umbral aplicado será el número de mensajes por instancia. Esto significa que si hay dos instancias y si el umbral se establece en 100, se escalará cuando el número total de mensajes de la cola sea 200. Por ejemplo, 100 mensajes por instancia.

Para los conjuntos de escala de la máquina virtual, puede actualizar la configuración Escalado automático en la plantilla ARM para usar *metricName* como *ApproximateMessageCount* y pasar el identificador de la cola de almacenamiento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Para Bus de servicio, el concepto de grupo de recursos no existe pero Azure Resource Manager crea un grupo de recursos predeterminado por región. El grupo de recursos suele tener el formato 'Default-ServiceBus-[región]'. Por ejemplo, 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast', etc.

<!---HONumber=AcomDC_0330_2016-->