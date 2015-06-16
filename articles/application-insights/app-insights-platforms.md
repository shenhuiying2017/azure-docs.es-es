<properties 
	pageTitle="Application Insights: plataformas" 
	description="¿Se puede usar Application Insights con...?" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: plataformas

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

#### ¿Se puede usar Application Insights con...?


## Lenguajes

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [Páginas web de JavaScript](app-insights-web-track-usage.md)
+ [Aplicaciones Windows Store JavaScript](#cordova)
+ [Java](../app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Plataformas

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Aplicaciones y máquinas virtuales web de Azure](../insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Escritorio de Windows](app-insights-windows-desktop.md)


## Marcos de registro

+	[Log4Net, NLog o System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J o Logback](app-insights-java-trace-logs.md)


## Proyectos

Visite también el [proyecto SDK de Application Insights en GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Cordova y aplicaciones JavaScript de la Tienda Windows

En Visual Studio, haga clic con el botón secundario en el proyecto y elija **Administrar paquetes de NuGet**.

Seleccione **En línea** y busque en Application Insights.

Instale la **API de Application Insights para aplicaciones JavaScript**.

Utilice el [script de aplicación web](app-insights-web-track-usage.md) del cliente estándar, pero con un cambio.

Cuando se obtiene el script desde el portal de Application Insights, inserte una línea después de la clave de instrumentación:

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[Aplicaciones de la Tienda Windows con JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54--> 