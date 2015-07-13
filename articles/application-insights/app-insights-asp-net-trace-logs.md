<properties 
	pageTitle="Exploración de registros de seguimiento de .NET en Application Insights" 
	description="Busque registros generados con Seguimiento, NLog o Log4Net." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Exploración de registros de seguimiento de .NET en Application Insights  

Si usa NLog, log4Net o System.Diagnostics.Trace para realizar el seguimiento de diagnósticos en la aplicación ASP.NET, sus registros se pueden enviar a [Visual Studio Application Insights][start], donde puede explorarlos y buscarlos. Los registros se combinarán con los otros informes de telemetría procedente de su aplicación, y así podrá identificar los seguimientos asociados con el mantenimiento de cada solicitud de usuario y correlacionarlos con otros eventos e informes de excepciones.

También puede escribir seguimientos de registro e informes de eventos y excepciones mediante el SDK de Application Insights.

Si aún no ha [configurado Application Insights para su proyecto][start], hágalo ahora.


##  Instalación de un adaptador para el marco de registro

Si usa un marco de registro, como log4Net, NLog o System.Diagnostics.Trace, puede instalar un adaptador que envíe estos registros a Application Insights junto con otros informes de telemetría.

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Seleccione En línea > Todo, seleccione **Incluir versión preliminar** y busque "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Seleccione el paquete adecuado entre los siguientes:
  + Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

#### Insertar llamadas de registro de diagnóstico

Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");


## Uso de la API de seguimiento directamente

Puede llamar directamente a la API de seguimiento de Application Insights. Los adaptadores de registro usan esta API.

Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");




## Explorar los registros

En la hoja de información general de su aplicación del [portal de Application Insights][portal], elija [Buscar][diagnostic].

![En Application Insights, elija Buscar.](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Búsqueda de diagnóstico](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Por ejemplo, puede:

* Filtrar por seguimientos de registro, o por elementos con determinadas propiedades
* Inspeccionar un elemento específico en detalle
* Encontrar otros informes de telemetría relacionados con la misma solicitud de usuario (es decir, con el mismo OperationId) 
* Guardar la configuración de esta página como favorita


## Pasos siguientes

[Diagnóstico de errores y excepciones en ASP.NET][exceptions]

[Más información sobre Búsqueda de diagnóstico][diagnostic].



## Solución de problemas

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### Puedo ver seguimientos en Búsqueda de diagnóstico, pero no los otros eventos.

A veces, el paso de todos los eventos y solicitudes por la canalización puede llevar un rato.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

## <a name="add"></a>Pasos siguientes

* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md

 
<!--HONumber=62-->