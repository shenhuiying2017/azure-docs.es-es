---
title: "Supervisión del uso y el rendimiento en las aplicaciones de escritorio de Windows"
description: "Analice el uso y el rendimiento de la aplicación de escritorio de Windows con HockeyApp y Application Insights."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: mbullwin
ms.openlocfilehash: 3b91996c12a27847befcacf6daf14457a0e066aa
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Supervisión del uso y el rendimiento en las aplicaciones de escritorio de Windows


[Azure Application Insights](app-insights-overview.md) y [HockeyApp](https://hockeyapp.net) permiten supervisar el uso y rendimiento de la aplicación implementada.

> [!IMPORTANT]
> Se recomienda [HockeyApp](https://hockeyapp.net) para distribuir y supervisar las aplicaciones de escritorio y para dispositivos. Con HockeyApp, se puede administrar la distribución, las pruebas en directo y los comentarios de los usuarios, así como supervisar los informes de uso y bloqueo. También puede [exportar y consultar la telemetría con Analytics](app-insights-hockeyapp-bridge-app.md).
> 
> Aunque puede enviarse telemetría a Application Insights desde una aplicación de escritorio, esto es principalmente útil para fines experimentales y de depuración.
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar telemetría a Application Insights desde una aplicación Windows
1. En [Azure Portal](https://portal.azure.com), [cree un recurso de Application Insights](app-insights-create-new-resource.md). Para el tipo de aplicación, elija la aplicación ASP.NET.
2. Realice una copia de la clave de instrumentación. Busque la clave en la lista desplegable Essentials del recurso que acaba de crear. 
3. En Visual Studio, edite los paquetes NuGet de su proyecto de aplicación y agregue Microsoft.ApplicationInsights.WindowsServer. (O elija Microsoft.ApplicationInsights si únicamente le interesa la API sola, sin los módulos de recopilación de telemetría estándar).
4. Establezca la clave de instrumentación en el código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*su clave*`";` 
   
    o en ApplicationInsights.config (si tiene instalado uno de los paquetes de telemetría estándar):
   
    `<InstrumentationKey>`*su clave*`</InstrumentationKey>` 
   
    Si usa ApplicationInsights.config, asegúrese de que sus propiedades en el Explorador de soluciones se establecen en **Acción de compilación = Contenido, Copiar en el directorio de salida = Copiar**.
5. [Use la API](app-insights-api-custom-events-metrics.md) para enviar telemetría.
6. Ejecute la aplicación y vea la telemetría en el recurso que creó en Azure Portal.

## <a name="telemetry"></a>Ejemplo de código
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>pasos siguientes
* [Creación de un panel](app-insights-dashboards.md)
* [Búsqueda de diagnóstico](app-insights-diagnostic-search.md)
* [Exploración de métricas](app-insights-metrics-explorer.md)
* [Escribir consultas de Analytics](app-insights-analytics.md)

