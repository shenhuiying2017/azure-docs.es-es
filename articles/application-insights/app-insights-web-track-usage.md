---
title: "Análisis de uso de aplicaciones web con Azure Application Insights | Microsoft Docs"
description: "Información general del análisis de uso con Application Insights"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Análisis de uso de aplicaciones web con Application Insights
Saber cómo la gente usa su aplicación le permite centrar el trabajo de desarrollo en los escenarios que son más importantes, así como obtener información sobre los objetivos que les resultan más fáciles o más difíciles de lograr.

[Azure Application Insights](app-insights-overview.md) ofrece dos niveles de seguimiento de uso:

* **Datos de usuarios, sesiones y vistas de página** : listos para usar.  
* **Telemetría personalizada**: se [escribe código](app-insights-api-custom-events-metrics.md) para hacer un seguimiento de los usuarios a través de la experiencia del usuario con la aplicación. 


## <a name="get-started"></a>Introducción

La mejor experiencia se obtiene mediante la instalación de Application Insights en el código de servidor de aplicaciones y en las páginas web. Los componentes de cliente y servidor de la aplicación devuelven telemetría al portal de Azure para su análisis.

1. **Código de servidor:** instale el módulo adecuado para [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), u [otra aplicación ](app-insights-platforms.md).

    * *¿No desea instalar código del servidor? Simplemente [cree un recurso de Azure Application Insights](app-insights-create-new-resource.md).*

