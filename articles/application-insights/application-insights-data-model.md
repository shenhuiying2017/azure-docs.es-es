---
title: "Modelo de datos de telemetría de Azure Application Insights | Microsoft Docs"
description: "Información general del modelo de datos de Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Modelo de datos de telemetría de Application Insights

Application Insights define el modelo de datos de telemetría para Application Performance Management (APM). Este modelo normaliza la recopilación de datos y permite la creación de escenarios de supervisión independientes de la plataforma y el lenguaje. Los datos recopilados por Application Insights modelan el patrón de ejecución de aplicaciones típico:

![Modelo de aplicación de Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Hay dos tipos de aplicaciones: las aplicaciones con un punto de conexión que reciben ***solicitudes*** externas (las aplicaciones web) y las aplicaciones que "se despiertan" periódicamente para procesar los datos almacenados en alguna parte (WebJobs o funciones). En ambos casos, la ejecución única se denomina ***operación***. La operación se realiza de manera correcta o incorrecta través de una ***excepción*** o es posible que dependa de otro servicios o almacenamiento para transportar su lógica de negocios. Para reflejar estos conceptos, el modelo de datos de Application Insights define tres tipos de telemetría: [solicitud](./application-insights-data-model-request-telemetry.md), [excepción](/application-insights-data-model-exception-telemetry.md) y [dependencia](/application-insights-data-model-dependency-telemetry.md).

Por lo general, estos tipos se definen mediante el marco de aplicación y se recopilan automáticamente mediante el SDK. `ASP.NET MVC` define la noción de ejecución de solicitud en su estructura modelo-vista-controlador: marca el inicio y la detención de una solicitud. Las llamadas de dependencia a SQL se definen mediante `System.Data`. Las llamadas a los puntos de conexión HTTP se definen mediante `System.Net`. Se pueden ampliar los tipos de telemetría recopilados por plataformas y marcos específicos mediante el uso de propiedades y medidas personalizadas. No obstante, hay casos en los que querrá telemetría de informe personalizada. Es posible que quiera implementar el registro de diagnósticos mediante un marco de instrumentación conocido, como `Log4Net` o `System.Diagnostics`. O bien que necesite capturar la interacción del usuario con el servicio para analizar los patrones de uso. Application Insights reconoce tres tipos de datos adicionales: [seguimiento](/application-insights-data-model-trace-telemetry.md), [evento](/application-insights-data-model-event-telemetry.md) y [métrica](/application-insights-data-model-metric-telemetry.md) para modelar estos escenarios.

El modelo de telemetría de Application Insights define la forma de [correlacionar](/correlation.md) la telemetría con la operación de la que es una parte. Por ejemplo, una solicitud puede realizar llamadas de SQL Database y registrar información de diagnóstico. Puede establecer el contexto de correlación de los elementos de telemetría que lo vincularán a la telemetría de solicitudes.

## <a name="schema-improvements"></a>Mejoras de esquema

El modelo de datos de Application Insights es una manera sencilla y básica pero eficaz de modelar la telemetría de la aplicación. Nos esforzamos para que el modelo sea simple y compacto para admitir escenarios esenciales y permitir extender el esquema para el uso avanzado.

Para informar de problemas del esquema o del modelo de datos y proporcionar sugerencias, use el repositorio [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) de GitHub.

## <a name="next-steps"></a>Pasos siguientes

- Consulte las [plataformas](/app-insights-platforms.md) compatibles con Application Insights.
- Obtenga información sobre cómo [ampliar y filtrar la telemetría](/app-insights-api-filtering-sampling.md).
- Use el [muestreo](/app-insights-sampling.md) para minimizar la cantidad de telemetría en función del modelo de datos.

