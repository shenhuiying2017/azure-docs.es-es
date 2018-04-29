---
title: Migración de las métricas de Azure Storage | Microsoft Docs
description: Obtenga información sobre cómo migrar las métricas heredadas a las métricas nuevas que Azure Monitor administra.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Migración de las métricas de Azure Storage

Alineado con la estrategia de unificar la experiencia de Monitor en Azure, Azure Storage incorporar métricas en la plataforma de Azure Monitor. En el futuro, finalizará el servicio de las métricas heredadas con un aviso por adelantado en función de la directiva de Azure. Si usa las métricas de almacenamiento heredadas, debe migrarlas antes de la fecha de finalización del servicio para mantener su información de métricas.

Este artículo lo guía en la migración de métricas heredadas a las métricas nuevas.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Descripción de las métricas heredadas que administra Azure Storage

Azure Storage recopila valores de métricas heredadas, las agrega y almacena en tablas $Metric dentro de la misma cuenta de almacenamiento. Puede usar Azure Portal para configurar el gráfico de supervisión y también puede usar Azure Storage SDK para leer los datos de las tablas $Metric según el esquema. Para más detalles, puede consultar [Storage Analytics](./storage-analytics.md).

Las métricas heredadas proporcionan métricas de capacidad solo en Blob y métricas de transacción en Blob, Table, File y Queue.

Las métricas heredadas están diseñadas en un esquema sin formato. El diseño genera un valor de métrica de cero cuando los patrones de tráfico no desencadenan la métrica. Por ejemplo, **ServerTimeoutError** se establece en 0 en las tablas $Metric incluso cuando no se recibe ningún error de tiempo de expiración del servidor a partir del tráfico en vivo a la cuenta de almacenamiento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Descripción de las métricas nuevas que administra Azure Monitor

