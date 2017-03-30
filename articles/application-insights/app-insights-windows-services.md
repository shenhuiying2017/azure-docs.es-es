---
title: Servicios de Azure Application Insights para Windows y roles de trabajo | Microsoft Docs
description: "Agregue manualmente el SDK de Application Insights a su aplicación de ASP.NET para analizar el uso, la disponibilidad y el rendimiento."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: df23a55197d15946f16868d14c6db08dcba4df19
ms.lasthandoff: 03/16/2017


---
# <a name="manually-configure-application-insights-for-aspnet-4-applications"></a>Configuración manual de Application Insights para aplicaciones ASP.NET 4
[Application Insights](app-insights-overview.md) es una herramienta extensible para desarrolladores web que ayuda a supervisar el rendimiento y el uso de la aplicación activa. Se puede configurar manualmente para supervisar servicios de Windows, roles de trabajo y otras aplicaciones de ASP.NET. En el caso de las aplicaciones web, la configuración manual es una alternativa a la [configuración automática](app-insights-asp-net.md) que ofrece Visual Studio.

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Antes de comenzar
Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).
* Visual Studio 2013 o posterior.

## <a name="add"></a>1. Creación de recursos en Application Insights
Inicie sesión en el [Portal de Azure](https://portal.azure.com/)y cree un nuevo recurso de Application Insights. Elija ASP.NET como el tipo de aplicación.

![Haga clic en Nuevo, Application Insights.](./media/app-insights-windows-services/01-new-asp.png)

Un [recurso](app-insights-resources-roles-access-control.md) de Azure es una instancia de un servicio. Este recurso es donde se le presentará telemetría de su aplicación analizada.

La elección del tipo de aplicación establece el contenido predeterminado de las hojas de recursos y las propiedades que estarán visibles en el [Explorador de métricas](app-insights-metrics-explorer.md).

#### <a name="copy-the-instrumentation-key"></a>Copia de la clave de instrumentación
La clave identifica al recurso y se instalará pronto en el SDK para dirigir los datos al recurso.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-windows-services/02-props-asp.png)

Los pasos que acaba de realizar para crear un recurso nuevo son una excelente manera de comenzar a supervisar cualquier aplicación. Ahora puede enviarle datos.

## <a name="sdk"></a>2. Instale el SDK en su aplicación.
Instalar y configurar el SDK de Application Insights varía en función de la plataforma en la que trabaja. En el caso de las aplicaciones de ASP.NET, es fácil.

1. En Visual Studio, edite los paquetes de NuGet de su proyecto de aplicación web.
   
    ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-windows-services/03-nuget.png)
2. Instale el SDK de Application Insights para aplicaciones web.
   
    ![Busque "Application Insights"](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *¿Puedo usar otros paquetes?*
   
    Sí. Elija la API principal (Microsoft.ApplicationInsights) si solo desea usar la API para enviar su propia telemetría. El paquete de Windows Server incluye automáticamente la API principal más otra serie de paquetes, como la recopilación de contadores de rendimiento y la supervisión de dependencias. 

#### <a name="to-upgrade-to-future-sdk-versions"></a>Para actualizar a futuras versiones del SDK
De vez en cuando, lanzamos una versión nueva del SDK.

Para actualizar a una [nueva versión del SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione **Microsoft.ApplicationInsights.Web** y elija **Actualizar**.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.

## <a name="3-send-telemetry"></a>3. Envío de datos de telemetría
**Si ha instalado el paquete central de la API:**

* Establezca la clave de instrumentación en el código, por ejemplo en `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *su clave* `";` 
* [Escriba su propia telemetría mediante la API](app-insights-api-custom-events-metrics.md#ikey).

**Si ha instalado otros paquetes de Application Insights,** puede, si lo prefiere, usar el archivo .config para establecer la clave de instrumentación:

* Edite ApplicationInsights.config (que la instalación de NuGet agregó). Inserte esto justo antes de la etiqueta de cierre:
  
    `<InstrumentationKey>` *la clave de instrumentación que copió* `</InstrumentationKey>`
* Asegúrese de que las propiedades de ApplicationInsights.config en el Explorador de soluciones se establecen en **Build Action = Content, Copy to Output Directory = Copy**.

Es útil establecer la clave de instrumentación en el código si desea [cambiar la clave para diferentes configuraciones de compilación](app-insights-separate-resources.md). Si establece la clave en el código, no tiene que establecerla en el archivo `.config`.

## <a name="run"></a> Ejecución del proyecto
Use **F5**para ejecutar la aplicación y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![Recuento de eventos en Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Visualización de los datos de telemetría
Vuelva al [Portal de Azure](https://portal.azure.com/) y busque el recurso de Application Insights.

Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-windows-services/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas](app-insights-web-monitor-performance.md)

#### <a name="no-data"></a>¿No hay datos?
* Use la aplicación y abra varias páginas para generar telemetría.
* Abra el icono [Buscar](app-insights-diagnostic-search.md) para ver los eventos individuales. A veces, los eventos tardan un poco en llegar a través de la canalización de métricas.
* Espere unos segundos y haga clic en **Actualizar**. Los gráficos se actualizan automáticamente de forma periódica, pero puede actualizarlos manualmente si espera que aparezcan algunos datos.
* Vea [Solución de problemas](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publicación de la aplicación
Implemente ahora la aplicación en su servidor o en Azure y observe cómo se acumulan los datos.

![Uso de Visual Studio para publicar la aplicación](./media/app-insights-windows-services/15-publish.png)

Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Al implementar la aplicación en la configuración de lanzamiento, los datos se acumulan más lentamente.

#### <a name="no-data-after-you-publish-to-your-server"></a>¿No hay datos después de publicar en el servidor?
Abra estos puertos para el tráfico de salida en el firewall del servidor:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>¿Tiene problemas el servidor de compilación?
Consulte [este apartado de la solución de problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Si la aplicación genera muchos datos de telemetría (y está usando la versión 2.0.0-beta3, o una posterior, del SDK de ASP.NET), el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de solamente una fracción representativa de los eventos. Sin embargo, los eventos relacionados con la misma solicitud se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre ellos. 
> [Más información sobre el muestreo](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Pasos siguientes
* [Agregue más telemetría](app-insights-asp-net-more.md) para obtener la visión completa de 360 grados de la aplicación.


