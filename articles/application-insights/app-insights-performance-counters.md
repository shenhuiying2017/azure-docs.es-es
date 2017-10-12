---
title: Contadores de rendimiento en Application Insights | Microsoft Docs
description: Supervise los contadores de rendimiento de .NET, tanto del sistema como personalizados, en Application Insights.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: bwren
ms.openlocfilehash: 038d6e051be8112b9264e7efa6485965d11e32c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de rendimiento de sistema en Application Insights
Windows proporciona una amplia variedad de [contadores de rendimiento](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters), como la ocupación de la CPU, memoria, disco y uso de la red. También puede definir sus propios contadores. [Application Insights](app-insights-overview.md) puede mostrar estos contadores de rendimiento si la aplicación se ejecuta en IIS en un host local o en una máquina virtual para la que tenga acceso administrativo. Los gráficos indican los recursos disponibles para su aplicación activa y pueden ayudar a identificar la carga no equilibrada entre las instancias de servidor.

Los contadores de rendimiento aparecen en la hoja Servidores, que incluye una tabla que segmenta por instancia de servidor.

![Contadores de rendimiento notificados en Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Los contadores de rendimiento no están disponibles para Azure Web Apps. Pero puede [enviar Diagnósticos de Azure a Application Insights](app-insights-azure-diagnostics.md)).

## <a name="view-counters"></a>Visualización de contadores
La hoja Servidores muestra un conjunto predeterminado de contadores de rendimiento. 

Para ver otros contadores, edite los gráficos de la hoja Servidores o abra una nueva hoja [Explorador de métricas](app-insights-metrics-explorer.md) y agregue nuevos gráficos. 

Los contadores disponibles aparecen como las métricas cuando se edita un gráfico.

![Contadores de rendimiento notificados en Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Para ver todos los gráficos más útiles en un solo lugar, cree un [panel](app-insights-dashboards.md) y ánclelos a él.

## <a name="add-counters"></a>Adición de contadores
Si el contador de rendimiento que desea no aparece en la lista de métricas, eso es debido a que el SDK de Application Insights no lo está recopilando en el servidor web. Puede configurarlo para que lo haga.

1. Averigüe qué contadores están disponibles en el servidor mediante este comando de PowerShell en el servidor:
   
    `Get-Counter -ListSet *`
   
    (Consulte [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx)).
2. Abra ApplicationInsights.config.
   
   * Si agrega Application Insights a la aplicación durante el desarrollo, edite ApplicationInsights.config en el proyecto y vuelva a implementarlo en los servidores.
   * Si utiliza Monitor de estado para instrumentar una aplicación web en tiempo de ejecución, busque ApplicationInsights.config en el directorio raíz de la aplicación en IIS. Actualícelo allí en cada instancia del servidor.
3. Edite la directiva del recopilador de rendimiento:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Puede capturar tanto los contadores estándar como los que ha implementado usted mismo. `\Objects\Processes` es un ejemplo de contador estándar, disponible en todos los sistemas Windows. `\Sales(photo)\# Items Sold` es un ejemplo de contador personalizado que podría implementarse en un servicio web. 

El formato es `\Category(instance)\Counter"` o, para las categorías que no tienen instancias, simplemente `\Category\Counter`.

`ReportAs` es necesario para los nombres de contadores que no coinciden `[a-zA-Z()/-_ \.]+`: es decir, que contienen caracteres que no están en los siguientes conjuntos: letras, paréntesis, barra diagonal, guión, subrayado, espacio, punto.

Si especifica una instancia, se recopilará como una dimensión "CounterInstanceName" de la métrica notificada.

### <a name="collecting-performance-counters-in-code"></a>Recopilación de contadores de rendimiento en el código
Para recopilar contadores de rendimiento del sistema y enviarlos a Application Insights, puede adaptar el siguiente fragmento:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

También puede hacer lo mismo con las métricas personalizadas que haya creado:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contadores de rendimiento en Analytics
Puede buscar y mostrar informes de contador de rendimiento en [Analytics](app-insights-analytics.md).

El esquema **performanceCounters** expone `category`, el nombre de `counter` y el nombre de `instance` de cada contador de rendimiento.  En la telemetría de cada aplicación, solo se ven los contadores de dicha aplicación. Por ejemplo, para ver qué contadores están disponibles: 

![Contadores de rendimiento en Application Insights Analytics](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Aquí 'Instance' hace referencia a la instancia de contador de rendimiento, no al rol o a la instancia de máquina de servidor. El nombre de la instancia del contador de rendimiento normalmente segmenta contadores, como el tiempo de procesador por el nombre del proceso o la aplicación).

Para obtener un gráfico de la memoria disponible en un período reciente: 

![Gráfico de tiempo de la memoria in Application Insights Analytics](./media/app-insights-performance-counters/analytics-available-memory.png)

Al igual que otros datos de telemetría, **performanceCounters** también tiene una columna `cloud_RoleInstance` que indica la identidad de la instancia del servidor host en el que se ejecuta la aplicación. Por ejemplo, para comparar el rendimiento de una aplicación en distintas máquinas: 

![Rendimiento segmentado por instancia de rol en Application Insights Analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Recuentos de ASP.NET y Application Insights
*¿En qué se diferencian la tasa de excepciones y las métricas de excepciones?*

* *tasa de excepciones* es un contador de rendimiento del sistema. El CLR cuenta todas las excepciones controladas y no controladas que se producen, y divide el total de un intervalo de muestreo entre la duración del intervalo. El SDK de Application Insights recopila este resultado y lo envía al portal.
* *Excepciones* es un recuento de los informes de TrackException recibidos a través del portal en el intervalo de muestreo del gráfico. Solo incluye las excepciones controladas para las que ha escrito llamadas a TrackException en el código y no incluye todas las [excepciones no controladas](app-insights-asp-net-exceptions.md). 

## <a name="alerts"></a>Alertas
Al igual que otras métricas, puede [establecer una alerta](app-insights-alerts.md) para advertirle si un contador de rendimiento queda fuera de un límite especificado. Abra la hoja de alertas y haga clic en Agregar alerta.

## <a name="next"></a>Pasos siguientes
* [Seguimiento de dependencias](app-insights-asp-net-dependencies.md)
* [Seguimiento de excepciones](app-insights-asp-net-exceptions.md)