En el caso de las métricas de almacenamiento nuevas, Azure Storage emite los datos de métrica al back-end de Azure Monitor. Azure Monitor proporciona una experiencia de supervisión unificada, incluidos los datos del portal, además de la ingesta de datos. Para más detalles, puede consultar este [artículo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Las métricas nuevas proporcionan métricas de capacidad y métricas de transacción en Blob, Table, File, Queue y Premium Storage.

La multidimensionalidad es una de las características que Azure Monitor proporciona. Azure Storage adopta el diseño en la definición del nuevo esquema de métricas. Para las dimensiones compatibles con las métricas, puede encontrar detalles en [Métricas de Azure Storage en Azure Monitor](./storage-metrics-in-azure-monitor.md). El diseño multidimensional brinda rentabilidad tanto en el ancho de banda de la ingesta y la capacidad de almacenamiento de métricas. Por lo tanto, si el tráfico no ha desencadenado métricas relacionadas, no se generarán los datos de métricas relacionados. Por ejemplo, si el tráfico no desencadenó ningún error de tiempo de expiración de servidor, Azure Monitor no devuelve datos cuando se consulta el valor de la métrica **Transactions** con la dimensión **ResponseType** equivalente a **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Asignación de métricas entre métricas heredadas y métricas nuevas

Si lee los datos de métricas mediante programación, debe adoptar el esquema de métricas nuevo en sus programas. Para entender mejor los cambios, puede consultar la asignación que aparece en la tabla siguiente:

**Métricas de capacidad**

| Métrica heredada | Métrica nueva |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity con dimensión BlobType equivalente a BlockBlob o PageBlob |
| ObjectCount         | BlobCount con dimensión BlobType equivalente a BlockBlob o PageBlob |
| ContainerCount      | ContainerCount |

Las métricas siguientes son ofertas nuevas no compatibles con las métricas heredadas:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Métricas de transacción**

| Métrica heredada | Métrica nueva |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transacciones con dimensión ResponseType equivalente a AuthorizationError |
| AnonymousClientOtherError | Transacciones con dimensión ResponseType equivalente a ClientOtherError |
| AnonymousClientTimeoutError | Transacciones con dimensión ResponseType equivalente a ClientTimeoutError |
| AnonymousNetworkError | Transacciones con dimensión ResponseType equivalente a NetworkError |
| AnonymousServerOtherError | Transacciones con dimensión ResponseType equivalente a ServerOtherError |
| AnonymousServerTimeoutError | Transacciones con dimensión ResponseType equivalente a ServerTimeoutError |
| AnonymousSuccess | Transacciones con dimensión ResponseType equivalente a Success |
| AnonymousThrottlingError | Transacciones con dimensión ResponseType equivalente a ClientThrottlingError o ServerBusyError |
| AuthorizationError | Transacciones con dimensión ResponseType equivalente a AuthorizationError |
| Disponibilidad | Disponibilidad |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transacciones con dimensión ResponseType equivalente a ClientOtherError |
| ClientTimeoutError | Transacciones con dimensión ResponseType equivalente a ClientTimeoutError |
| NetworkError | Transacciones con dimensión ResponseType equivalente a NetworkError |
| PercentAuthorizationError | Transacciones con dimensión ResponseType equivalente a AuthorizationError |
| PercentClientOtherError | Transacciones con dimensión ResponseType equivalente a ClientOtherError |
| PercentNetworkError | Transacciones con dimensión ResponseType equivalente a NetworkError |
| PercentServerOtherError | Transacciones con dimensión ResponseType equivalente a ServerOtherError |
| PercentSuccess | Transacciones con dimensión ResponseType equivalente a Success |
| PercentThrottlingError | Transacciones con dimensión ResponseType equivalente a ClientThrottlingError o ServerBusyError |
| PercentTimeoutError | Transacciones con dimensión ResponseType equivalente a ServerTimeoutError o ResponseType equivalente a ClientTimeoutError|
| SASAuthorizationError | Transacciones con dimensión ResponseType equivalente a AuthorizationError |
| SASClientOtherError | Transacciones con dimensión ResponseType equivalente a ClientOtherError |
| SASClientTimeoutError | Transacciones con dimensión ResponseType equivalente a ClientTimeoutError |
| SASNetworkError | Transacciones con dimensión ResponseType equivalente a NetworkError |
| SASServerOtherError | Transacciones con dimensión ResponseType equivalente a ServerOtherError |
| SASServerTimeoutError | Transacciones con dimensión ResponseType equivalente a ServerTimeoutError |
| SASSuccess | Transacciones con dimensión ResponseType equivalente a Success |
| SASThrottlingError | Transacciones con dimensión ResponseType equivalente a ClientThrottlingError o ServerBusyError |
| ServerOtherError | Transacciones con dimensión ResponseType equivalente a ServerOtherError |
| ServerTimeoutError | Transacciones con dimensión ResponseType equivalente a ServerTimeoutError |
| Correcto | Transacciones con dimensión ResponseType equivalente a Success |
| ThrottlingError | Transacciones con dimensión ResponseType equivalente a ClientThrottlingError o ServerBusyError |
| TotalBillableRequests | Transacciones |
| TotalEgress | Salida |
| TotalIngress | Entrada |
| TotalRequests | Transacciones |

## <a name="faq"></a>Preguntas más frecuentes

* ¿Cómo se deben migrar las reglas de alertas existentes?

R: Si creó reglas de alertas clásicas en función de métricas de almacenamiento heredadas, debe crear reglas de alertas nuevas según el esquema de métricas nuevo.

* ¿Los datos de métricas nuevas se almacenarán en la misma cuenta de almacenamiento de manera predeterminada?

R.: No. Si tiene que archivar los datos de métricas en la cuenta de almacenamiento, puede usar la [configuración de diagnóstico en Azure Monitor](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/).

## <a name="next-steps"></a>Pasos siguientes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de almacenamiento en Azure Monitor](./storage-metrics-in-azure-monitor.md)
