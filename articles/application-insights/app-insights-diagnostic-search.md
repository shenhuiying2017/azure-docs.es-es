---
title: "Uso de Búsqueda en Azure Application Insights | Microsoft Docs"
description: "Busque y filtre los datos de telemetría sin procesar que envía la aplicación web."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: ce2fd9ed1ce796762cc15622cb1c59a316c1909d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="using-search-in-application-insights"></a>Uso de Búsqueda en Application Insights
Búsqueda es una característica de [Application Insights](app-insights-overview.md) que se usa para buscar y explorar elementos de telemetría individuales, como vistas de páginas, excepciones o solicitudes web. Y puede ver los seguimientos de registros y eventos que haya codificado.

(Para consultas más complejas sobre los datos, use [Analytics](app-insights-analytics-tour.md)).

## <a name="where-do-you-see-search"></a>¿Dónde verá Búsqueda?
### <a name="in-the-azure-portal"></a>En el Portal de Azure
Puede abrir la búsqueda de diagnóstico explícitamente desde la hoja de información general de Application Insights de la aplicación:

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

También se abre al hacer clic a través de algunos gráficos y elementos de la cuadrícula. En este caso, los filtros se establecen previamente para centrarse en el tipo de elemento seleccionado. 

Por ejemplo, en la hoja de información general, hay un gráfico de barras de solicitudes clasificadas por tiempo de respuesta. Recorra mediante clic un intervalo de rendimiento para ver una lista de solicitudes individuales en ese intervalo de tiempo de respuesta:

![Recorrido mediante clic del rendimiento de la solicitud](./media/app-insights-diagnostic-search/07-open-from-filters.png)

El cuerpo principal de Búsqueda de diagnóstico es una lista de elementos de telemetría: solicitudes de servidor, vistas de página, eventos personalizados que haya codificado, etc. En la parte superior de la lista hay un gráfico de resumen que muestra los recuentos de eventos con el paso del tiempo.

Haga clic en Actualizar para obtener nuevos eventos.

### <a name="in-visual-studio"></a>En Visual Studio

En Visual Studio, también hay una ventana de Búsqueda de Application Insights. Esta ventana resulta muy útil para mostrar eventos de telemetría generados por la aplicación que está depurando. Pero también puede mostrar los eventos recopilados desde la aplicación publicada en Azure Portal.

Abra la ventana de búsqueda en Visual Studio:

![Búsqueda de Application Insights abierta en Visual Studio](./media/app-insights-diagnostic-search/32.png)

La ventana de búsqueda tiene características similares al portal web:

![Ventana de búsqueda de Application Insights en Visual Studio](./media/app-insights-diagnostic-search/34.png)

La pestaña Track Operation (Seguimiento de operaciones) está disponible cuando se abre una solicitud o una vista de página. Una "operación" es una secuencia de eventos que está asociada a una sola solicitud o vista de página. Por ejemplo, llamadas de dependencia, excepciones, registros de seguimiento y eventos personalizados pueden ser parte de una única operación. La pestaña Track Operation (Seguimiento de operaciones) muestra gráficamente el tiempo y la duración de estos eventos con respecto a la solicitud o vista de página. 

## <a name="inspect-individual-items"></a>Inspección de elementos individuales
Seleccione cualquier elemento de telemetría para ver los campos clave y los elementos relacionados. Si desea ver el conjunto completo de campos, haga clic en "...". 

