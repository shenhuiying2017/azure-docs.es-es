<properties
	pageTitle="Análisis para aplicaciones de Windows Phone y de la Tienda Windows | Microsoft Azure"
	description="Analice el uso y los bloqueos de las aplicaciones de su dispositivo Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/26/2016"
	ms.author="awills"/>

# Análisis para aplicaciones de Windows Phone y de la Tienda Windows

Microsoft ofrece dos soluciones para DevOps de dispositivos: [HockeyApp](http://hockeyapp.net/) para análisis del lado cliente y [Application Insights](app-insights-overview.md) para el del lado servidor.

[HockeyApp](http://hockeyapp.net/) es nuestra solución de DevOps de móviles para aplicaciones de dispositivos iOS, OS X, Android o Windows, así como aplicaciones multiplataforma basadas en Xamarin, Cordova y Unity. Con ella, podrá distribuir compilaciones a evaluadores de la versión beta, recopilar datos de bloqueo y obtener métricas y comentarios de los usuarios. Está integrada en Visual Studio Team Services, lo que facilita las implementaciones de compilaciones y la integración de elementos de trabajo.

## Introducción a HockeyApp

Vaya a:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp para Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp Blog](http://hockeyapp.net/blog/)
* Únase a [Hockeyapp Preseason](http://hockeyapp.net/preseason/) para obtener versiones anteriores.

Si la aplicación tiene un lado servidor, utilice [Application Insights](app-insights-overview.md) para supervisar el lado servidor web de la aplicación en [ASP.NET](app-insights-asp-net.md) o [J2EE](app-insights-java-get-started.md).


También puede usar [Application Insights para aplicaciones de escritorio de Windows](app-insights-windows-desktop.md).

## Analytics, exportación y acceso de la API a los datos de HockeyApp 

[Configuración de un puente de HockeyApp](app-insights-hockeyapp-bridge-app.md) en Application Insights. Esto le permite:

* Usar el eficaz lenguaje de consulta [Analytics](app-insights-analytics.md) en los datos de telemetría
* [Exportar telemetría](app-insights-export-telemetry.md) a Almacenamiento de blobs de Azure

## Pasos siguientes

* [Get started with HockeyApp for Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) (Introducción con HockeyApp para Windows).

<!---HONumber=AcomDC_0831_2016-->