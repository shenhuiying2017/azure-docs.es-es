---
title: 'Modelo de datos de telemetría de Azure Application Insights: telemetría de seguimientos | Microsoft Docs'
description: Modelo de datos de Application Insights para la telemetría de seguimientos
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: d93ed9f292b6c05d0a3fb3202567f4024f62e35e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
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
- Consulte [modelo de datos](application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- [Escritura de telemetría de seguimiento personalizada](app-insights-api-custom-events-metrics.md#tracktrace)
- Consulte las [plataformas](app-insights-platforms.md) compatibles con Application Insights.
