---
title: "Modelo de datos de telemetría de Azure Application Insights: telemetría de seguimientos | Microsoft Docs"
description: "Modelo de datos de Application Insights para la telemetría de seguimientos"
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
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 178b1ec0edce5a3fb20dc5c591e12cfe6e721cf2
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetría de seguimientos: modelo de datos de Application Insights

La telemetría de seguimientos (en [Application Insights](app-insights-overview.md)) representa instrucciones de seguimiento de estilo `printf` en las que se pueden realizar búsquedas de texto. `Log4Net`, `NLog` y las demás entradas de archivo de registro basadas en texto se convierten a instancias de este tipo. El seguimiento no tiene medidas como una extensibilidad.

## <a name="message"></a>Message

Mensaje de seguimiento.

Longitud máxima: 32 768 caracteres

## <a name="severity-level"></a>Nivel de gravedad

El nivel de gravedad del seguimiento. El valor puede ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Exploración de los registros de seguimiento de .NET en Application Insights](app-insights-asp-net-trace-logs.md).
- [Exploración de los registros de seguimiento de Java en Application Insights](app-insights-java-trace-logs.md).
- Consulte el [modelo de datos](application-insights-data-model.md) para ver los tipos y el modelo de datos de Application Insights.
- [Escritura de telemetría de seguimiento personalizada](app-insights-api-custom-events-metrics.md#tracktrace)
- Consulte las [plataformas](app-insights-platforms.md) compatibles con Application Insights.

