---
title: "Exploración de registros de seguimiento de .NET en Application Insights"
description: Busque registros generados con Seguimiento, NLog o Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 6da0bf009fa71885d7d8e3bd5376c5a7c9d4a344
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Exploración de registros de seguimiento de .NET en Application Insights
Si usa NLog, log4Net o System.Diagnostics.Trace para realizar el seguimiento de diagnósticos en la aplicación ASP.NET, sus registros se pueden enviar a [Azure Application Insights][start], donde puede explorarlos y buscarlos. Los registros se combinarán con los otros informes de telemetría procedente de su aplicación, y así podrá identificar los seguimientos asociados con el mantenimiento de cada solicitud de usuario y correlacionarlos con otros eventos e informes de excepciones.

> [!NOTE]
> ¿Necesita el módulo de captura de registros? Es un adaptador útil para registradores de terceros, pero si aún no está usando NLog, log4Net o System.Diagnostics.Trace, considere la posibilidad de llamar directamente a [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) .
>
>

## <a name="install-logging-on-your-app"></a>Instalación del registro en la aplicación
Instale el marco de registro elegido en su proyecto. Esto debería producir una entrada en app.config o web.config.

Si usa System.Diagnostics.Trace, debe agregar una entrada a web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Configuración de Application Insights para recopilar registros
Si todavía no lo ha hecho, **[agregue Application Insights a su proyecto](app-insights-asp-net.md)**. Verá una opción para incluir el compilador de registros.

O **configure Application Insights** haciendo clic con el botón derecho en el proyecto en el Explorador de soluciones. Seleccione la opción de **Configurar recolección de seguimientos**.

*¿No aparece ningún menú de Application Insights ni una opción de compilador de registros?* Pruebe la [solución de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalación manual
Utilice este método si el tipo de proyecto no es compatible con el programa de instalación de Application Insights (por ejemplo, un proyecto de escritorio de Windows).

1. Si planea usar log4Net o NLog, instálelo en su proyecto.
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Busque "Application Insights"
4. Seleccione el paquete adecuado entre los siguientes:

   * Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.EventSourceListener (para capturar eventos EventSource)
   * Microsoft.ApplicationInsights.EtwListener (para capturar eventos ETW)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

## <a name="insert-diagnostic-log-calls"></a>Insertar llamadas de registro de diagnóstico
Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Uso de eventos EventSource
Puede configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para enviarse a Application Insights como seguimientos. Primero, instale el paquete de NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Luego, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

En cada origen, puede establecer los siguientes parámetros:
 * `Name` especifica el nombre del evento EventSource que se recopilará.
 * `Level` especifica el nivel de registro que se recopilará. Puede ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` o `Warning`.
 * `Keywords` especifica el valor del entero de combinaciones de palabras clave que se usará (opcional).

## <a name="using-diagnosticsource-events"></a>Uso de eventos de DiagnosticSource
Puede configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para enviarlos a Application Insights como seguimientos. Primero, instale el paquete de NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Seguidamente, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnsoticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada instancia de DiagnosticSource que quiera seguir, agregue una entrada con el atributo `Name` establecido con el nombre de la instancia DiagnosticSource.

## <a name="using-etw-events"></a>Uso de eventos ETW
Puede configurar eventos ETW para enviarse a Application Insights como seguimientos. Primero, instale el paquete de NuGet `Microsoft.ApplicationInsights.EtwCollector`. Luego, edite la sección `TelemetryModules` del archivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Solo se pueden recopilar eventos ETW si el proceso que hospeda el SDK se ejecuta bajo una identidad que sea miembro de "Usuarios del registro de rendimiento" o los administradores.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

En cada origen, puede establecer los siguientes parámetros:
 * `ProviderName` es el nombre del proveedor ETW que se recopilará.
 * `ProviderGuid` especifica el GUID del proveedor ETW que se recopilará, que puede usarse en lugar de `ProviderName`.
 * `Level` establece el nivel de registro que se recopilará. Puede ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` o `Warning`.
 * `Keywords` establece el valor del entero de combinaciones de palabras clave que se usará (opcional).

## <a name="using-the-trace-api-directly"></a>Uso de la API de seguimiento directamente
Puede llamar directamente a la API de seguimiento de Application Insights. Los adaptadores de registro usan esta API.

Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, aquí podría codificar datos POST.

Además, puede agregar un nivel de gravedad al mensaje. Y, al igual que con otra telemetría, puede agregar valores de propiedad que puede usar para ayudar a filtrar o buscar distintos conjuntos de seguimientos. Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Esto permitiría filtrar fácilmente en [Búsqueda][diagnostic] todos los mensajes de un determinado nivel de gravedad relativos a una determinada base de datos.

## <a name="explore-your-logs"></a>Explorar los registros
Ejecute la aplicación, ya sea en modo de depuración o mediante su implementación para que esté activa.

En la hoja de información general de su aplicación del [portal de Application Insights][portal], elija [Buscar][diagnostic].

![En Application Insights, elija Buscar.](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Búsqueda](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Por ejemplo, puede:

* Filtrar por seguimientos de registro, o por elementos con determinadas propiedades
* Inspeccionar un elemento específico en detalle
* Encontrar otros informes de telemetría relacionados con la misma solicitud de usuario (es decir, con el mismo OperationId)
* Guardar la configuración de esta página como favorita

> [!NOTE]
> **Muestreo.** Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Pasos siguientes
[Diagnóstico de errores y excepciones en ASP.NET][exceptions]

[Aprenda más sobre la búsqueda][diagnostic].

## <a name="troubleshooting"></a>Solución de problemas
### <a name="how-do-i-do-this-for-java"></a>¿Cómo se puede hacer para Java?
Utilice los [adaptadores de registro de Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>No aparece la opción de Application Insights en el menú contextual del proyecto
* Compruebe si las herramientas de Application Insights están instaladas en este equipo de desarrollo. En el menú Herramientas de Visual Studio, en Extensiones y actualizaciones, busque Herramientas de Application Insights. Si no se encuentran en la pestaña Instalado, abra la pestaña En línea e instálelas.
* Podría tratarse de un tipo de proyecto no admitido por las herramientas de Application Insights. Use la [instalación manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>No aparece la opción de adaptador en la herramienta de configuración
* Debe instalar primero el marco de registro.
* Si usa System.Diagnostics.Trace, asegúrese de que lo ha [configurado en `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* ¿Tiene la versión más reciente de Application Insights? En el menú **Herramientas** de Visual Studio, elija **Extensiones y actualizaciones** y abra la pestaña **Actualizaciones**. Si Developer Analytics Tools está instalado, haga clic para actualizarlo.

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".
Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Puedo ver seguimientos en Búsqueda de diagnóstico, pero no los otros eventos.
A veces, el paso de todos los eventos y solicitudes por la canalización puede llevar un rato.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?
Hay varios factores que afectan a la cantidad de datos que se conservan. Consulte la sección [Límites](app-insights-api-custom-events-metrics.md#limits) de la página de métricas de eventos de cliente para más información. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>No veo algunas de las entradas del registro que esperaba
Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)

## <a name="add"></a>Pasos siguientes
* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
