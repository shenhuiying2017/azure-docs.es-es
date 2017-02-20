---
title: "Configurar el análisis de aplicaciones web para ASP.NET con Application Insights | Microsoft Docs"
description: "Configurar el análisis de rendimiento, disponibilidad y uso para el sitio web de ASP.NET, hospedado localmente o en Azure."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 919f67a422faad2ba4c19e1f11f8e873098e8bd1
ms.openlocfilehash: 9c27cfb674a7743c7cfe47b35b263da48c9c564e


---
# <a name="set-up-application-insights-for-aspnet"></a>Configurar Application Insights para ASP.NET
[Azure Application Insights](app-insights-overview.md) supervisa la aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento](app-insights-detect-triage-diagnose.md), y [descubrir cómo se usa la aplicación](app-insights-overview-usage.md).  Funciona con las aplicaciones hospedadas en máquinas virtuales en la nube o en sus servidores IIS locales propios, así como con las aplicaciones web de Azure.

## <a name="before-you-start"></a>Antes de comenzar
Necesita:

* Visual Studio 2013, actualización 3 o superior. Es mejor que sea superior.
* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com). 

Puede consultar otros artículos si está interesado en:

* [La instrumentación de una aplicación web en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md)
* [Servicios en la nube de Azure](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Incorporación del SDK de Application Insights
### <a name="if-its-a-new-project"></a>Si se trata de un proyecto nuevo:
Cuando cree un proyecto nuevo en Visual Studio, asegúrese de que Application Insights está seleccionado. 

![Creación de un proyecto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>....o si se trata de un proyecto existente
Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y elija **Agregar telemetría de Application Insights** o **Configurar Application Insights**.

![Elija Agregar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ¿Es un proyecto de ASP.NET Core? [Siga estas instrucciones para corregir unas pocas líneas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. Ejecutar la aplicación
Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparece un recuento de los eventos que se han registrado. 

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. Visualización de los datos de telemetría...
### <a name="-in-visual-studio"></a>... en Visual Studio
Abra la ventana de Application Insights en Visual Studio: haga clic en el botón Application Insights, o bien haga clic con el botón derecho en el proyecto en el Explorador de soluciones, seleccione `Application Insights` y después haga clic en `Search Live Telemetry`:

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-asp-net/55.png)

Esta vista ("Datos de la sesión de depuración") muestra los datos de telemetría generados en el servidor de la aplicación. Experimente con los filtros y haga clic en cualquier evento para ver más información al respecto.

* *¿No hay datos? Asegúrese de que el intervalo de tiempo es correcto y haga clic en el icono de búsqueda.*

[Más información acerca de las herramientas de Application Insights en Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>... en el portal
Salvo que elija *Install SDK only* (Instalar SDK solamente), también puede ver la telemetría en el portal web de Application Insights. 

El portal tiene más gráficos, herramientas de análisis y paneles que Visual Studio. 

Abra el recurso de Application Insights, ya sea mediante el inicio de sesión en [Azure Portal](https://portal.azure.com/) y buscarlos allí, o haga clic en el proyecto en Visual Studio y deje que le lleve allí.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

* *¿Error de acceso? Si tiene más de un conjunto de credenciales de Microsoft, puede iniciar sesión con un conjunto erróneo. En el portal, cierre la sesión y vuelva a iniciarla.*

El portal se abrirá en una vista de los datos de telemetría desde su aplicación: ![Página de información general de Application Insights](./media/app-insights-asp-net/66.png).

Haga clic en un icono para ver su contenido con mayor detalle.

### <a name="more-detail-in-the-portal"></a>Más detalles en el portal

* [**Live Metrics Stream**](app-insights-live-stream.md) muestra la telemetría casi al instante.

    ![En la hoja de información general, haga clic en Live Stream.](./media/app-insights-asp-net/livestream.png)

    Abra Live Stream al mismo tiempo que se ejecuta la aplicación, para que puedan conectarse.

    Live Stream solo muestra telemetría durante un minuto después de enviarla. Para más investigaciones históricas, use Search, Explorador de métricas y Analytics. Los datos pueden tardar unos minutos en aparecer.

* [**Search** ](app-insights-diagnostic-search.md) muestra los eventos individuales, como solicitudes excepciones y vistas de página. Puede filtrar por tipo de evento, coincidencia de términos y valores de propiedad. Haga clic en cualquier evento para ver sus propiedades y eventos relacionados. 

    ![En la hoja de información general, haga clic en Search.](./media/app-insights-asp-net/search.png)

 * En modo de desarrollo, pueden ver una gran cantidad de eventos de dependencia (AJAX). Son las sincronizaciones entre el explorador y el emulador del servidor. Para ocultarlas, haga clic en el filtro Dependencia.
* [**Las métricas agregadas**](app-insights-metrics-explorer.md) como las tasas de solicitud y de error aparecen en los gráficos. Haga clic en cualquiera de los gráficos para abrir una hoja en la que encontrará más información. Haga clic en la etiqueta **Editar** de cualquier gráfico para establecer filtros o el tamaño, entre otros.
    
    ![En la hoja de información general, haga clic en cualquier gráfico.](./media/app-insights-asp-net/metrics.png)

[Más información acerca del uso de Application Insights en el Portal de Azure](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. Publicación de la aplicación
Publique su aplicación en el servidor IIS o en Azure. Consulte [Secuencia de métricas en directo](app-insights-metrics-explorer.md#live-metrics-stream) para asegurarse de que todo está ejecutándose sin problemas.

Verá cómo sea crea la telemetría en el portal de Application Insights, donde puede supervisar las métricas, buscar la telemetría y configurar los [paneles](app-insights-dashboards.md). También puede usar el potente [lenguaje de consulta de Analytics](app-insights-analytics.md) para analizar el uso y el rendimiento o buscar eventos específicos. 

Puede igualmente seguir analizando la telemetría en [Visual Studio](app-insights-visual-studio.md) con herramientas como búsqueda de diagnóstico y [Tendencias](app-insights-visual-studio-trends.md).

> [!NOTE]
> Si la aplicación envía suficiente telemetría que se acerque a las [limitaciones de peticiones](app-insights-pricing.md#limits-summary), se activará el [muestreo](app-insights-sampling.md) automático. El muestreo reduce la cantidad de datos de telemetría enviados desde su aplicación, a la vez que conserva los datos correlacionados para fines de diagnóstico.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> ¿Qué hizo "Agregar Application Insights"?
Application Insights envía datos de telemetría desde su aplicación al portal de Application Insights (que está hospedado en Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Por tanto, el comando realizó tres operaciones:

1. Agregar el paquete NuGet del SDK web de Application Insights al proyecto. Para verlo en Visual Studio, haga clic con el botón secundario en el proyecto y elija Administrar paquetes de NuGet.
2. Crear un recurso de Application Insights en el [Portal de Azure](https://portal.azure.com/). Es donde verá los datos. Recupera la *clave de instrumentación* , que identifica el recurso.
3. Insertar la clave de instrumentación en `ApplicationInsights.config`, con el fin de que el SDK pueda enviar datos de telemetría al portal.

Si lo desea, puede realizar estos pasos manualmente para [ASP.NET 4](app-insights-windows-services.md) o [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="to-upgrade-to-future-sdk-versions"></a>Para actualizar a futuras versiones del SDK
Para actualizar a una [nueva versión del SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), vuelva a abrir el Administrador de paquetes NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.

## <a name="add-more-telemetry"></a>Incorporación de telemetría adicional
### <a name="dependencies-exceptions-and-performance-counters"></a>Dependencias, excepciones y contadores de rendimiento

[Instale el monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648) en cada una de las máquinas del servidor de IIS para obtener telemetría adicional acerca de las aplicaciones web.

Si ya está instalado, no es preciso hacer nada. 

Puede que ya haya utilizado el monitor de estado para iniciar la supervisión de una aplicación en tiempo de ejecución. 

Con el monitor de estado, además del SDK de tiempo de compilación, obtendrá un conjunto más completo de telemetría que incluye:

* [Contadores de rendimiento](app-insights-performance-counters.md) - 
  CPU, memoria, disco y otros contadores de rendimiento relacionados con la aplicación. 
* [Excepciones](app-insights-asp-net-exceptions.md): telemetría más detallada para algunas excepciones.
* [Dependencias](app-insights-asp-net-dependencies.md), que incluyen valores devueltos.

### <a name="web-pages-and-single-page-apps"></a>Páginas web y aplicaciones de una página
1. [Agregue el fragmento de código de JavaScript](app-insights-javascript.md) a sus páginas web para mejorar las hojas Explorador y Uso con datos sobre vistas de página, tiempos de carga, excepciones de explorador, rendimiento de las llamadas AJAX, y recuentos de usuarios y sesiones.
2. [Eventos personalizados con código](app-insights-api-custom-events-metrics.md) para el recuento, la hora o la medición de las acciones del usuario.


### <a name="diagnostic-code"></a>Código de diagnóstico
¿Tiene un problema? Si desea insertar código en la aplicación para que le ayude con el diagnóstico, tiene varias opciones:

* [Capturar seguimientos de registros](app-insights-asp-net-trace-logs.md): si ya utiliza Log4N, NLog o System.Diagnostics.Trace para registrar eventos de seguimiento, puede enviar el resultado a Application Insights para que pueda correlacionarlo con las solicitudes, buscar en el mismo y analizarlo. 
* [Eventos y métricas personalizados](app-insights-api-custom-events-metrics.md): use TrackEvent() y TrackMetric() en el código del servidor o de la página web.
* [Etiquetar la telemetría con propiedades adicionales](app-insights-api-filtering-sampling.md#add-properties)

Use [Buscar](app-insights-diagnostic-search.md) para buscar y correlacionar eventos específicos y [Análisis](app-insights-analytics.md) para realizar consultas más eficaces.

## <a name="alerts"></a>Alertas
Sea el primero en saber si la aplicación tiene problemas. (¡No espere a que se lo digan los usuarios!) 

* [Cree pruebas web](app-insights-monitor-web-app-availability.md) para asegurarse de que el sitio sea visible en la web.
* Los [diagnósticos proactivos](app-insights-proactive-diagnostics.md) se ejecutan automáticamente (si la aplicación tiene una cantidad mínima de tráfico determinada). No tiene que hacer nada para configurarlos. Le indican si la aplicación tiene una tasa de solicitudes con error inusual.
* [Establezca alertas de métricas](app-insights-alerts.md) que le adviertan si una métrica cruza un umbral. Puede establecerlas en las métricas personalizadas que codifique en la aplicación.

De forma predeterminada, se envían notificaciones de alerta al propietario de la suscripción de Azure. 

![ejemplo de correo electrónico de alerta](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Versión y seguimiento de versiones
### <a name="track-application-version"></a>Seguimiento de la versión de la aplicación
Asegúrese de que el proceso de MSBuild genera `buildinfo.config` . En su archivo .csproj, agregue:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Cuando tenga la información de la compilación, el módulo web de Application Insights agregará automáticamente la **versión de la aplicación** como una propiedad a cada elemento de telemetría. Puede filtrar por versión al realizar [búsquedas de diagnósticos](app-insights-diagnostic-search.md) o al [explorar métricas](app-insights-metrics-explorer.md). 

Sin embargo, tenga en cuenta que el número de versión de compilación la genera únicamente MS Build, no la compilación de desarrollador en Visual Studio.

### <a name="release-annotations"></a>Anotaciones de la versión
Si usa Visual Studio Team Services, puede [obtener un marcador de anotación](app-insights-annotations.md) agregado a los gráficos, siempre que publique una nueva versión.

![ejemplo de anotación de la versión](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Pasos siguientes
|  |
| --- | --- |
| **[Trabajo con Application Insights en Visual Studio](app-insights-visual-studio.md)**<br/>Depuración con telemetría, búsqueda de diagnóstico, profundización en el código. |
| **[Trabajo con el portal de Application Insights](app-insights-dashboards.md)**<br/>Paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa activo de dependencias de la aplicación y exportación de la telemetría. |




<!--HONumber=Feb17_HO3-->


