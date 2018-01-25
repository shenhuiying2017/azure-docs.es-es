---
title: "Métricas de Azure Relay en Azure Monitor (versión preliminar) | Microsoft Docs"
description: Use Azure Monitor para supervisar Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: dd5bc0dd0088856954e06d880f2c03f0a74ed9d7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas de Azure Relay en Azure Monitor (versión preliminar)

Las métricas de Azure Relay le permiten conocer el estado de los recursos de su suscripción de Azure. Con un amplio conjunto de datos de métricas, puede evaluar el estado general de los recursos de Relay, no solo en el nivel de espacio de nombres, sino también en el nivel de entidad. Estas estadísticas pueden ser importantes, ya que le ayudan a supervisar el estado de Azure Relay. Las métricas también pueden ayudarle a solucionar problemas de causa principal sin necesidad de ponerse en contacto con el soporte técnico de Azure.

Azure Monitor proporciona interfaces de usuario unificadas para la supervisión de distintos servicios de Azure. Para obtener más información, vea [Supervisión de Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) y el ejemplo [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (Recuperación de métricas de Azure Monitor con .NET) en GitHub.

## <a name="access-metrics"></a>Acceso a la métrica

Azure Monitor proporciona varias maneras de tener acceso a las métricas. Puede acceder a las métricas desde [Azure Portal](https://portal.azure.com), o usar las API de Azure Monitor (REST y .NET) y soluciones de análisis como Operation Management Suite y Event Hubs. Para obtener más información, vea [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

De forma predeterminada, las métricas están habilitadas y puede acceder a datos de los últimos 30 días. Si es necesario conservar los datos durante un periodo mayor, se pueden archivar en una cuenta de Azure Storage. Esto se configura en la [configuración de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) de Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acceso a métricas del portal

Una vez transcurrido un tiempo, las métricas se pueden supervisar en [Azure Portal](https://portal.azure.com). En el ejemplo siguiente se muestra cómo ver las solicitudes correctas y las solicitudes entrantes en el nivel de cuenta:

![][1]

También puede tener acceso a las métricas directamente a través del espacio de nombres. Para ello, seleccione el espacio de nombres y, después, haga clic en **Metrics (Preview)** (Métricas [Versión preliminar]). 

Para ver métricas que admitan las dimensiones, debe filtrar por el valor de la dimensión que desee.

## <a name="billing"></a>Facturación

Actualmente, el uso de métricas en Azure Monitor es gratuito, ya que se trata de una versión preliminar. Sin embargo, si usa otras soluciones que ingieren datos de métricas, puede que se le facturen dichas soluciones. Por ejemplo, se le facturará por Azure Storage si archiva datos de métricas en una cuenta de Azure Storage. También se le facturará por Operation Management Suite (OMS) si transmite datos de métricas a OMS para realizar análisis avanzados.

Las siguientes métricas ofrecen una visión general del estado de su servicio. 

> [!NOTE]
> Se están dejando de usar algunas métricas a medida que se mueven a otro nombre. Esto podría requerir que actualice las referencias. Las métricas que se marquen con la palabra clave "en desuso" no se admitirán en el futuro.

Los valores de las métricas se envían a Azure Monitor cada minuto. La granularidad de tiempo define el intervalo de tiempo en el que se presentan los valores de las métricas. El intervalo de tiempo compatible para todas las métricas de Azure Relay es 1 minuto.

## <a name="connection-metrics"></a>Métricas de conexión

| Nombre de métrica | DESCRIPCIÓN |
| ------------------- | ----------------- |
| ListenerConnections-Success (versión preliminar) | Número de conexiones correctas del agente de escucha a Azure Relay durante un determinado periodo. <br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ListenerConnections-ClientError (versión preliminar)|Número de errores de cliente en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ListenerConnections-ServerError (versión preliminar)|Número de errores del servidor en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|SenderConnections-Success (versión preliminar)|Número de conexiones correctas del remitente durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|SenderConnections-ClientError (versión preliminar)|Número de errores de cliente en las conexiones del remitente durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|SenderConnections-ServerError (versión preliminar)|Número de errores del servidor en las conexiones del remitente durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ListenerConnections-TotalRequests (versión preliminar)|Número total de errores en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|SenderConnections-TotalRequests (versión preliminar)|Solicitudes de conexión realizadas por los remitentes durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ActiveConnections (versión preliminar)|Número de conexiones activas durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ActiveListeners (versión preliminar)|Número de agentes de escucha activos durante un determinado periodo.<br/><br/> Unidad: recuento <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|ListenerDisconnects (versión preliminar)|Número de agentes de escucha desconectados durante un determinado periodo.<br/><br/> Unidad: bytes <br/> Tipo de agregación: total <br/> Dimensión: EntityName|
|SenderDisconnects (versión preliminar)|Número de remitentes desconectados durante un determinado periodo.<br/><br/> Unidad: bytes <br/> Tipo de agregación: total <br/> Dimensión: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de uso de memoria

| Nombre de métrica | DESCRIPCIÓN |
| ------------------- | ----------------- |
|BytesTransferred (versión preliminar)|Número de bytes transferidos durante un determinado periodo.<br/><br/> Unidad: bytes <br/> Tipo de agregación: total <br/> Dimensión: EntityName|

## <a name="metrics-dimensions"></a>Dimensiones de métricas

Azure Relay admite las siguientes dimensiones para las métricas de Azure Monitor. Agregar dimensiones a las métricas es opcional. Si no agrega dimensiones, las métricas se especifican en el nivel de espacio de nombres. 

|Nombre de dimensión|DESCRIPCIÓN|
| ------------------- | ----------------- |
|EntityName| Azure Relay admite entidades de mensajería en el espacio de nombres.|

## <a name="next-steps"></a>pasos siguientes

Vea [Información general sobre la supervisión en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




