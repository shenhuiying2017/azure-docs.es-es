---
title: "Exploración de registros de seguimiento de .NET en Application Insights"
description: Busque registros generados con Seguimiento, NLog o Log4Net.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: d46407da69184da6b1dba72aeb86e97cf1cae725


---
# <a name="explore-net-trace-logs-in-application-insights"></a>Exploración de registros de seguimiento de .NET en Application Insights
Si usa NLog, log4Net o System.Diagnostics.Trace para realizar el seguimiento de diagnósticos en la aplicación ASP.NET, sus registros se pueden enviar a [Azure Application Insights][start], donde puede explorarlos y buscarlos. Los registros se combinarán con los otros informes de telemetría procedente de su aplicación, y así podrá identificar los seguimientos asociados con el mantenimiento de cada solicitud de usuario y correlacionarlos con otros eventos e informes de excepciones.

> [!NOTE]
> ¿Necesita el módulo de captura de registros? Es un adaptador útil para registradores de terceros, pero si aún no está usando NLog, log4Net o System.Diagnostics.Trace, considere la posibilidad de llamar directamente a [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) .
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
   
    ![Get the prerelease version of the appropriate adapter](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)
4. Seleccione el paquete adecuado entre los siguientes:
   
   * Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

## <a name="insert-diagnostic-log-calls"></a>Insertar llamadas de registro de diagnóstico
Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");


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
> **Muestreo.**  Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)
> 
> 

## <a name="next-steps"></a>Pasos siguientes
[Diagnóstico de errores y excepciones en ASP.NET][exceptions]

[Más información sobre la búsqueda][diagnostic].

## <a name="troubleshooting"></a>Solución de problemas
### <a name="how-do-i-do-this-for-java"></a>¿Cómo se puede hacer para Java?
Utilice los [adaptadores de registro de Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>No aparece la opción de Application Insights en el menú contextual del proyecto
* Compruebe si las herramientas de Application Insights están instaladas en este equipo de desarrollo. En el menú Herramientas de Visual Studio, en Extensiones y actualizaciones, busque Herramientas de Application Insights. Si no se encuentran en la pestaña Instalado, abra la pestaña En línea e instálelas.
* Podría tratarse de un tipo de proyecto no admitido por las herramientas de Application Insights. Use la [instalación manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>No aparece la opción de adaptador en la herramienta de configuración
* Debe instalar primero el marco de registro.
* Si usa System.Diagnostics.Trace, asegúrese de que lo ha [configurado en `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* ¿Tiene la versión más reciente de las herramientas de Application Insights? En el menú **Herramientas** de Visual Studio, elija **Extensiones y actualizaciones** y abra la pestaña **Actualizaciones**. Si las herramientas de Application Insights se encuentran ahí, haga clic para actualizarlas.

### <a name="a-nameemptykeyai-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".
Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Puedo ver seguimientos en Búsqueda de diagnóstico, pero no los otros eventos.
A veces, el paso de todos los eventos y solicitudes por la canalización puede llevar un rato.

### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>¿Qué cantidad de datos se conserva?
Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>No veo algunas de las entradas del registro que esperaba
 Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)

## <a name="a-nameaddanext-steps"></a><a name="add"></a>Pasos siguientes
* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md





<!--HONumber=Nov16_HO3-->


