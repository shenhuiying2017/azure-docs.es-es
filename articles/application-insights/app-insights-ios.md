<properties 
    pageTitle="Application Insights para aplicaciones iOS" 
    description="Analice el uso y el rendimiento de la aplicación de iOS con Application Insights." 
    services="application-insights" 
    documentationCenter="ios"
    authors="alancameronwills" 
    manager="ronmart"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="04/27/2015" 
    ms.author="awills"/>

# Application Insights para aplicaciones iOS

Visual Studio Application Insights le permite supervisar su aplicación móvil para obtener información relativa al uso, los eventos y los bloqueos.

## Requisitos

Necesitará:

* Una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft.
* Xcode 6 o posterior.
* El SDK se ejecuta en dispositivos con iOS 6.0 o posterior.

## Creación de recursos en Application Insights

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Seleccione la opción iOS.

![Haga clic en Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-ios/11-new.png)

En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

## Descargue Application Insights para Mac,

si aún no lo ha hecho.

1. Descarga de [Application Insights para Mac](http://go.microsoft.com/fwlink/?LinkID=533209)

2. Extraiga el archivo ZIP.

3. Haga clic en el icono de la aplicación para iniciar Application Insights para Mac.

## <a name="signin"></a>Inicio de sesión en Azure

1. Haga clic en Iniciar sesión.

2. Inicie sesión con su cuenta de Azure.

## Instale el SDK en su aplicación.

1. Haga clic en Integrar para iniciar la integración con el SDK.

2. Seleccione el proyecto de Xcode de la lista o haga clic en Abrir para buscar el proyecto. A continuación, haga clic en Integrar.

3. Elija la carpeta del SDK de Application Insights y luego haga clic en Instalar.

4. Agregue el script de ejecución que se muestra a las fases de compilación.

    [Agregar fase de ejecución de script](http://hockeyapp.net/help/runscriptbuildphase/)

5. Agregue los marcos que faltan al proyecto de Xcode.

6. Arrastre el marco de Application Insights a su proyecto de Xcode y haga clic en Siguiente.

7. Seleccione Integrar SDK en destino para su destino.

8. Haga clic en Crear nuevo componente para crear la aplicación en el portal de Application Insights.

9. Seleccione su suscripción, el grupo de recursos y escriba un nombre de componente. En la mayoría de los casos, este nombre debe coincidir con el nombre de la aplicación. Confirme con el botón Crear recurso.

10. Asegúrese de que se haya seleccionado el componente adecuado y haga clic en Siguiente.

11. Modifique el código fuente, como se muestra en el asistente, y haga clic en Finalizar.

12. Inicie la aplicación en el simulador de iOS con Compilar y ejecutar.

## Inserción de llamadas de telemetría

Una vez que se llama a `[MSAIApplicationInsights start]`, el SDK comenzará a realizar el seguimiento de las sesiones y las vistas de página, así como de las excepciones no controladas o los bloqueos.

Puede agregar eventos adicionales de la manera siguiente:

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric" 
                                        value:42.2];

## Visualización de los datos en Application Insights

Vuelva a http://portal.azure.com y busque el recurso de Application Insights.

Haga clic en Buscar para abrir [Búsqueda de diagnóstico][diagnostic], que es donde aparecerán los primeros eventos. Si no ve nada, espere un minuto o dos y haga clic en Actualizar.

![Haga clic en Búsqueda de diagnóstico.](./media/app-insights-ios/21-search.png)

A medida que use la aplicación, los datos aparecerán en la hoja de información general.

![Hoja de información general](./media/app-insights-ios/22-oview.png)

Haga clic en cualquier gráfico para obtener más detalles. Por ejemplo, bloqueos:

![Haga clic en el gráfico de bloqueos](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Pasos siguientes

[Seguimiento del uso de la aplicación][track]

[Búsqueda de diagnóstico][diagnostic]

[Explorador de métricas][metrics]

[Solución de problemas][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=August15_HO6-->