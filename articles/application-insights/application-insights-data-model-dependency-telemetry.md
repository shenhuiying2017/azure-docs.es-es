---
title: "Modelo de datos de telemetría de Azure Application Insights: telemetría de dependencias | Microsoft Docs"
description: "Modelo de datos de Application Insights para la telemetría de dependencias"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: mbullwin
ms.openlocfilehash: aa305c30dc358997420be6802d43fa69e45f4a5f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetría de dependencias: modelo de datos de Application Insights

La telemetría de dependencias (en [Application Insights](app-insights-overview.md)) representa una interacción del componente supervisado con un componente remoto como SQL o un punto de conexión HTTP.

## <a name="name"></a>Nombre

Nombre del comando que se inició con esta llamada de dependencia. Valor de cardinalidad bajo. Algunos ejemplos son el nombre del procedimiento almacenado y la plantilla de ruta de acceso de dirección URL.

## <a name="id"></a>ID

Identificador de una instancia de llamada de dependencia. Se usa para la correlación con el elemento de telemetría de solicitud correspondiente a esta llamada de dependencia. Para más información, vea la página de [correlación](application-insights-correlation.md).

## <a name="data"></a>Datos

Comando iniciado por esta llamada de dependencia. Algunos ejemplos son la instrucción SQL y la dirección URL HTTP con todos los parámetros de consulta.

## <a name="type"></a>Tipo

Nombre del tipo de dependencia. Valor de cardinalidad bajo para una agrupación lógica de dependencias y la interpretación de otros campos como commandName y resultCode. Algunos ejemplos son SQL, tabla de Azure y HTTP.

## <a name="target"></a>Destino

Sitio de destino de una llamada de dependencia. Algunos ejemplos son el nombre del servidor y la dirección de host. Para más información, vea la página de [correlación](application-insights-correlation.md).

## <a name="duration"></a>Duración

Duración de la solicitud en formato: `DD.HH:MM:SS.MMMMMM`. Debe ser menor de `1000` días.

## <a name="result-code"></a>Código de resultado

Código de resultado de una llamada de dependencia. Algunos ejemplos son el código de error SQL y el código de estado HTTP.

## <a name="success"></a>Correcto

Indicación de si la llamada es correcta o no.

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Pasos siguientes

- Configure el seguimiento de dependencias para [.NET](app-insights-asp-net-dependencies.md).
- Configure el seguimiento de dependencias para [Java](app-insights-java-agent.md).
- [Escritura de una telemetría de dependencia personalizada](app-insights-api-custom-events-metrics.md#trackdependency)
- Consulte el [modelo de datos](application-insights-data-model.md) para ver los tipos y el modelo de datos de Application Insights.
- Consulte las [plataformas](app-insights-platforms.md) compatibles con Application Insights.
