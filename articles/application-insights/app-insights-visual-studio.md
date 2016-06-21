<properties 
	pageTitle="Trabajo con Application Insights en Visual Studio" 
	description="Análisis de rendimiento y diagnóstico durante la depuración y en producción." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="awills"/>


# Trabajo con Application Insights en Visual Studio

En Visual Studio (2015 y posterior), se pueden analizar los problemas de rendimiento y diagnóstico, tanto en depuración como en producción, mediante los datos de telemetría de [Visual Studio Application Insights](app-insights-overview.md).

Si aún no ha [instalado Application Insights en su aplicación](app-insights-asp-net.md), hágalo ahora.

## <a name="run"></a> Depure el proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, verá un recuento de los eventos que se han registrado.

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Haga clic en este botón para abrir la búsqueda de diagnóstico.



## Búsqueda de diagnóstico

La ventana de búsqueda muestra los eventos que se han registrado. (Si inició la sesión en Azure al configurar Application Insights, podrá buscar los mismos eventos en el portal.)

![Haga clic con el botón derecho en el proyecto y seleccione Application Insights, Búsqueda.](./media/app-insights-visual-studio/34.png)

La búsqueda de texto sin formato funciona en todos los campos de los eventos. Por ejemplo, buscar parte de la dirección URL de una página; o el valor de una propiedad, como la ciudad del cliente; o palabras específicas en un registro de seguimiento.

Haga clic en cualquier evento para ver sus propiedades con todo detalle.

También puede abrir la pestaña de elementos relacionados para ayudar a diagnosticar las solicitudes con errores o las excepciones.


![](./media/app-insights-visual-studio/41.png)



## Concentrador de diagnósticos

El concentrador de diagnósticos (en Visual Studio 2015 o posterior) muestra los datos de telemetría del servidor de Application Insights cuando se generan. Esto funciona incluso si solo ha optado por instalar el SDK, sin conectarlo a un recurso del Portal de Azure.

![Abra la ventana Herramientas de diagnóstico e inspeccione los eventos de Application Insights.](./media/app-insights-visual-studio/31.png)


## Excepciones

Si ha [configurado la supervisión de excepciones](app-insights-asp-net-exceptions.md), los informes de excepciones se mostrarán en la ventana de búsqueda.

Haga clic en una excepción para obtener un seguimiento de la pila. Si el código de la aplicación es abierto en Visual Studio, puede hacer clic para recorrer el seguimiento de la pila hasta dar con la línea correspondiente del código.


![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/17.png)

Además, en la línea de Code Lens sobre cada método, verá un recuento de las excepciones registradas por Application Insights en las últimas 24 horas.

![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/21.png)


## Supervisión local



(Desde Visual Studio 2015 Update 2) Si no ha configurado el SDK para enviar datos de telemetría al portal de Application Insights (para que no haya ninguna clave de instrumentación en ApplicationInsights.config), la ventana diagnóstico mostrará los datos de telemetría de la sesión de depuración más reciente.

Esto es conveniente si ya ha publicado una versión anterior de la aplicación. No quiere que los datos de telemetría de las sesiones de depuración se mezclen con los datos de telemetría en el portal de Application Insights de la aplicación publicada.

También es útil si tiene [datos de telemetría personalizados](app-insights-api-custom-events-metrics.md) que desea depurar antes de enviarlos al portal.


* *En primer lugar, configuré totalmente Application Insights para enviar los datos de telemetría al portal. Pero ahora me gustaría ver los datos de telemetría solo en Visual Studio.*

 * En la configuración de la ventana de búsqueda, hay una opción para buscar diagnósticos locales, incluso si la aplicación envía datos de telemetría al portal.
 * Para detener el envío de datos de telemetría al portal, convierta en comentario la línea `<instrumentationkey>...` de ApplicationInsights.config. Cuando esté listo para enviar de nuevo datos de telemetría al portal, quite los comentarios.



## Para actualizar a futuras versiones del SDK

Para actualizar a una [nueva versión del SDK](app-insights-release-notes-dotnet.md), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.



## Pasos siguientes

||
|---|---
|**[Agregar más datos](app-insights-asp-net-more.md)**<br/>Supervisar el uso, la disponibilidad, las dependencias y las excepciones. Integrar seguimientos de marcos de registro. Escribir telemetría personalizada. | ![Visual Studio](./media/app-insights-asp-net/64.png)
|**[Trabajar con el portal de Application Insights](app-insights-dashboards.md)**<br/>Paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa de dependencias en vivo de la aplicación y la exportación de datos de telemetría. |![Visual studio](./media/app-insights-asp-net/62.png)


 

<!---HONumber=AcomDC_0615_2016-->