![Haga clic en Nuevo elemento de trabajo, modifique los campos y, a continuación, haga clic en Aceptar.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Filtro de los tipos de evento
Abra la hoja Filtro y elija los tipos de evento que desea ver. (Si, posteriormente, desea restaurar los filtros con los que abrió la hoja, haga clic en Restablecer).

![Elija Filtrar y seleccione los tipos de telemetría](./media/app-insights-diagnostic-search/02-filter-req.png)

Los tipos de evento son:

* **Seguimiento** - [registros de diagnóstico](app-insights-asp-net-trace-logs.md), como llamadas a TrackTrace, log4Net, NLog y System.Diagnostic.Trace.
* **Solicitud** - solicitudes HTTP recibidas por la aplicación de servidor, como páginas, scripts, imágenes, archivos de estilo y datos. Estos eventos se utilizan para crear los gráficos de información general de solicitudes y respuestas.
* **Vista de página** - [es la telemetría enviada por el cliente web](app-insights-javascript.md) , que se usa para crear informes de vistas de página. 
* **Evento personalizado** - si ha insertado llamadas a TrackEvent() [para supervisar el uso](app-insights-api-custom-events-metrics.md) , puede buscarlas aquí.
* **Excepción** - [excepciones no detectadas en el servidor](app-insights-asp-net-exceptions.md) y las que se registran mediante TrackException().
* **Dependencia:** - [ llamadas desde su aplicación de servidor](app-insights-asp-net-dependencies.md) a otros servicios, como API de REST o bases de datos, y llamadas de AJAX desde su [código de cliente](app-insights-javascript.md).
* **Disponibilidad**: resultados de [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtro de los valores de propiedad
Puede filtrar eventos por los valores de sus propiedades. Las propiedades disponibles dependen de los tipos de evento que haya seleccionado. 

Por ejemplo, seleccione solicitudes con un código de respuesta específico. 

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/03-response500.png)

El hecho de no elegir ningún valor de una propiedad en particular tiene el mismo efecto que elegir todos los valores. Se desactiva el filtrado en esa propiedad.

### <a name="narrow-your-search"></a>Acotación de la búsqueda
Observe que los recuentos a la derecha de los valores de filtro muestran cuántas repeticiones hay en el conjunto filtrado actual. 

En este ejemplo, es obvio que la solicitud "Rpt/Empleados" genera la mayor parte de los 500 errores:

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Búsqueda de eventos con la misma propiedad
Encuentre todos los elementos con el mismo valor de propiedad:

![Haga clic con el botón secundario en una propiedad](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Búsqueda de los datos

> [!NOTE]
> Para escribir consultas más complejas, abra [**Analytics**](app-insights-analytics-tour.md) desde la parte superior de la hoja Buscar.
> 

Puede buscar términos en cualquiera de los valores de propiedad. Esto es especialmente útil si ha escrito [eventos personalizados](app-insights-api-custom-events-metrics.md) con valores de propiedad. 

Quizás desee establecer un intervalo de tiempo, dado que las búsquedas en un intervalo más corto son más rápidas. 

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

Busque palabras completas, no subcadenas. Use comillas con los caracteres especiales.

| string | is *not* found by | but these do find it |
| --- | --- | --- |
| HomeController.About |home<br/>controller<br/>out | homecontroller<br/>about<br/>"homecontroller.about"|
|Estados Unidos|Uni<br/>ted|united<br/>states<br/>united AND states<br/>"united states"

A continuación se muestran las expresiones de búsqueda que puede utilizar:

| Consulta de ejemplo | Efecto |
| --- | --- |
| `apple` |Busca todos los eventos del intervalo de tiempo cuyos campos incluyen la palabra "apple". |
| `apple AND banana` |Busca eventos que contienen ambos términos. Utilizar "AND" en mayúsculas, no "and". |
| `apple OR banana`<br/>`apple banana` |Buscar eventos que contengan cualquiera de los dos términos. Usar "OR" no "or".<br/>Forma abreviada. |
| `apple NOT banana` |Buscar eventos que contengan un término pero no el otro. |



## <a name="sampling"></a>muestreo
Si la aplicación genera muchos datos de telemetría (y está usando la versión 2.0.0-beta3, o una posterior, del SDK de ASP.NET), el módulo de muestreo adaptable reduce automáticamente el volumen que se envía al portal mediante el envío de solamente una fracción representativa de los eventos. Sin embargo, los eventos relacionados con la misma solicitud se seleccionan o deseleccionan como grupo, por lo que puede navegar entre ellos. 

[Más información sobre el muestreo](app-insights-sampling.md).



## <a name="create-work-item"></a>Creación de elemento de trabajo
Puede crear un error en GitHub o en Visual Studio Team Services con los detalles de cualquier elemento de telemetría. 

![Haga clic en Nuevo elemento de trabajo, modifique los campos y, a continuación, haga clic en Aceptar.](./media/app-insights-diagnostic-search/42.png)

La primera vez que lo haga, deberá configurar un vínculo a la cuenta y el proyecto de Team Services.

![Introduzca la dirección URL del servidor de Team Services y el nombre del proyecto y haga clic en Autorizar.](./media/app-insights-diagnostic-search/41.png)

(También puede configurar el vínculo en la hoja Elementos de trabajo).

## <a name="save-your-search"></a>Guardado de la búsqueda
Cuando haya establecido todos los filtros que desea, puede guardar la búsqueda como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirla con otros miembros del equipo.

![Haga clic en Favoritos, establezca el nombre y haga clic en Guardar](./media/app-insights-diagnostic-search/08-favorite-save.png)

Para ver de nuevo la búsqueda, **vaya a la hoja de información general** y abra Favoritos:

![Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Si ha guardado con el intervalo de tiempo relativo, la hoja abierta de nuevo tiene los datos más recientes. Si ha guardado con el intervalo de tiempo absoluto, verá los mismos datos cada vez. (Si "relativo" no está disponible cuando quiera guardar un favorito, haga clic en Intervalo de tiempo en el encabezado y establezca un intervalo de tiempo que no sea un personalizado).

## <a name="send-more-telemetry-to-application-insights"></a>Envío de más telemetría a Application Insights
Además de la telemetría inmediata enviada por el SDK de Application Insights, puede:

* Capturar seguimientos de registros de su plataforma de registro de favoritos en [.NET](app-insights-asp-net-trace-logs.md) o [Java](app-insights-java-trace-logs.md). Esto significa que puede buscar en los seguimientos de registros y correlacionarlos con vistas de página, excepciones y otros eventos. 
* [Escribir código](app-insights-api-custom-events-metrics.md) para enviar eventos personalizados, vistas de página y excepciones. 

[Aprender a enviar registros y telemetría personalizada a Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>Preguntas y respuestas
### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Consulte [Resumen de límites](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>¿Cómo puedo ver datos POST en mis solicitudes de servidor?
Aunque no registramos los datos POST automáticamente, puede usar [TrackTrace o llamadas de registro](app-insights-asp-net-trace-logs.md). Coloque los datos POST en el parámetro de mensaje. No puede filtrar por el mensaje de la misma forma que con las propiedades, pero el límite de tamaño es mayor.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Pasos siguientes
* [Escribir consultas complejas en Analytics](app-insights-analytics-tour.md)
* [Envío de registros y telemetría personalizada a Application Insights](app-insights-asp-net-trace-logs.md).
* [Configuración de pruebas de disponibilidad y de capacidad de respuesta](app-insights-monitor-web-app-availability.md)
* [Solución de problemas](app-insights-troubleshoot-faq.md)
