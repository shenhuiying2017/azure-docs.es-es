---
title: "Correlación de Telemetría de Application Insights | Microsoft Docs"
description: "Correlación de Telemetría de Application Insights"
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
ms.author: mbullwin
ms.openlocfilehash: e821a640d3d75e712c022bd681eb07b83da91911
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlación de Telemetría en Application Insights

En el mundo de los microservicios, todas las operaciones lógicas requieren que se realice el trabajo en diversos componentes del servicio. Cada uno de estos componentes puede supervisarse por separado mediante [Application Insights](app-insights-overview.md). El componente de la aplicación web se comunica con el componente del proveedor de autenticación para validar las credenciales de usuario y con el componente de API a fin de obtener datos para su visualización. El componente de API, a su vez, puede consultar datos de otros servicios, usar componentes de proveedor de la caché e informar al componente de facturación sobre esta llamada. Application Insights admite la correlación de telemetría distribuida. Permite detectar qué componente es responsable de los errores o las degradaciones del rendimiento.

Este artículo explica el modelo de datos utilizado por Application Insights para poner en correlación la telemetría enviada por varios componentes. En él se tratan las técnicas y protocolos de propagación de contexto. También se trata la implementación de los conceptos de correlación en distintos idiomas y plataformas.

## <a name="telemetry-correlation-data-model"></a>Modelo de datos de correlación de telemetría

Application Insights define el [modelo de datos](application-insights-data-model.md) para la correlación de telemetría distribuida. Para asociar la telemetría con la operación lógica, todos los elementos de telemetría tienen el campo de contexto denominado `operation_Id`. Todos los elementos de telemetría comparten este identificador en el seguimiento distribuido. De este modo, aun con la pérdida de telemetría de una sola capa, todavía puede asociar la telemetría notificada por otros componentes.

Normalmente, la operación lógica distribuida consta de un conjunto de operaciones más pequeñas: solicitudes procesadas por uno de los componentes. La [telemetría de solicitudes](application-insights-data-model-request-telemetry.md) define esas operaciones. Cada telemetría de solicitudes cuenta con su propio `id`, que la identifica global y exclusivamente. Además, toda la telemetría (seguimientos, excepciones, etc.) asociada a esta solicitud debe establecer `operation_parentId` en el valor de la solicitud `id`.

La [telemetría de dependencia](application-insights-data-model-dependency-telemetry.md) representa todas las operaciones salientes como, por ejemplo, la llamada HTTP a otro componente. La telemetría de dependencia también define su propio `id`, que es globalmente único. La telemetría de solicitudes, iniciada por esta llamada de dependencia, la usa como `operation_parentId`.

Puede crear la vista de la operación lógica distribuida usando `operation_Id`, `operation_parentId` y `request.id` con `dependency.id`. Esos campos también definen el orden de causalidad de las llamadas de telemetría.

En el entorno de los microservicios, los seguimientos de componentes pueden ir a los distintos almacenamientos. Cada componente puede tener su propia clave de instrumentación en Application Insights. A fin de obtener la telemetría para la operación lógica, es necesario consultar los datos de cada almacenamiento. Si el número de almacenamientos es muy elevado, debe tener una idea de dónde mirar a continuación.

El modelo de datos de Application Insights define dos campos, `request.source` y `dependency.target`, para solucionar este problema. El primer campo identifica el componente que inició la solicitud de dependencia y, el segundo, el componente que devolvió la respuesta de la llamada de dependencia.


## <a name="example"></a>Ejemplo

Tomemos como ejemplo una aplicación COTIZACIONES BURSÁTILES que muestra el precio de mercado actual de una acción con una API externa denominada API DE COTIZACIONES. La aplicación COTIZACIONES BURSÁTILES tiene una página `Stock page` abierta por el explorador web de cliente mediante `GET /Home/Stock`. La aplicación consulta la API de COTIZACIONES BURSÁTILES mediante el uso de una llamada HTTP `GET /api/stock/value`.

Puede analizar la telemetría resultante mediante la ejecución de una consulta:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

