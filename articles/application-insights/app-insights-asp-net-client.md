<properties 
	pageTitle="Seguimiento de dependencia en Application Insights" 
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
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Configurar Application Insights: páginas web

*Application Insights se encuentra en su versión de vista previa.*


[Application Insights de Visual Studio](http://azure.microsoft.com/services/application-insights) supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento][detect], y [descubrir cómo se usa la aplicación][knowUsers].


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Obtenga información sobre el rendimiento y el uso de sus páginas web. Agregue Visual Studio Application Insights a sus páginas y averigüe cuántos usuarios tiene, la frecuencia con que vuelven y las páginas que más usan. También obtendrá informes de tiempos de carga y las posibles excepciones. Agregue algunos [eventos y métricas personalizados][api] y podrá analizar en detalle las características más usadas y los errores más comunes; asimismo, podrá optimizar la página para tener éxito con los usuarios.

![Seleccione Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-asp-net-client/16-page-views.png)

Si ya ha configurado la telemetría de servidor para su aplicación web [ASP.NET](app-insights-asp-net.md) o [Java](app-insights-java-get-started.md), tendrá una idea clara tanto desde el punto de vista del cliente como del servidor. Las dos secuencias se integrarán en el portal de Application Insights.

## Abrir su recurso de Application Insights

Inicie sesión en el [portal de Azure](http://portal.azure.com).

Si ya ha configurado la supervisión del lado del servidor de su aplicación, ya tendrá un recurso:

![Seleccione Examinar, Servicios para desarrolladores, Application Insights.](./media/app-insights-asp-net-client/01-find.png)

Si no tiene uno, créelo:

![Seleccione Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-asp-net-client/01-create.png)


## Agregar el script de SDK a la aplicación o la página web

En Inicio rápido, obtenga el script para páginas web:

![En la hoja de información general de su aplicación, elija Inicio rápido, Obtener código para supervisar mis páginas web. Copie el script.](./media/app-insights-asp-net-client/02-monitor-web-page.png)

Inserte el script justo antes de la etiqueta &lt;/head&gt; de cada página de la que quiera realizar el seguimiento. Si su sitio web tiene una página maestra, puede colocar el script allí. Por ejemplo:

* En un proyecto de ASP.NET MVC, lo colocaría en View\\Shared\\_Layout.cshtml.
* En un sitio de SharePoint, en el panel de control, abra [Configuración del sitio/Página maestra](app-insights-sharepoint.md).

El script contiene la clave de instrumentación que dirige los datos al recurso de Application Insights.

*(Si está usando un marco de página web conocido, mire a ver si encuentra adaptadores de Application Insights). Por ejemplo, hay [un módulo AngularJS](http://ngmodules.org/modules/angular-appinsights)).*


## <a name="run"></a>Ejecución de la aplicación

Ejecute la aplicación web, úsela un rato para generar datos de telemetría y espere unos segundos. Puede ejecutarla con la tecla **F5** en la máquina de desarrollo, o publicarla y dejar que los usuarios jueguen con ella.

Si quiere comprobar la telemetría que envía una aplicación web a Application Insights, use las herramientas de depuración del explorador (**F12** en muchos exploradores). Los datos se envían a dc.services.visualstudio.com.

## Exploración de los datos

En la hoja de información general de la aplicación, hay un gráfico cerca de la parte superior que muestra el tiempo promedio para cargar páginas en el explorador:


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*¿Aún no hay datos? Haga clic en **Actualizar** en la parte superior de la página. ¿Todavía nada? Consulte [Solución de problemas][qna].*

Haga clic en ese gráfico y obtendrá una versión más detallada:

![](./media/app-insights-asp-net-client/07-client-perf.png)

Se trata de un gráfico apilado que divide el tiempo de carga total de páginas en los [intervalos estándar definidos por W3C](http://www.w3.org/TR/navigation-timing/#processing-model).

![](./media/app-insights-asp-net-client/08-client-split.png)

Tenga en cuenta que el tiempo de *conexión de red* es normalmente más bajo de lo que cabría esperar, porque es una media de todas las solicitudes del explorador al servidor. Muchas de las solicitudes individuales tienen un tiempo de conexión de 0 porque ya hay una conexión activa con el servidor.


### Rendimiento por página

Más abajo en la hoja de detalles, hay una cuadrícula segmentada por dirección URL de página:


![](./media/app-insights-asp-net-client/09-page-perf.png)

Si quiere ver el rendimiento de las páginas con el paso del tiempo, haga doble clic en la cuadrícula y cambie su tipo de gráfico:

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## Información general del uso de clientes

De vuelta en la hoja de información general, haga clic en **Uso**:

![](./media/app-insights-asp-net-client/14-usage.png)

* **Usuarios:**: el número de usuarios diferentes durante el intervalo de tiempo del gráfico. (Las cookies se usan para identificar a los usuarios que vuelven).
* **Sesiones:**: una sesión se cuenta cuando un usuario no ha realizado ninguna solicitud durante 30 minutos.
* **Vistas de página**: cuenta el número de llamadas a trackPageView(), normalmente se llama una vez en cada página web.


### Siga haciendo clic hasta llegar a información más detallada.

Haga clic en cualquiera de los gráficos para ver su contenido con mayor detalle. Observe que puede cambiar el intervalo de tiempo de los gráficos.

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


Haga clic en un gráfico para ver otras métricas que se pueden mostrar o agregue un gráfico nuevo y seleccione las métricas que muestra.

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]Las métricas solo se pueden mostrar en algunas combinaciones. Cuando selecciona una métrica, se deshabilitan las incompatibles.



## Recuentos de página personalizados

De forma predeterminada, se realiza un recuento de página cada vez que se carga una página nueva en el explorador del cliente. Sin embargo, tal vez quiera realizar un recuento de otras vistas de página. Por ejemplo, una página puede mostrar su contenido en pestañas y a usted puede interesarle realizar el recuento de una página cuando el usuario cambia de pestaña. O el código JavaScript de la página puede cargar contenido nuevo sin cambiar la URL del explorador.

Inserte una llamada de JavaScript como esta en el lugar adecuado del código de cliente:

    appInsights.trackPageView(myPageName);

El nombre de página puede contener los mismos caracteres que una URL, aunque se ignorarán los elementos situados tras "#" o "?".


## Inspección de eventos de vista de página individuales

Por lo general, Application Insights analiza la telemetría de vista de página y usted solo verá informes acumulativos, cuya media se ha calculado en función de todos los usuarios. Pero a efectos de depuración, también puede ver eventos de vista de página individuales.

En la hoja Búsqueda de diagnóstico, establezca Filtros en Vista de página.

![](./media/app-insights-asp-net-client/12-search-pages.png)

Seleccione el evento que desea ver con mayor detalle. En la página de detalles, haga clic en "..." para ver aún más detalles.

> [AZURE.NOTE]Si usa [Buscar][diagnostic], tenga en cuenta que tiene que hacer coincidir palabras completas: "Acerc" y "cerca de" no coinciden con "Acerca de", pero "Acerca d*" sí lo hace. Tampoco puede iniciar un término de búsqueda con un carácter comodín. Por ejemplo, "*cerc" no coincide con "Acerca".

> [Más información sobre Búsqueda de diagnóstico][diagnostic]

### Propiedades de la vista de página

* **Duración de la vista de página**&#151;Tiempo que se tarda en cargar la página e iniciar la ejecución de scripts. En concreto, el intervalo entre el inicio de la carga de la página y la ejecución de trackPageView. Si mueve trackPageView desde su posición habitual después de la inicialización del script, reflejará un valor diferente.

## Seguimiento de uso personalizado

¿Desea averiguar qué hacen los usuarios con su aplicación? Mediante la inserción de llamadas en el código de cliente y servidor, puede enviar su propia telemetría a Application Insights. Por ejemplo, puede averiguar el número de usuarios que crean pedidos sin completarlos o qué errores de validación aparecen con más frecuencia, o el promedio de puntuación en un juego.

* [Más información sobre la API de eventos y métricas personalizados][api].
* [Referencia de API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a> Vídeo: Seguimiento del uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Pasos siguientes

- [Instalación del SDK](../article/application-insights/app-insights-asp-net.md#selector1)
- [Seguimiento del uso con eventos y métricas personalizados][api]
- [Disponibilidad](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [Aplicación ya activa](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
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
[start]: app-insights-overview.md


 

<!---HONumber=Oct15_HO3-->