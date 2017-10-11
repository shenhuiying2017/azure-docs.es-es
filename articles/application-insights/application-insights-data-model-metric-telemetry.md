---
title: "Modelo de datos de telemetría de Azure Application Insights: telemetría de métricas | Microsoft Docs"
description: "Modelo de datos de Application Insights para la telemetría de métricas"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: 42e55db4f932de85ee1a71c408b889e0ff9fe3e1
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetría de métricas: modelo de datos de Application Insights

Hay dos tipos de telemetría de métricas compatibles con [Application Insights](app-insights-overview.md): la medida única y la métrica previamente agregada. La medida única es simplemente un nombre y un valor. La métrica previamente agregada especifica el valor mínimo y máximo de la métrica en el intervalo de agregación y la desviación estándar del mismo.

La telemetría de métricas previamente agregadas da por supuesto que el período de agregación es de un minuto.

Hay varios nombres de métrica conocidos compatibles con Application Insights. 

Métricas que representan los contadores del sistema y de procesos:

| **Nombre de .NET**             | **Nombre independiente de la plataforma** | **Nombre de API DE REST** | **Descripción**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Trabajo en curso... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU total del equipo
| `\Memory\Available Bytes`                 | Trabajo en curso... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | memoria disponible en disco
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Trabajo en curso... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU del proceso que hospeda la aplicación
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Trabajo en curso... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memoria que usa el proceso que hospeda la aplicación
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Trabajo en curso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | tasa de operaciones de E/S ejecutadas por el proceso que hospeda la aplicación
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Trabajo en curso... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | tasa de solicitudes procesadas por la aplicación 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Trabajo en curso... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | velocidad de las excepciones producidas por la aplicación
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Trabajo en curso... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tiempo medio de ejecución de solicitudes
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Trabajo en curso... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | número de peticiones en espera de procesamiento en una cola

## <a name="name"></a>Nombre

El nombre de la métrica que le gustaría ver en el portal de Application Insights y la interfaz de usuario. 

## <a name="value"></a>Valor

Valor único para la medida. Suma de las mediciones individuales para la agregación.

## <a name="count"></a>Recuento

Peso de la métrica agregada. No se debe establecer para una medida.

## <a name="min"></a>Min

Valor mínimo de la métrica agregada. No se debe establecer para una medida.

## <a name="max"></a>max

Valor máximo de la métrica agregada. No se debe establecer para una medida.

## <a name="standard-deviation"></a>Desviación estándar

Desviación estándar de la métrica agregada. No se debe establecer para una medida.

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo usar la [API de Application Insights para eventos y métricas personalizados](app-insights-api-custom-events-metrics.md#trackmetric).
- Consulte el [modelo de datos](application-insights-data-model.md) para ver los tipos y el modelo de datos de Application Insights.
- Consulte las [plataformas](app-insights-platforms.md) compatibles con Application Insights.
