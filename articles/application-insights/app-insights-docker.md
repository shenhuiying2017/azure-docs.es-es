<properties 
	pageTitle="Supervisión de un host Docker en Application Insights" 
	description="En Application Insights se pueden mostrar los contadores de rendimiento, los eventos y las excepciones del host Docker, además de la telemetría de las aplicaciones en contenedor." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Supervisión de un host Docker en Application Insights

En Application Insights puede representarse los contadores de rendimiento y los informes de excepciones de un host [Docker](https://www.docker.com/). Instale la aplicación [Application Insights](app-insights-overview.md) en un contenedor de su host, y se mostrarán los contadores de rendimiento generales del host, así como de las demás imágenes.

Con Docker, distribuye sus aplicaciones en contenedores ligeros junto con todas las dependencias. Estos contenedores se ejecutan en cualquier equipo host que ejecute un motor Docker.

Application Insights puede supervisar el rendimiento y la actividad de los contenedores y el host Docker.

![ejemplo](./media/app-insights-docker/00.png)

(Y por supuesto, si usted es el propietario del origen de cualquiera de las aplicaciones de los contenedores, [instale el SDK de Application Insights](app-insights-java-live) en cada uno de ellos para obtener el registro detallado de rendimiento y el seguimiento de uso).

## Creación de recursos en Application Insights

Un recurso de Application Insights es el lugar donde verá y analizará los datos. Se hospeda en el Portal de Microsoft Azure.

1.	En primer lugar, necesita una suscripción a [Microsoft Azure](https://azure.com). (Hay una opción de pago por uso en la que no paga nada si no usa los servicios. Además, puede usar el nivel Gratis de Application Insights).
2.	Inicie sesión en el [Portal de Azure](https://portal.azure.com) y cree un nuevo recurso de Application Insights. Elija Otro para el tipo de aplicación. (Esta opción solo afecta a la selección inicial de los gráficos que ve, y no es demasiado importante).

    ![ejemplo](./media/app-insights-docker/01-new.png)
3.	Siempre puede llegar a su nuevo recurso de supervisión mediante la opción Examinar, Todo. Pero probablemente también opte por poner un icono para la aplicación en el panel de inicio que aparece cada vez que va al portal.

    Abra ahora el nuevo recurso.
4.	Agregue el icono de Docker; para ello, elija **Editar**, luego **Agregar iconos**, y arrastre el icono de Docker desde la galería. Al principio solo se muestran gráficos vacíos.

    ![ejemplo](./media/app-insights-docker/03.png)

5. Haga clic en la lista desplegable **Essentials** y copie la clave de instrumentación. Se usará para indicar al SDK dónde puede enviar sus datos de telemetría.

Tenga a mano la ventana del explorador, volverá a ella pronto para examinar los datos de telemetría.


## Instalación de Application Insights en el host
 
Ahora que tiene algún sitio para mostrar los datos de telemetría, puede configurar la aplicación que los recopilará y enviará. 1. Inicie sesión en el host Docker. 2. Busque la imagen de Microsoft Application Insights en Docker Hub y extráigala en su host. 3. Establezca la clave de instrumentación:

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. Ejecute la imagen.

## Visualización de la telemetría

Vuelva al recurso de Application Insights en el Portal de Azure.

Pronto verá los datos que llegan del contenedor de Docker, en especial si tiene otros contenedores que se ejecutan en el motor de Docker.

Haga clic en cualquiera de los gráficos para ver su contenido con mayor detalle.

Estas son algunas de las vistas que puede obtener.

### Contadores de rendimiento por host, actividad por imagen


![ejemplo](./media/app-insights-docker/10.png)

Haga clic en cualquier nombre de imagen para más detalles. Para personalizar la vista, haga clic en cualquiera de los gráficos o en el encabezado de cuadrícula, o bien use Agregar gráfico.

[Más información sobre el Explorador de métricas](app-insights-metrics-explorer.md).

## Eventos individuales


![ejemplo](./media/app-insights-docker/12.png)

Para investigar eventos individuales, haga clic en [Búsqueda](app-insights-diagnostic-search.md). Busque y filtre para encontrar los eventos deseados. Haga clic en cualquier evento para más detalles.
 
## Excepciones por el nombre del contenedor
 

![ejemplo](./media/app-insights-docker/14.png)



## Preguntas y respuestas

*¿Qué me da Application Insights que no me dé Docker?*

* Desglose detallado de los contadores de rendimiento por contenedor e imagen.
* Integración de datos de contenedores y aplicaciones en un panel.
* [Exportación de la telemetría](app-insights-export-telemetry.md), para su análisis posterior en una base de datos, Power BI u otro panel.

*¿Cómo se obtienen datos de telemetría de la propia aplicación?*

* Instale el SDK de Application Insights en la aplicación. Aprenda cómo para [aplicaciones web Java](app-insights-java-get-started.md) y [aplicaciones web de Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1125_2015-->