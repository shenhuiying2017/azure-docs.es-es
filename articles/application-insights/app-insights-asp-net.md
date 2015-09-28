<properties 
	pageTitle="Application Insights para ASP.NET" 
	description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2015" 
	ms.author="awills"/>


# Application Insights para ASP.NET

*Application Insights se encuentra en su versión de vista previa.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[Application Insights de Visual Studio](http://azure.microsoft.com/services/application-insights) supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento][detect], y [descubrir cómo se usa la aplicación][knowUsers]. Se puede usar con una amplia variedad de tipos de aplicación. Funciona con las aplicaciones hospedadas en máquinas virtuales de Azure o en sus servidores IIS locales propios, así como con las aplicaciones web de Azure. ([También se tratan las aplicaciones para dispositivos y los servidores Java][start]).

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-asp-net/10-perf.png)


#### Antes de comenzar

Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).
* Visual Studio 2013, actualización 3 o superior.

## <a name="ide"></a> Adición de Application Insights al proyecto de Visual Studio

#### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio, asegúrese de seleccionar Application Insights.


![Creación de un proyecto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ....o si se trata de un proyecto existente

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Opciones de configuración

La primera vez deberá iniciar sesión o registrarse en Microsoft Azure en vista previa. (Al margen de la cuenta de Visual Studio Online).

Si esta aplicación forma parte de una aplicación mayor, es posible que quiera usar **Configurar valor** para colocarla en el mismo grupo de recursos que los demás componentes.


####<a name="land"></a> ¿Qué hizo "Agregar Application Insights"?

El comando realizó estos pasos (que puede hacer manualmente si lo prefiere):

* Crea un recurso de Application Insights en el [Portal de Azure][portal]. Es donde verá los datos. Recupera la *clave de instrumentación* que identifica el recurso.
* Agrega el paquete NuGet del SDK web de Application Insights al proyecto. Para verlo en Visual Studio, haga clic con el botón secundario en el proyecto y elija Administrar paquetes de NuGet.
* Coloca la clave de instrumentación en `ApplicationInsights.config`.


## <a name="run"></a> Ejecución del proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Apertura de Application Insights

Abra el recurso de Application Insights en el [Portal de Azure][portal].

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)


Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-asp-net/12-first-perf.png)

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


## Adición de supervisión de explorador

La supervisión de explorador o cliente le da datos sobre usuarios, sesiones, vistas de página y excepciones o bloqueos que se producen en el explorador.

![Seleccione Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-asp-net/16-page-views.png)

También podrá escribir su propio código para realizar un seguimiento de cómo trabajan los usuarios con su aplicación, hasta el nivel detallado de clics y pulsaciones de teclas.

#### Si los clientes son exploradores web

Si la aplicación muestra páginas web, agregar un fragmento de código de JavaScript en cada página. Obtenga el código del recurso de Application Insights:

![En la aplicación web, abra Inicio rápido y haga clic en 'Obtener código para supervisar mis páginas web'](./media/app-insights-asp-net/02-monitor-web-page.png)

Observe que el código contiene la clave de instrumentación que identifica al recurso de la aplicación.

[Obtenga más información sobre el seguimiento de páginas web.](app-insights-web-track-usage.md)

#### Si los clientes son aplicaciones de dispositivos

Si la aplicación atiende a clientes como teléfonos u otros dispositivos, agregue el [SDK adecuado](app-insights-platforms.md) a la aplicación del dispositivo.

Si configura el SDK de cliente con la misma clave de instrumentación que el SDK de servidor, se integrarán las dos secuencias para poderlas ver juntas.

## Seguimiento de uso

Cuando ha entregado un nuevo caso de usuario, le gustaría saber cuánto lo están usando sus clientes y si están alcanzando sus objetivos o tienen dificultades. Obtenga una imagen detallada de actividad del usuario mediante la inserción de TrackEvent() y otras llamadas en el código, tanto en el cliente como en el servidor.

[Uso de la API para realizar un seguimiento del uso][api]


## Registros de diagnóstico

[Capture seguimientos del registro][netlogs] desde su marco de registro favorito para ayudarle a diagnosticar cualquier problemas. Aparecerán las entradas de registro en [búsqueda de diagnósticos][diagnostic] junto con los eventos de telemetría de Application Insights.

## Publicación de la aplicación

Si aún no ha publicado la aplicación (desde que agregó Application Insights), hágalo ahora. Vea el crecimiento de los datos en los gráficos a medida que las personas usan la aplicación.


#### ¿No hay datos después de publicar en el servidor?

Abra estos puertos para el tráfico de salida en el firewall del servidor:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`

### Mantenimiento de recursos independientes para desarrollo, prueba y lanzamiento

Para una aplicación importante, es aconsejable enviar los datos de telemetría de depuración, pruebas y producción a [recursos independientes](app-insights-separate-resources.md).




## Adición de contadores de rendimiento del sistema y seguimiento de dependencia

Las [métricas de dependencia](app-insights-dependencies.md) pueden ser muy valiosas a la hora de ayudar a diagnosticar problemas de rendimiento. Miden las llamadas desde su aplicación a bases de datos, API de REST y otros componentes externos.

![](./media/app-insights-asp-net/04-dependencies.png)

Este paso también permite [generar informes de contadores de rendimiento](app-insights-web-monitor-performance.md#system-performance-counters) como ocupación de red, de memoria o de CPU.

#### Si la aplicación se ejecuta en su servidor IIS

Inicie sesión en el servidor con derechos de administrador e instale [Monitor de estado de Application Insights](http://go.microsoft.com/fwlink/?LinkId=506648).

Debe asegurarse de que hay algunos [puertos adicionales abiertos en el firewall de su servidor](app-insights-monitor-performance-live-website-now.md#troubleshooting).

#### Si la aplicación es una aplicación web de Azure

En el panel de control de la aplicación web de Azure, agregue la extensión Application Insights.

![En la aplicación web, Configuración, Extensiones, Agregar, Application Insights](./media/app-insights-asp-net/05-extend.png)

(La extensión solo ayuda a una aplicación que se ha compilado con el SDK. A diferencia del Monitor de estado, no puede instrumentar una aplicación existente).

#### Para supervisar roles de servicios en la nube de Azure

Existe un [procedimiento manual para agregar el monitor de estado](app-insights-cloudservices.md).

## Pruebas web de disponibilidad

[Configure pruebas web][availability] para comprobar desde el exterior que la aplicación está activa y responde.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






## Para actualizar a futuras versiones del SDK

Para actualizar a una [nueva versión del SDK](app-insights-release-notes-dotnet.md), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.



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

 

<!---HONumber=Sept15_HO3-->