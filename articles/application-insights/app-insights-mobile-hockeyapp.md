---
title: "Supervisión de rendimiento de aplicaciones móviles: Azure Application Insights | Microsoft Docs"
description: "Supervisión del uso y el rendimiento de aplicaciones para desarrolladores de aplicaciones móviles. DevOps con HockeyApp y Application Insights."
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Análisis móvil con HockeyApp y Application Insights
Supervise el rendimiento y el uso de su prueba beta, y de las aplicaciones móviles y de escritorio que se han implementado con [HockeyApp](https://hockeyapp.net/). Supervise las aplicaciones back-end y de servicio web compatibles con [Azure Application Insights](app-insights-overview.md). Analice datos de aplicaciones de servidor y cliente en Analytics, y muestre los gráficos en paralelo en un Panel de Azure.

Análisis del desarrollador de Microsoft ofrece dos soluciones para la supervisión del rendimiento de aplicaciones:

* **HockeyApp** para aplicaciones móviles y de escritorio.
  * Distribución de versiones de prueba en usuarios seleccionados.
  * Análisis de bloqueos.
  * Telemetría personalizada para el análisis de uso.
* **Application Insights** para servicios y sitios web, y aplicaciones back-end.
  * Alertas y métricas de uso y rendimiento.
  * Generación de informes de excepciones y seguimiento de diagnósticos.
  * Búsqueda de diagnósticos con filtrado y vínculos de telemetría relacionados.

Ambas soluciones ofrecen:

* **[Lenguaje de consulta analítico](app-insights-analytics.md)** eficaz para los diagnósticos y el análisis.
* **[Exportación de datos](app-insights-export-telemetry.md)** a su propio almacenamiento.
* **Panel integrado** para la visualización de tablas y gráficos analíticos.

## <a name="monitor-your-app-components"></a>Supervisar los componentes de las aplicaciones
Siga las instrucciones de estas páginas para instalar el SDK en el código y empezar a realizar un seguimiento de la aplicación.

### <a name="web-apps---application-insights"></a>Web Apps: Application Insights
* [Aplicación web para ASP.NET](app-insights-asp-net.md) 
* [Aplicaciones web de Java](app-insights-java-get-started.md)
* [Aplicación web de Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure Cloud Services](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Mobile Apps: HockeyApp
* [Aplicación para iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicación para Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicación para Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicación para Windows universal](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicación para Windows Phone 8 y 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Aplicación para Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Para las aplicaciones de escritorio de Windows se recomienda usar HockeyApp. También puede [enviar telemetría a Application Insights desde una aplicación de Windows](app-insights-windows-desktop.md). Puede que quiera hacer eso para probar Application Insights.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Análisis y exportación de telemetría de HockeyApp
Puede investigar la telemetría de registros y personalizada de HockeyApp mediante las características de análisis y exportación continua de Application Insights [configurando un puente](app-insights-hockeyapp-bridge-app.md).




<!--HONumber=Jan17_HO4-->