En la vista de resultados, observe que todos los elementos de telemetría comparten la raíz `operation_Id`. Al realizarse la llamada ajax desde la página, el nuevo y único id. `qJSXU` se asigna a la telemetría de dependencia y el id. de pageView se usa como `operation_ParentId`. A su vez, la solicitud de servidor utiliza el id. de ajax como `operation_ParentId`, etc.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Ahora, al realizarse la llamada `GET /api/stock/value` a un servicio externo, desea conocer la identidad de ese servidor. De este modo, puede establecer el campo `dependency.target` adecuadamente. Si el servicio externo no admite la supervisión, `target` se establece en el nombre de host del servicio como `stock-prices-api.com`. Sin embargo, si ese servicio se identifica devolviendo un encabezado HTTP predefinido, `target` contiene la identidad de servicio que permite a Application Insights crear un seguimiento distribuido consultando la telemetría de ese servicio. 

## <a name="correlation-headers"></a>Encabezados de correlación

Estamos trabajando en una propuesta de RFC para el [protocolo HTTP de correlación](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Esta propuesta define dos encabezados:

- `Request-Id`: lleva el id. único a nivel global de la llamada
- `Correlation-Context`: lleva la colección de pares nombre-valor de las propiedades de seguimiento distribuidas

El estándar también define dos esquemas de generación `Request-Id`: sin formato y jerárquico. Con el esquema sin formato, hay una clave de `Id` conocida definida para la colección `Correlation-Context`.

Application Insights define la [extensión](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) del protocolo HTTP de correlación. Usa pares nombre-valor `Request-Context` para propagar la colección de propiedades utilizadas por el autor o destinatario de la llamada. El SDK de Application Insights usa este encabezado para establecer los campos `dependency.target` y `request.source`.

## <a name="open-tracing-and-application-insights"></a>Open Tracing y Application Insights

Aspecto de los modelos de datos de [Open Tracing](http://opentracing.io/) y Application Insights 

- `request`, `pageView` se asigna a **Span** con `span.kind = server`
- `dependency` se asigna a **Span** con `span.kind = client`
- `id` de un elemento `request` y `dependency` se asigna a **Span.Id**
- `operation_Id` se asigna a **TraceId**
- `operation_ParentId` se asigna a **Reference** de tipo `ChildOf`

Consulte [modelo de datos](application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.

Consulte [specification](https://github.com/opentracing/specification/blob/master/specification.md) (especificación) y [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) (convenciones_semánticas) para obtener definiciones de los conceptos de Open Tracing.


## <a name="telemetry-correlation-in-net"></a>Correlación de telemetría en .NET

Con el tiempo, .NET ha definido varias formas de poner en correlación la telemetría y registros de diagnósticos. Hay un elemento, `System.Diagnostics.CorrelationManager`, que permite realizar un seguimiento de [LogicalOperationStack y ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` y el seguimiento de eventos para Windows definen el método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` usa [ámbitos de registro](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF y HTTP conectan la propagación del contexto "actual".

Sin embargo, esos métodos no han habilitado la compatibilidad con el seguimiento distribuido automático. `DiagnosticsSource` es una forma de admitir la correlación entre máquinas. Las bibliotecas de .NET admiten DiagnosticsSource y permiten la propagación entre máquinas del contexto de propagación a través del transporte como http.

En la [guía de actividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) de DiagnosticsSource se explican los conceptos básicos de actividades de seguimiento. 

ASP.NET Core 2.0 admite la extracción de encabezados HTTP e inicia la nueva actividad. 

La versión inicial de `System.Net.HttpClient`, `<fill in>`, admite la inserción automática de los encabezados HTTP de correlación y realiza un seguimiento de la llamada HTTP como actividad.

Hay un nuevo módulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) de ASP.NET clásico. En este módulo se implementa la correlación de telemetría mediante DiagnosticsSource. Inicia la actividad en función de los encabezados de solicitud entrantes.  También pone en correlación la telemetría de las diversas fases del procesamiento de solicitudes. Incluso en los casos en que se ejecutan todas las fases del procesamiento de IIS en un subproceso administrado distinto.

La versión inicial del SDK de Application Insights, `2.4.0-beta1`, usa DiagnosticsSource y Activity para recolectar la telemetría y asociarla a la actividad actual. 

## <a name="next-steps"></a>Pasos siguientes

- [Escritura de telemetría personalizada](app-insights-api-custom-events-metrics.md)
- Incorpore todos los componentes del microservicio en Application Insights. Consulte las [plataformas compatibles](app-insights-platforms.md).
- Consulte [modelo de datos](application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- Obtenga información sobre cómo [ampliar y filtrar la telemetría](app-insights-api-filtering-sampling.md).
