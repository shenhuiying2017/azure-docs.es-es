<properties
    pageTitle="Análisis para aplicaciones de iOS | Microsoft Azure"
    description="Analice el uso y el rendimiento de la aplicación de iOS."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Análisis para aplicaciones de iOS

Application Insights de Visual Studio le permite supervisar su aplicación móvil para obtener información relativa al uso, los eventos y los bloqueos.


> [AZURE.NOTE]Se recomienda usar [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) para obtener informes de bloqueo, análisis, distribución y administración de comentarios.

## Requisitos

Necesitará:

* Una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft.
* Xcode 6 o posterior.
* El SDK se ejecuta en dispositivos con iOS 6.0 o posterior.

## Creación de recursos en Application Insights

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Seleccione la opción iOS.

![Haga clic en Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-ios/11-new.png)

En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

## Configuración

Siga la [guía de configuración](https://github.com/Microsoft/ApplicationInsights-iOS#setup) para instalar el SDK en la aplicación.

## Visualización de los datos en Application Insights

Ejecute la aplicación para generar telemetría.

Vuelva a http://portal.azure.com y busque el recurso de Application Insights.

Haga clic en **Buscar** para abrir [Búsqueda de diagnóstico][diagnostic], que es donde aparecerán los primeros eventos. Si no ve nada, espere un minuto o dos y haga clic en **Actualizar**.

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
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->