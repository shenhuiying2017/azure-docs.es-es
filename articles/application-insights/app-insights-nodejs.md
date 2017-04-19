---
title: "Supervisión de la aplicación de Node.js con Azure Application Insights | Microsoft Docs"
description: "Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 310ada88bb4d9b39eeaa10f303b9e1bd3b1f927f
ms.lasthandoff: 04/13/2017


---
# <a name="add-application-insights-sdk-to-monitor-your-nodejs-app"></a>Incorporación del SDK de Application Insights para supervisar la aplicación de Node.js


[Azure Application Insights](app-insights-overview.md) supervisa la aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento](app-insights-detect-triage-diagnose.md), y [descubrir cómo se usa la aplicación](app-insights-web-track-usage.md). Funciona con las aplicaciones hospedadas en máquinas virtuales de Azure o en sus servidores IIS locales propios, así como con las aplicaciones web de Azure.

El SDK proporciona la recopilación automática de las tasas de solicitudes HTTP entrantes y las respuestas, los contadores de rendimiento (CPU, memoria, RPS) y las excepciones no controladas. Además, puede agregar llamadas personalizadas para realizar un seguimiento de las dependencias, métricas u otros eventos.

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-nodejs/10-perf.png)

#### <a name="before-you-start"></a>Antes de comenzar
Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).

## <a name="add"></a>Creación de recursos en Application Insights
Inicie sesión en [Azure Portal][portal] y cree un nuevo recurso de Application Insights. Un [recurso][roles] de Azure es una instancia de un servicio. Este recurso es donde se le presentará telemetría de su aplicación analizada.

![Haga clic en Nuevo, Application Insights.](./media/app-insights-nodejs/01-new-asp.png)

Elija General como el tipo de aplicación. La elección del tipo de aplicación establece el contenido predeterminado de las hojas de recursos y las propiedades que estarán visibles en el [Explorador de métricas][metrics].

#### <a name="copy-the-instrumentation-key"></a>Copia de la clave de instrumentación
La clave identifica al recurso y se instalará pronto en el SDK para dirigir los datos al recurso.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-nodejs/02-props-asp.png)

## <a name="sdk"></a> Instale el SDK en su aplicación.
```
npm install applicationinsights --save
```

## <a name="usage"></a>Uso
Esto permitirá la supervisión de solicitudes, el seguimiento de excepciones no controladas y la supervisión del rendimiento del sistema (CPU/memoria/RPS).

```javascript

var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

La clave de instrumentación también se puede establecer en la variable de entorno APPINSIGHTS_INSTRUMENTATIONKEY. Si se hace esto, no se requiere ningún argumento cuando se llama a `appInsights.setup()` o `appInsights.getClient()`.

Puede probar el SDK sin enviar datos de telemetría: establezca la clave de instrumentación como una cadena no vacía.

## <a name="run"></a> Ejecución del proyecto
Ejecute la aplicación y pruébela: abra varias páginas para generar datos de telemetría.

## <a name="monitor"></a> Visualización de los datos de telemetría
Vuelva al [Portal de Azure](https://portal.azure.com) y busque el recurso de Application Insights.

Busque los datos en la página Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-nodejs/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas.][perf]

#### <a name="no-data"></a>¿No hay datos?
* Use la aplicación y abra varias páginas para generar telemetría.
* Abra el icono [Buscar](app-insights-diagnostic-search.md) para ver los eventos individuales. A veces, los eventos tardan un poco en llegar a través de la canalización de métricas.
* Espere unos segundos y haga clic en **Actualizar**. Los gráficos se actualizan automáticamente de forma periódica, pero puede actualizarlos manualmente si espera que aparezcan algunos datos.
* Consulte [Solución de problemas][qna].

## <a name="publish-your-app"></a>Publicación de la aplicación
Implemente ahora la aplicación en IIS o en Azure y observe cómo se acumulan los datos.

#### <a name="no-data-after-you-publish-to-your-server"></a>¿No hay datos después de publicar en el servidor?
Asegúrese de que [están abiertos los puertos del firewall necesarios](app-insights-ip-addresses.md).

#### <a name="trouble-on-your-build-server"></a>¿Tiene problemas el servidor de compilación?
Consulte [este apartado de la solución de problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

## <a name="customized-usage"></a>Uso personalizado
### <a name="disabling-auto-collection"></a>Deshabilitar la recopilación automática
```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### <a name="custom-monitoring"></a>Supervisión personalizada
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[Más información acerca de la API de telemetría](app-insights-api-custom-events-metrics.md).

### <a name="using-multiple-instrumentation-keys"></a>Uso de múltiples claves de instrumentación
```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## <a name="examples"></a>Ejemplos
### <a name="tracking-dependency"></a>Seguimiento de dependencia
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### <a name="manual-request-tracking-of-all-get-requests"></a>Seguimiento de solicitud manual de todas las solicitudes "GET"
```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Pasos siguientes
* [Navegación y paneles en el portal de Application Insights](app-insights-dashboards.md)
* [Escritura de consultas de Analytics sobre los datos de telemetría](app-insights-analytics-tour.md)

<!--Link references-->

[knowUsers]: app-insights-web-track-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md

