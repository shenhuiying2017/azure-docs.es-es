<properties 
	pageTitle="Seguir el uso en aplicaciones web" 
	description="Registre las actividades del usuario." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# Seguir el uso de aplicaciones web

Descubra cómo se está usando su aplicación web. Configure análisis de uso y sabrá qué páginas están mirando los usuarios, cuántos de ellos vuelven y la frecuencia con que visitan su sitio. Agregue algunos [eventos y métricas personalizados][track] y podrá analizar en detalle las características más usadas, los errores más comunes y optimizar la aplicación para tener éxito con los usuarios.

La telemetría se recopila desde el cliente y el servidor. Los datos de cliente se recopilan desde todos los exploradores web modernos y los datos del servidor se pueden recopilar si su plataforma es ASP.NET. (No tiene que ejecutarse en Azure.) 

* [Configurar el análisis de uso web](#webclient)
* [Análisis de uso](#usage)
* [Recuentos de páginas personalizadas para aplicaciones de una página](#spa)
* [Inspección de los eventos de página individuales](#inspect)
* [Seguimiento detallado con eventos y métricas personalizados](#custom)
* [Vídeo](#video)

## <a name="webclient"></a> Configuración de análisis de clientes web

#### Obtener un recurso de Application Insights en Azure

**Si está desarrollando una aplicación ASP.NET** y todavía no lo ha hecho, [agregue Application Insights al proyecto web][start]. 

**Si su plataforma de sitio web no es ASP.NET:** regístrese en [Microsoft Azure](http://azure.com), vaya al [Portal de vista previa](https://portal.azure.com) y agregue un recurso de Application Insights.

![](./media/appinsights/appinsights-11newApp.png)

(Puede volver a ella más adelante con el botón Examinar.)



#### Agregar nuestro script a las páginas web

En Inicio rápido, obtenga el script para páginas web.

![](./media/appinsights/appinsights-06webcode.png)

Inserte el script justo antes de la etiqueta </head> de cada página que desea seguir. Si su sitio web tiene una página maestra, puede colocar el script allí. Por ejemplo, en un proyecto de ASP.NET MVC, la colocaría en View\Shared\_Layout.cshtml

## <a name="usage"></a>Análisis de uso

Ejecute su sitio web, utilícelo para generar la telemetría y espere entre 1 y 2 minutos. Puede ejecutarlo con F5 en la máquina de desarrollo o implementarlo en el servidor.

En la hoja de información general de la aplicación, aparecen estos mosaicos de uso:

![](./media/appinsights/appinsights-47usage.png)

*¿Aún no hay datos? Haga clic en **Actualizar** en la parte superior de la página.*

* **Sesiones por explorador**

    Una *sesión* es un período que se inicia cuando un usuario abre una página del sitio web y termina si el usuario no envía ninguna solicitud web durante un tiempo de espera de 30 minutos. 

    Haga clic en las distintas opciones para ver el gráfico con mayor detalle.

* **Vistas de página principales**

    Muestra los recuentos totales de las últimas 24 horas.

    Haga clic en el mosaico de vistas de página para obtener un historial más detallado. Para obtener un historial más largo, puede cambiar el intervalo de tiempo del informe.

![](./media/appinsights/appinsights-49usage.png)


Haga clic en un gráfico para ver otras métricas que se pueden mostrar o agregue un gráfico nuevo y seleccione las métricas que muestra.

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] Desactive *todas* las métricas para habilitarlas todas. Las métricas solo se pueden mostrar en algunas combinaciones. Cuando selecciona una métrica, se deshabilitan las incompatibles.



## <a name="spa"></a> Recuentos de páginas personalizadas para aplicaciones de una página

De forma predeterminada, se realiza un recuento de página cada vez que se carga una página nueva en el explorador del cliente.  Sin embargo, tal vez quiera realizar un recuento de otras vistas de página. Por ejemplo, una página puede mostrar su contenido en pestañas y a usted puede interesarle realizar el recuento de una página cuando el usuario cambia de pestaña. O el código JavaScript de la página puede cargar contenido nuevo sin cambiar la URL del explorador. 

Inserte una llamada de JavaScript como esta en el lugar adecuado del código de cliente:

    appInsights.trackPageView(myPageName);

El nombre de página puede contener los mismos caracteres que una URL, aunque se ignorarán los elementos situados tras "#" o "?".


## <a name="inspect"></a> Inspeccionar eventos de vista de página individuales

Por lo general, Application Insights analiza la telemetría de vista de página y usted solo verá informes acumulativos, cuya media se ha calculado en función de todos los usuarios. Pero a efectos de depuración, también puede ver eventos de vista de página individuales.

En la hoja Búsqueda de diagnóstico, establezca Filtros en Vista de página.

![](./media/appinsights/appinsights-51searchpageviews.png)

Seleccione el evento que desea ver con mayor detalle.

> [AZURE.NOTE] Si desea usar [Buscar][diagnostic], tenga en cuenta que deben coincidir las palabras completas: "Acerc" y "cerca" no coinciden con "Acerca", aunque "Acerc* " sí. Tampoco puede iniciar un término de búsqueda con un carácter comodín. Por ejemplo, "*cerc" no coincide con "Acerca". 

> [Más información sobre la búsqueda de diagnóstico][diagnostic]

## <a name="custom"></a> Seguimiento detallado con eventos y métricas personalizados

¿Desea averiguar qué hacen los usuarios con su aplicación? Mediante la inserción de llamadas en el código de cliente y servidor, puede enviar su propia telemetría a Application Insights. Por ejemplo, podría averiguar el número de usuarios que crean pedidos sin completarlos o qué errores de validación aparecen con más frecuencia, o el promedio de puntuación en un juego.

[Más información acerca de la API de eventos y métricas personalizados][track].

## <a name="video"></a> Vídeo: Seguimiento del uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Pasos siguientes

[Seguimiento de uso con eventos y métricas personalizados][track]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=35.2-->

<!--HONumber=46--> 
