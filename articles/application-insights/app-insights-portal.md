<properties
	pageTitle="Uso del portal de Application Insights"
	description="Información general del análisis de uso con Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Uso del portal de Application Insights

Una vez [configurado Application Insights en su proyecto](app-insights-overview.md), aparecerán los datos de telemetría acerca del rendimiento y el uso de la aplicación en el recurso del proyecto Application Insights en el [portal de Azure](https://portal.azure.com).

## Búsqueda de la telemetría en Azure

En el [portal de Azure](https://portal.azure.com), busque el recurso de Application Insights que ha creado para la aplicación.

![Haga clic en Examinar, seleccione Application Insights y, después, seleccione su aplicación.](./media/app-insights-portal/00-start.png)

La página de información general proporciona la telemetría básica, además de vínculos a más elementos. El contenido depende del tipo de aplicación y se puede personalizar.

## Hoja de información general de la aplicación

La hoja (página) de información general de la aplicación muestra los gráficos más importantes para la supervisión del rendimiento y el uso. El contenido depende del tipo de aplicación y se puede personalizar en cualquier momento.


### Personalizar hoja de información general 

Elija lo que desea ver en la información general. En Personalizar, puede insertar títulos de sección, arrastrar iconos y gráficos, quitar elementos y agregar nuevos iconos y gráficos de la galería.

![Haga clic en "...", Personalizar. Arrastre los iconos y gráficos. Agregue iconos de la galería.](./media/app-insights-portal/020-customize.png)


## Creación de un gráfico y cuadrículas de métricas

### Edición de gráficos y cuadrículas

Para agregar un nuevo gráfico a la hoja:

![En el Explorador de métricas, elija Agregar gráfico](./media/app-insights-metrics-explorer/04-add.png)

Seleccione un gráfico nuevo o uno existente para editar lo siguiente:

![Seleccionar una o más métricas](./media/app-insights-metrics-explorer/08-select.png)

Puede mostrar más de una métrica en un gráfico, aunque hay restricciones sobre las combinaciones que se pueden mostrar juntas. Cuando elija una métrica, algunas otras se deshabilitan.

Si codifica las [métricas personalizadas](app-insights-api-custom-events-metrics.md#track-metric) en la aplicación (llamadas a TrackMetric y TrackEvent), aparecerán aquí.

### Segmentación de los datos

Seleccione un diagrama o cuadrícula, cambie a agrupación y elija una propiedad para agruparla por:

![Seleccionar Agrupación en, seleccione una propiedad en Agrupar por](./media/app-insights-metrics-explorer/15-segment.png)

Si codifica las métricas personalizadas en la aplicación y estas incluyen los [valores de propiedad](app-insights-api-custom-events-metrics.md#properties), podrá seleccionar la propiedad en la lista.

¿Es demasiado pequeño el gráfico para los datos segmentados? Ajuste el alto:

![Ajuste de la barra deslizante](./media/app-insights-metrics-explorer/18-height.png)

### Filtrado de los datos

Para ver solo las métricas para un conjunto de valores de propiedad seleccionado:

![Hacer clic en Filtro, expanda una propiedad y comprobar algunos valores](./media/app-insights-metrics-explorer/19-filter.png)

Si no selecciona ningún valor para una propiedad determinada, es lo mismo que seleccionar todas ellas: no hay ningún filtro en esa propiedad.

Observe los recuentos de eventos junto a cada valor de propiedad. Al seleccionar valores de una propiedad, se ajustan los recuentos junto con otros valores de propiedades.

### Almacenamiento de la hoja de métricas

Cuando haya creado algunos gráficos, guárdelos como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirlo con otros miembros del equipo.

![Elegir un favorito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Para ver de nuevo la hoja, **vaya a la hoja de información general** y abra Favoritos:

![En la hoja de información general, elegir Favoritos](./media/app-insights-metrics-explorer/22-favorite-get.png)

Si eligió el intervalo de tiempo relativo al guardar, la hoja se actualizará con la métrica más reciente. Si eligió el intervalo de tiempo absoluto, mostrará los mismos datos cada vez.

### Restablecimiento de la hoja

Si edita una hoja, pero le gustaría volver al conjunto original guardado, haga clic en Restablecer.

![En los botones situados en la parte superior del Explorador de métricas](./media/app-insights-metrics-explorer/17-reset.png)

## Creación de una página de búsqueda

Abra la búsqueda de diagnóstico:

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Abra la hoja Filtro y elija los tipos de evento que desea ver. (Si, posteriormente, desea restaurar los filtros con los que abrió la hoja, haga clic en Restablecer).

![Elija Filtrar y seleccione los tipos de telemetría](./media/app-insights-diagnostic-search/02-filter-req.png)

### Filtro de los valores de propiedad

Puede filtrar eventos por los valores de sus propiedades. Las propiedades disponibles dependen de los tipos de evento que haya seleccionado.

Por ejemplo, seleccione solicitudes con un código de respuesta específico.

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/03-response500.png)

El hecho de no elegir ningún valor de una propiedad determinada tiene el mismo efecto que elegir todos los valores; se desactiva el filtrado en esa propiedad.


### Acotación de la búsqueda

Observe que los recuentos a la derecha de los valores de filtro muestran cuántas repeticiones hay en el conjunto filtrado actual.

En este ejemplo, es obvio que la solicitud `Reports/Employees` genera la mayor parte de los 500 errores:

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/04-failingReq.png)

Además ,si desea ver también qué otros eventos se estaban produciendo durante este tiempo, puede activar **Incluir eventos con propiedades no definidas**.

### Guardado de la búsqueda

Cuando haya establecido todos los filtros que desea, puede guardar la búsqueda como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirla con otros miembros del equipo.

![Haga clic en Favoritos, establezca el nombre y haga clic en Guardar](./media/app-insights-diagnostic-search/08-favorite-save.png)


Para ver de nuevo la búsqueda, **vaya a la hoja de información general** y abra Favoritos:

![Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Si ha guardado con el intervalo de tiempo relativo, la hoja abierta de nuevo tiene los datos más recientes. Si ha guardado con el intervalo de tiempo absoluto, verá los mismos datos cada vez.

<!---HONumber=Oct15_HO3-->