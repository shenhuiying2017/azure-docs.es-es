<properties
	pageTitle="Application Insights: lenguajes y plataformas e integraciones| Microsoft Azure"
	description="Lenguajes y plataformas e integraciones disponibles para Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="OlegAnaniev-MSFT"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/01/2016"
	ms.author="awills"/>

# Análisis del desarrollador: lenguajes y plataformas e integraciones

Estos elementos son implementaciones de [Application Insights](app-insights-overview.md) sobre las que tenemos noticias, que incluyen también algunas de terceros.

## Idiomas

+ [C#|VB (.NET)](app-insights-asp-net.md)
+ [Java](app-insights-java-get-started.md)
+ [Páginas web de JavaScript](app-insights-web-track-usage.md)
+ [Objective-C](https://github.com/Microsoft/ApplicationInsights-iOS)
+ [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
+ [Ruby](https://rubygems.org/gems/application_insights)
+ [Nada más](#projects)

## Plataformas y marcos

+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [ASP.NET](app-insights-asp-net.md)
+ [ASP.NET: para aplicaciones que ya están activas](app-insights-monitor-performance-live-website-now.md)
+ [ASP.NET 5](app-insights-asp-net-core.md)
+ [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
+ [Aplicaciones y máquinas virtuales web de Azure](../azure-portal/insights-perf-analytics.md)
+ [Servicios en la nube de Azure](app-insights-cloudservices.md)&#151;incluidos los roles web y de trabajo
+ [CRM Online desde Microsoft Dynamics](app-insights-sample-mscrm.md)
+ [Docker](app-insights-docker.md)
+ [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
+ [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
+ [J2EE](app-insights-java-get-started.md)
+ [J2EE: para aplicaciones que ya están activas](app-insights-java-live.md)
* [Aplicación Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
+ [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Aplicación universal de Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
+ [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplicación para Windows Phone 8 y 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Aplicación para Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
+ [Aplicaciones, servicios y roles de trabajo del escritorio de Windows](app-insights-windows-desktop.md)
+ [Nada más](#projects)


## Marcos de registro

+	[Log4Net, NLog o System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J o Logback](app-insights-java-trace-logs.md)
+   El [Registro semántico](https://github.com/fidmor89/SLAB_AppInsights)&#151; se integra con el [Bloque de aplicación de registro semántico](https://msdn.microsoft.com/library/dn440729.aspx)
+   [Pruebas de carga en la nube](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)


## Sistemas de administración de contenido

+ [Concrete](https://github.com/fidmor89/appInsights-Concrete)
+ [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [Orchard](https://orchardazureappinsights.codeplex.com)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)

## Exportación y análisis de datos

+ [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
+ [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
+ [Análisis de transmisiones](app-insights-export-power-bi.md)

## <a name="projects"></a> Creación de su propio SDK

Si no hay todavía un SDK para el lenguaje o plataforma, quizás desee crear uno. Examine el código de los SDK existentes que aparecen en el [proyecto de SDK de Application Insights en GitHub](https://github.com/Microsoft/AppInsights-Home).

<!---HONumber=AcomDC_0907_2016-->