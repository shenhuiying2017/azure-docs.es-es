---
title: "Supervisión de servicios de Node.js con Azure Application Insights | Microsoft Docs"
description: Supervise el rendimiento y diagnostique problemas en servicios de Node.js con Application Insights.
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017

---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Supervisión de servicios y aplicaciones de Node.js con Application Insights

[Azure Application Insights](app-insights-overview.md) supervisa los servicios back-end y componentes después de implementarlos para ayudarle a [detectar y diagnosticar rápidamente problemas de rendimiento, entre otros](app-insights-detect-triage-diagnose.md). Puede utilizarlo para servicios de Node.js hospedados en cualquier lugar: su centro de datos, máquinas virtuales de Azure y aplicaciones web, e incluso otras nubes públicas.

Para recibir, almacenar y explorar los datos de supervisión, siga estas instrucciones para incluir un agente en el código y configurar el recurso correspondiente de Application Insights en Azure. El agente envía datos a ese recurso para su posterior análisis y exploración.

El agente de Node.js puede supervisar automáticamente solicitudes HTTP entrantes y salientes, varias métricas del sistema y excepciones. A partir de la versión v0.20, también puede supervisar algunos paquetes comunes de terceros como `mongodb`, `mysql` y `redis`. Todos los eventos relacionados con una solicitud HTTP entrante se correlacionan para una solución de problemas más rápida.

Puede supervisar más aspectos de la aplicación y el sistema mediante la instrumentación manual con el agente de API que se describe más adelante.

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Introducción

Vamos a configurar la supervisión para una aplicación o servicio.

### <a name="resource"></a> Configuración de un recurso de App Insights

**Antes de empezar**, asegúrese de que tiene una suscripción de Azure o bien [obtenga una nueva de forma gratuita][azure-free-offer]. Si su organización ya tiene una suscripción de Azure, un administrador puede seguir [estas instrucciones][add-aad-user] y agregarle a ella.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

Inicie sesión en [Azure Portal][portal] y cree un recurso de Application Insights como se muestra a continuación: haga clic en "Nuevo" > "Herramientas del desarrollador" > "Application Insights". El recurso incluye un punto de conexión para recibir los datos de telemetría, almacenamiento de estos datos, informes y paneles guardados, reglas y configuración de alertas y mucho más.

![Creación de recursos en Application Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

En la página de creación de recursos, elija "Aplicación Node.js" en la lista desplegable de tipo de aplicación. El tipo de aplicación determina el conjunto predeterminado de paneles e informes creados para usted. No se preocupe, cualquier recurso de Application Insights puede recopilar datos de cualquier lenguaje o plataforma.

![Formulario de nuevo recurso de Application Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> Configuración del agente de Node.js

Ahora es el momento de incluir el agente en la aplicación para que pueda recopilar datos.
En primer lugar, copie la clave de instrumentación del recurso (en adelante llamada `ikey`) desde el portal, tal y como se muestra a continuación. El sistema de App Insights usa esta clave para asignar datos a los recursos de Azure, por eso debe especificarla en una variable de entorno o en el código para que el agente la pueda utilizar.  

![Copia de la clave de instrumentación](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

A continuación, agregue la biblioteca de agentes de Node.js a las dependencias de la aplicación mediante package.json. En la carpeta raíz de la aplicación, ejecute:

```bash
npm install applicationinsights --save
```

Ahora debe cargar explícitamente la biblioteca en el código. Dado que el agente inserta la instrumentación en muchas otras bibliotecas, debe cargarlo tan pronto como sea posible, incluso antes que otras instrucciones `require`. Para comenzar, en la parte superior del primer archivo .js, agregue:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

El método `setup` configura la clave de instrumentación (y, por tanto, el recurso de Azure) que se usará de forma predeterminada para todos los elementos. Llame a `start` una vez finalizada la configuración para iniciar la recopilación y el envío de los datos de telemetría.

También puede proporcionar un valor de ikey mediante la variable de entorno APPINSIGHTS\_INSTRUMENTATIONKEY, en lugar de pasarla manualmente a `setup()` o `getClient()`. Esta práctica permite conservar los valores de ikey fuera del código fuente confirmado y también para especificar distintos valores de ikey para los diferentes entornos.

A continuación se documentan opciones de configuración adicionales.

Para probar el agente sin enviar datos de telemetría, establezca la clave de instrumentación en cualquier cadena no vacía.

### <a name="monitor"></a> Supervisión de la aplicación

El agente recopila automáticamente datos de telemetría sobre el tiempo de ejecución de Node.js y algunos módulos de terceros comunes. Utilice ahora la aplicación para generar algunos datos.

A continuación, en [Azure Portal][portal] busque el recurso de Application Insights que creó anteriormente y busque los primeros puntos de datos en la escala de tiempo de la vista de información general, como se muestra en la siguiente imagen. Haga clic en los gráficos para más información.

![Primeros puntos de datos](./media/app-insights-nodejs/12-first-perf.png)

Haga clic en el botón Mapa de aplicación para visualizar la topología detectada para la aplicación, como muestra la imagen siguiente. Haga clic en los componentes del mapa para más información.

![Mapa de aplicación simple](./media/app-insights-nodejs/06-appinsights_appmap.png)

Obtenga más información sobre la aplicación y solucione problemas con las vistas disponibles en la sección "Investigar".

![Sección Investigar](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>¿No hay datos?

Dado que el agente crea lotes de datos para enviar, puede haber un retraso hasta que se muestran los elementos en el portal. Si no ve los datos en el recurso, pruebe algunas de las soluciones siguientes:

* Utilice algo más la aplicación, realice más acciones para generar más datos de telemetría.
* Haga clic en **Actualizar** en la vista de recursos del portal. Los gráficos se actualizan periódicamente de un modo automático, pero actualizar obliga a que esto ocurra inmediatamente.
* Compruebe que los [puertos de salida necesarios](app-insights-ip-addresses.md) estén abiertos.
* Abra el icono [Buscar](app-insights-diagnostic-search.md) para ver los eventos individuales.
* Consulte las [preguntas más frecuentes][].


## <a name="agent-configuration"></a>Configuración de agente

A continuación se muestran los métodos de configuración del agente y sus valores predeterminados.

Para correlacionar totalmente los eventos en un servicio, asegúrese de establecer `.setAutoDependencyCorrelation(true)`. Esto permite al agente realizar el seguimiento de contexto a través de las devoluciones de llamada asincrónicas en Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>API del agente

<!-- TODO: Fully document agent API. -->

La API del agente de .NET se describe con detalle [aquí](app-insights-api-custom-events-metrics.md).

Puede realizar el seguimiento de cualquier solicitud, evento, métrica o excepción mediante el cliente de Node.js de Application Insights. El ejemplo siguiente muestra algunas de las API disponibles.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Seguimiento de las dependencias

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>Incorporación de una propiedad personalizada a todos los eventos

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Seguimiento de solicitudes HTTP GET

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>Seguimiento de la hora de inicio del servidor

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>Más recursos

* [Navegación y paneles en el portal de Application Insights](app-insights-dashboards.md)
* [Escritura de consultas de Analytics sobre los datos de telemetría](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[preguntas más frecuentes]: app-insights-troubleshoot-faq.md

