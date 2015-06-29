<properties 
	pageTitle="Incorporación del SDK de Application Insights para supervisar la aplicación de ASP.NET" 
	description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>


# Incorporación del SDK de Application Insights para supervisar la aplicación de ASP.NET

*Application Insights se encuentra en su versión de vista previa.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Application Insights de Visual Studio supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento][detect], y [descubrir cómo se usa la aplicación][knowUsers]. Se puede usar con una amplia variedad de tipos de aplicación. Funciona con las aplicaciones hospedadas en máquinas virtuales de Azure o en sus servidores IIS locales propios, así como con las aplicaciones web de Azure. ([También se tratan las aplicaciones para dispositivos y los servidores Java][start]).

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

En muchos tipos de aplicaciones, [Visual Studio puede agregar Application Insights a la aplicación en cuestión](#ide) casi sin que usted se dé cuenta. Sin embargo, puesto que está leyendo esto para entender mejor el proceso, le guiaremos a través de los pasos manualmente.

#### Antes de comenzar

Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).
* Visual Studio 2013 o posterior.

## <a name="add"></a> 1. Creación de recursos en Application Insights

Inicie sesión en el [Portal de Azure][portal] y cree un nuevo recurso de Application Insights. Elija ASP.NET como tipo de aplicación.

![Haga clic en Nuevo, Application Insights.](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

Un [recurso][roles] de Azure es una instancia de un servicio. Este recurso es donde se le presentará telemetría de su aplicación analizada.

La elección del tipo de aplicación establece el contenido predeterminado de las hojas de recursos y las propiedades que estarán visibles en el [Explorador de métricas][metrics].

####  Realice una copia de la clave de instrumentación.

La clave identifica al recurso y se instalará pronto en el SDK para dirigir los datos al recurso.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)



## <a name="sdk"></a> 2. Instale el SDK en su aplicación.


1. En Visual Studio, edite los paquetes de NuGet de su proyecto de aplicación de escritorio.

    ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. Instale el SDK de Application Insights para aplicaciones web.

    ![Busque "Application Insights"](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)


3. Edite ApplicationInsights.config (que la instalación de NuGet ha agregado). Inserte esto justo antes de la etiqueta de cierre:

    `<InstrumentationKey>` *la clave de instrumentación que copió* `</InstrumentationKey>`

    (También puede [establecer la clave escribiendo código][apikey] en su aplicación.)

#### Para actualizar a futuras versiones del SDK

Para actualizar a una [nueva versión del SDK](app-insights-release-notes-dotnet.md), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.


## <a name="run"></a> 3. Ejecución del proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. Visualización de la telemetría

Vuelva al [portal de Azure][portal] y busque el recurso de Application Insights.


Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas.][perf]

Implemente ahora la aplicación y observe cómo se acumulan los datos.


Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Cuando se implementa una aplicación, los datos se acumulan a menor velocidad.

#### ¿No hay datos?

* Abra el icono [Buscar][diagnostic] para ver los eventos individuales.
* Use la aplicación y abra varias páginas para generar telemetría.
* Espere unos segundos y haga clic en Actualizar.
* Vea [Solución de problemas][qna].

#### ¿Tiene problemas el servidor de compilación?

Consulte [este apartado de la solución de problemas](app-insights-troubleshoot-faq.md#NuGetBuild).


## Complete la instalación

Para obtener la vista completa de 360 grados de la aplicación, debe realizar algunas tareas adicionales:


* [Agregue el SDK de JavaScript a sus páginas web][client] para obtener telemetría basada en el explorador, como recuentos de vista de página, de sesiones y de usuarios, tiempos de carga de página, excepciones de script y poder escribir telemetría personalizada en los scripts de página.
* Agregue seguimiento de dependencias para diagnosticar los problemas ocasionados por bases de datos u otros componentes usados por la aplicación:
 * [en su aplicación web o máquina virtual de Azure][azure]
 * [en el servidor IIS local][redfield]
* [Captura del seguimientos de registros][netlogs] de su marco de registro de favoritos
* [Realice el seguimiento de métricas y eventos personalizado][api] en el cliente, en el servidor o en ambos para obtener más información acerca de cómo se usa la aplicación.
* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.

## <a name="ide"></a> La forma automatizada

Si prefiere usar Visual Studio para realizar la configuración, es muy sencillo.

Necesitará [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o posterior) y una cuenta en [Microsoft Azure](http://azure.com).

#### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio, asegúrese de seleccionar Application Insights.


![Crear un proyecto ASP.NET](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

Visual Studio crea un recurso en Application Insights, agrega el SDK al proyecto y coloca la clave en el `.config` archivo.

Si el proyecto tiene páginas web, también agrega el [SDK de JavaScript][client] a la página web principal.

#### ....o si se trata de un proyecto existente

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

Visual Studio crea un recurso en Application Insights, agrega el SDK al proyecto y coloca la clave en el `.config` archivo.

En este caso, no se agrega el [SDK de JavaScript][client] a las páginas web: le recomendamos que lo haga en el paso siguiente.

#### Opciones de configuración

La primera vez deberá iniciar sesión o registrarse en Microsoft Azure en vista previa. (Al margen de la cuenta de Visual Studio Online).

Si esta aplicación forma parte de una aplicación mayor, es posible que quiera usar **Configurar valor** para colocarla en el mismo grupo de recursos que los demás componentes.

*¿No encuentra la opción Application Insights? Compruebe que está usando Visual Studio 2013 Update 3 o posterior, que las Herramientas de Application Insights se encuentran habilitadas en Extensiones y actualizaciones, y que está creando un proyecto web.*

#### Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)




## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=58_postMigration-->