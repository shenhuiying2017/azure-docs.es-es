---
title: 'Application Insights: lenguajes, plataformas e integraciones| Microsoft Docs'
description: Lenguajes y plataformas e integraciones disponibles para Application Insights
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin
ms.openlocfilehash: fe8f826ca3e0f44cba3d0b246afb01b262dc330b
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Análisis del desarrollador: lenguajes y plataformas e integraciones
Estos elementos son implementaciones de [Application Insights](app-insights-overview.md) sobre las que tenemos noticias, que incluyen también algunas de terceros.

## <a name="languages---officially-supported-by-application-insights-team"></a>Lenguajes: oficialmente admitidos por el equipo de Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Páginas web de JavaScript](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Lenguajes: admitidos por la comunidad
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Nada más](#projects)

## <a name="platforms-and-frameworks"></a>Plataformas y marcos
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET: para aplicaciones que ya están activas](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center, HockeyApp)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md)&#151;incluidos los roles web y de trabajo
* 
            [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center, HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE: para aplicaciones que ya están activas](app-insights-java-live.md)
* [Aplicación de macOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Aplicación Windows universal](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (App Center, HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplicación para Windows Phone 8 y 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Aplicación para Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Aplicaciones, servicios y roles de trabajo del escritorio de Windows](app-insights-windows-desktop.md)
* [Nada más](#projects)

## <a name="logging-frameworks"></a>Marcos de registro
* [Log4Net, NLog o System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J o Logback](app-insights-java-trace-logs.md)
* El [Registro semántico (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) se integra con el [Bloque de aplicación de registro semántico](https://msdn.microsoft.com/library/dn440729.aspx)
* [Pruebas de carga en la nube](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Complemento LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics de OMS](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)

## <a name="content-management-systems"></a>Sistemas de administración de contenido
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Exportación y análisis de datos
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Creación de su propio SDK
Si no hay todavía un SDK para el lenguaje o plataforma, quizás desee crear uno. Examine el código de los SDK existentes que aparecen en el [proyecto de SDK de Application Insights en GitHub](https://github.com/Microsoft/AppInsights-Home).