2. **Código de página web:** abra el [portal de Azure](https://portal.azure.com), abra el recurso de Application Insights para su aplicación y luego abra **Introducción > Supervisar y diagnosticar la aplicación del lado cliente**. 

    ![Copie el script en el encabezado de la página web maestra.](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **Obtener telemetría:** ejecute su proyecto en modo de depuración durante unos minutos y luego busque resultados en la hoja de información general en Application Insights.

    Publique su aplicación para supervisar el rendimiento de su aplicación y descubra lo que hacen sus usuarios con ella.

## <a name="usage-analysis-out-of-the-box"></a>Análisis de uso predefinido
Abra la hoja Uso.

![Gráficos de usuarios, sesiones y vistas de página](./media/app-insights-web-track-usage/14-usage.png)

Pase el puntero por la parte en blanco sobre un gráfico para ver los recuentos en un momento determinado. Si no, los números muestran el valor agregado a lo largo del período, como un promedio, un total o un recuento de usuarios diferentes durante el período.

¿Qué muestran estos gráficos?

* **Usuarios:** : recuento de los distintos usuarios activos durante el intervalo de tiempo del gráfico. En aplicaciones web, se cuentan los usuarios mediante el uso de cookies. Una persona que utiliza varios exploradores, que borra las cookies o que utiliza la característica de privacidad se contará varias veces.
* Los **usuarios autenticados** se contabilizan si se ha insertado una llamada a [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) en su código.
* **Sesiones:** el número de sesiones activas. Se considera una sesión web después de 30 minutos de inactividad. 
* **Vistas de página** : cuenta el número de llamadas a trackPageView(), normalmente se llama una vez en cada página web.

Haga clic en cualquiera de los gráficos para ver su contenido con mayor detalle. Observe que puede cambiar el intervalo de tiempo de los gráficos.

### <a name="where-do-my-users-live"></a>¿Dónde viven mis usuarios?
Haga clic en una parte en blanco del gráfico de usuarios para abrir otra hoja que muestra más detalles:

![En la hoja Uso, haga clic en el gráfico Usuarios.](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>¿Qué sistemas operativos o exploradores utilizan?

Haga clic en **Editar** en el gráfico de usuarios y agrupe (segmente) los datos por una propiedad, como explorador, sistema operativo o ciudad: 

![Seleccione un gráfico que muestre una sola métrica, cambie a Agrupación y elija una propiedad.](./media/app-insights-web-track-usage/03-browsers.png)

Para ver el conjunto completo de recuentos, cambie el tipo de gráfico a **Cuadrícula**. También puede elegir mostrar métricas adicionales:

![Gráfico de cuadrícula de varias columnas](./media/app-insights-web-track-usage/multi-column-grid.png)

En el caso de diagramas gráficos, puede agrupar por una propiedad o seleccionar varias métricas, pero no ambas cosas. En este gráfico se comparan dos métricas, usuarios y [usuarios autenticados](app-insights-api-custom-events-metrics.md#authenticated-users). 

![Seleccione un gráfico, busque y active o desactive las métricas.](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>Vistas de página
Haga clic en la hoja Usuarios y haga clic en el gráfico de vistas de página para obtener una versión más detallada, junto con un desglose de sus páginas más populares:

![En la hoja de información general, haga clic en el gráfico de vistas de páginas.](./media/app-insights-web-track-usage/05-games.png)

El ejemplo anterior es de un sitio web de juegos. A partir de los gráficos, podemos ver al instante:

* El uso no ha mejorado durante la semana anterior. ¿Quizás debemos pensar en la optimización de motor de búsqueda?
* Tennis es la página de juegos más popular. Vamos a centrarnos en mejorar aún más esta página.
* Por término medio, los usuarios visitan la página Tennis una tres veces por semana. (Hay unas tres veces más sesiones que usuarios.)
* La mayoría de usuarios visitan el sitio durante la semana laboral en EE. UU. y en el horario laborable. Quizás deberíamos proporcionar un botón "ocultar rápidamente" en la página web.
* Las [anotaciones](app-insights-annotations.md) en el gráfico muestran cuándo se implementaron nuevas versiones del sitio web. Ninguna de las implementaciones ha tenido un efecto notable sobre el uso.

## <a name="custom-tracking"></a>Seguimiento personalizado
Supongamos en lugar de implementar cada juego en una página web independiente se decide rediseñar todos para que estén en la misma aplicación de una página, con la mayoría de la funcionalidad codificada como Javascript en la página web. De esta forma el usuario puede cambiar rápidamente de un juego a otro, o incluso tener varios juegos en una sola página.

Pero todavía desea que Application Insights registre el número de veces que se abre cada juego, exactamente igual que cuando estaban en páginas web independientes. Es muy sencillo: inserte una llamada al módulo de telemetría en el JavaScript donde desea registrar que se ha abierto una nueva «página»:

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
Esta llamada simula la telemetría que registra una vista de página.  Sin embargo, no siempre deseará mezclar los mensajes con vistas de página. En este caso, use eventos personalizados. Puede enviarlos desde páginas web o un servidor web:


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

La [telemetría personalizada insertada en su código web o de servidor](app-insights-api-custom-events-metrics.md#trackevent) se puede usar de muchas maneras para comprender cómo se usan sus aplicaciones.

Para ver los eventos enviados por TrackEvent(): abra el Explorador de métricas, agregue un nuevo gráfico y luego edítelo. Las métricas aparecen debajo de las métricas personalizadas. 

![Un gráfico que muestra eventos personalizados.](./media/app-insights-web-track-usage/06-eventsSegment.png)

Si establece [valores de propiedad](app-insights-api-custom-events-metrics.md#properties) (también llamados dimensiones) en los eventos, puede agruparlos y filtrarlos.

Cree varios gráficos para correlacionar los cambios en otras métricas y eventos. Por ejemplo, a veces cuando se reproducen más juegos, lo habitual es que espere ver también un aumento de juegos abandonados. Pero el aumento de juegos abandonados es desproporcionado, desea averiguar si la carga alta está causando problemas que los usuarios encuentran inaceptables.

## <a name="drill-into-specific-events"></a>Profundización en eventos específicos
Para comprender mejor cómo se desarrolla una sesión típica, debe centrarse en una sesión de usuario específica que contenga un tipo determinado de evento.

En una cuadrícula de eventos, haga clic en el evento de interés y seleccione una repetición específica reciente:

![En la lista bajo el gráfico de resumen haga clic en un evento.](./media/app-insights-web-track-usage/08-searchEvents.png)

Echemos un vistazo a toda la telemetría de la sesión en la que se produjo un evento NoGame determinado.

![Haga clic en 'Toda la telemetría de la sesión'.](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

No había ninguna excepción, por lo que no se le impidió al usuario jugar debido a un error.

Podemos filtrar todos los tipos de telemetría excepto las vistas de páginas para esta sesión:

![](./media/app-insights-web-track-usage/10-filter.png)

Y ahora podemos comprobar que este usuario se ha conectado simplemente para comprobar los últimos resultados. Tal vez haya que desarrollar un caso de usuario que facilite esta comprobación. (Y debemos implementar un evento personalizado para informar cuando se produzca este caso específico).

## <a name="filter-search-and-segment-your-data-with-properties"></a>Filtrar, buscar y segmentar los datos con propiedades
Puede adjuntar etiquetas arbitrarias y valores numéricos a los eventos.

*JavaScript en páginas web*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# en el servidor*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB en el servidor*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Asocie propiedades a vistas de página de la misma manera:

*JavaScript en páginas web*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

En Búsqueda de diagnóstico, vea las propiedades haciendo clic a través de una única repetición de un evento.

![En la lista de eventos, abra un evento y luego haga clic en '...' para ver más propiedades.](./media/app-insights-web-track-usage/11-details.png)

Utilice el campo de búsqueda para ver las apariciones del evento con un valor de propiedad concreto.

![Escriba un valor en el campo de búsqueda.](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>Edición y creación de consultas sobre la telemetría

Haga clic en el icono de Azure Analytics en cualquier gráfico para abrir una consulta equivalente que pueda editar.
Desplácese hacia abajo para ver si se ha generado más de una consulta. Coloque el cursor en cualquier consulta y haga clic en **Ir**. 

Como alternativa, abra Analytics desde el icono de la hoja Información general y escriba sus propias consultas; o bien pruebe algunas de las consultas de ejemplo de la pestaña de inicio de Analytics.


![Ventana de análisis con una consulta generada](./media/app-insights-web-track-usage/open-analytics.png)

[Más información sobre el lenguaje de consulta de Azure Analytics](app-insights-analytics.md).

Para el análisis del uso, puede estar especialmente interesado en estas tablas:

* `customEvents`: resultados de las llamadas a [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).
* `pageViews`: recuentos de páginas abiertas en exploradores de cliente o llamadas a [trackPageView()](app-insights-api-custom-events-metrics.md#page-views).


## <a name="a--b-testing"></a>Prueba A | B
Si no conoce qué variante de una característica tendrá más éxito, publique ambas para que estén accesibles a los diferentes usuarios. Mida el éxito de cada una y, a continuación, cambie a una versión unificada.

Para realizar esta técnica, adjunte etiquetas distintas a toda la telemetría que se envía con cada versión de la aplicación. Puede hacerlo al definir las propiedades en el TelemetryContext activo. Estas propiedades predeterminadas se agregan a cada mensaje de telemetría que envía la aplicación: no solo los mensajes personalizados, sino también la telemetría estándar.

En el portal de Application Insights, podrá filtrar y agrupar (segmentar) los datos en las etiquetas, con el fin de comparar las distintas versiones.


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

La telemetría individual puede invalidar los valores predeterminados.

Puede configurar un inicializador universal para que todos los TelemetryClients nuevos usen automáticamente el contexto.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

En el inicializador de la aplicación como Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>Compilación - Métrica - Aprendizaje
Cuando se utiliza el análisis, se convierte en una parte integrada de su ciclo de desarrollo, no solo en algo que se cree que puede ayudar a solucionar problemas. A continuación se incluyen algunas sugerencias:

* Determine la métrica clave de la aplicación. ¿Desea tantos usuarios como sea posible o preferiría un pequeño conjunto de usuarios satisfechos? ¿Desea potenciar al máximo las visitas o las ventas?
* Planee la medición de cada caso. Cuando esboza un nuevo caso de usuario o característica o planea actualizar uno existente, siempre piensa cómo medirá el éxito del cambio. Antes de que se inicie la codificación, pregunte "¿Qué efecto tendrá en nuestras estadísticas, si funciona? ¿Debemos realizar el seguimiento cualquier evento nuevo?"
  Y, por supuesto, cuando se activa la característica, asegúrese de mirar el análisis y actuar en los resultados.
* Relacione otras métricas con la métrica clave. Por ejemplo, si agrega una característica "Favoritos", le gustaría saber con qué frecuencia los usuarios agregan favoritos. Pero quizás sea más interesante saber con qué frecuencia vuelven a sus favoritos. Y lo más importante, los clientes que usan favoritos ¿compran al final más de su producto?
* Pruebas de valor controlado. Configure un modificador de característica que permite que una nueva característica sea visible solo para algunos usuarios. Use Application Insights para ver si se utiliza la nueva característica de la manera que había previsto. Realice ajustes y publíquelos para una audiencia más amplia.
* Hable con los usuarios. El análisis no es suficiente por sí solo, pero es una herramienta complementaria para mantener una excelente relación con el cliente.

## <a name="learn-more"></a>Más información
* [Detección, clasificación y diagnóstico de errores y problemas de rendimiento en su aplicación](app-insights-detect-triage-diagnose.md)
* [Introducción a Application Insights en muchas plataformas.](app-insights-detect-triage-diagnose.md)
* [Uso de la API (información general)](app-insights-api-custom-events-metrics.md)
* [Referencia de API de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



