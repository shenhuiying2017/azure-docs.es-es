---
title: "Exploración de métricas en Azure Application Insights | Microsoft Docs"
description: "Interpretación de los gráficos en el Explorador de métricas y personalización de las hojas del Explorador de métricas."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 01b45323b74b54da157f4e9f1af783759c121be1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-metrics-in-application-insights"></a>Exploración de métricas en Application Insights
Las métricas de [Application Insights][start] son valores medidos y recuentos de eventos que se envían en telemetría desde la aplicación. Ayudar a detectar problemas de rendimiento y a observar las tendencias sobre cómo se utiliza la aplicación. Hay una amplia gama de métricas estándar y también se pueden crear los propios eventos y métricas personalizados.

Los recuentos de métricas y eventos se muestran en los gráficos de valores agregados, como sumas, promedios o recuentos.

A continuación encontrará un conjunto de ejemplo de gráficos:

![](./media/app-insights-metrics-explorer/01-overview.png)

Encontrará gráficos de métricas en todas partes en el portal de Application Insights. En la mayoría de los casos, pueden personalizarse y puede agregar más gráficos a la hoja. En la hoja Información general, haga clic para ver gráficos más detallados (que presentan títulos como "Servidores") o haga clic en **Explorador de métricas** para abrir una nueva hoja donde podrá crear gráficos personalizados.

## <a name="time-range"></a>Intervalo de tiempo
En todas las hojas puede cambiar el intervalo de tiempo cubierto por los gráficos o cuadrículas.

![Abrir la hoja de información general de la aplicación en el portal de Azure](./media/app-insights-metrics-explorer/03-range.png)

Si espera algunos datos que no ha aparecido todavía, haga clic en Actualizar. Los gráficos se actualizan a intervalos, pero los intervalos son más largos para intervalos de tiempo mayores. Puede tardar un tiempo en llegar a través de la canalización de análisis a un gráfico de datos.

Para acercar una parte de un gráfico, arrastre sobre ella:

![Arrastre por parte de un gráfico.](./media/app-insights-metrics-explorer/12-drag.png)

Haga clic en el botón Deshacer zoom para restaurarlo.

## <a name="granularity-and-point-values"></a>Valores de granularidad y punto
Mantenga el cursor sobre el gráfico para mostrar los valores de las métricas en ese momento.

![Mantener el mouse sobre un gráfico](./media/app-insights-metrics-explorer/02-focus.png)

Se agrega el valor de la métrica en un momento determinado en el intervalo de muestreo anterior.

El intervalo de muestreo o "granularidad" se muestra en la parte superior de la hoja.

![El encabezado de una hoja.](./media/app-insights-metrics-explorer/11-grain.png)

Puede ajustar la granularidad en la hoja Intervalo de tiempo:

![El encabezado de una hoja.](./media/app-insights-metrics-explorer/grain.png)

La granularidad disponible depende del intervalo de tiempo que seleccione. Las granularidades explícitas son alternativas a la granularidad "automática" para el intervalo de tiempo.


## <a name="editing-charts-and-grids"></a>Edición de gráficos y cuadrículas
Para agregar un nuevo gráfico a la hoja:

![En el Explorador de métricas, elija Agregar gráfico](./media/app-insights-metrics-explorer/04-add.png)

Seleccione **Editar** en un gráfico nuevo o uno existente para editar lo siguiente:

![Seleccionar una o más métricas](./media/app-insights-metrics-explorer/08-select.png)

Puede mostrar más de una métrica en un gráfico, aunque hay restricciones sobre las combinaciones que se pueden mostrar juntas. Cuando elija una métrica, algunas otras se deshabilitan.

Si codifica las [métricas personalizadas][track] en la aplicación (llamadas a TrackMetric y TrackEvent), aparecerán aquí.

## <a name="segment-your-data"></a>Segmentación de los datos
Puede dividir una métrica mediante la propiedad, por ejemplo, para comparar vistas de páginas en clientes con diferentes sistemas operativos.

Seleccione un diagrama o cuadrícula, cambie a agrupación y elija una propiedad para agruparla por:

![Seleccionar Agrupación en, seleccione una propiedad en Agrupar por](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Cuando usa la agrupación, los tipos de gráfico de barras y de áreas proporcionan una visualización apilada. Esto es conveniente cuando el método de agregación es la suma. Pero cuando el tipo de agregación es el promedio, elija los tipos de visualización de líneas o de cuadrícula.
>
>

Si ha codificado [métricas personalizadas][track] en la aplicación y estas incluyen valores de propiedad, podrá seleccionar la propiedad en la lista.

¿Es demasiado pequeño el gráfico para los datos segmentados? Ajuste el alto:

![Ajuste de la barra deslizante](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Tipos de agregación
La leyenda del lateral muestra normalmente de forma predeterminada el valor agregado durante el período del gráfico. Si mantiene el mouse sobre el gráfico, muestra el valor en ese momento.

Cada punto de datos en el gráfico es un agregado de los valores de datos recibidos en el intervalo de muestreo anterior o "granularidad". La granularidad se muestra en la parte superior de la hoja y varía en función de la escala de tiempo total del gráfico.

Se agregan métricas diferentes de distintas maneras:

* **Recuento**: recuento de los eventos recibidos en el intervalo de muestreo. Se usa para eventos como las solicitudes. Las variaciones en la altura del gráfico indican variaciones en el ritmo al que se producen los eventos. Pero tenga en cuenta que el valor numérico cambia al modificar el intervalo de muestreo.
* **Suma** : suma los valores de todos los puntos de datos recibidos durante el intervalo de muestreo o el período del gráfico.
* **Media** divide la suma por el número de puntos de datos recibidos durante el intervalo.
* **únicos** se usan para los recuentos de usuarios y cuentas. Durante el intervalo de muestreo, o durante el período del gráfico, la ilustración muestra el recuento de usuarios diferentes que se ven en ese momento.
* **%**: solo se usan versiones porcentuales de cada agregación con gráficos segmentados. La suma total es siempre el 100 % y el gráfico muestra la contribución relativa de diferentes componentes de un total.

    ![Agregación porcentual](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Cambio del tipo de agregación

![Edite el gráfico y, después, seleccione Agregación.](./media/app-insights-metrics-explorer/05-aggregation.png)

El método predeterminado para cada métrica se muestra cuando se crea un nuevo gráfico o cuando se anula la selección de todas las métricas:

![Cancelar la selección de todas las métricas para ver los valores predeterminados](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Anclado del eje Y 
De forma predeterminada, un gráfico muestra los valores del eje Y empezando desde cero hasta el valor máximo del intervalo de datos para proporcionar una representación visual del cuanto de los valores. Pero, en algunos casos, más que el cuanto podría ser interesante inspeccionar visualmente los pequeños cambios de los valores. Para personalizaciones como esta, use la característica de edición del intervalo del eje Y para anclar los valores mínimo y máximo del eje Y en el lugar deseado.
Haga clic en la casilla de verificación "Configuración avanzada" para que aparezca la opción Intervalo del eje Y

![Haga clic en Configuración avanzada, seleccione Intervalo personalizado y especifique los valores máximo y mínimo](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrado de los datos
Para ver solo las métricas para un conjunto de valores de propiedad seleccionado:

![Hacer clic en Filtro, expanda una propiedad y comprobar algunos valores](./media/app-insights-metrics-explorer/19-filter.png)

Si no selecciona ningún valor para una propiedad determinada, es lo mismo que seleccionar todas ellas: no hay ningún filtro en esa propiedad.

Observe los recuentos de eventos junto a cada valor de propiedad. Al seleccionar valores de una propiedad, se ajustan los recuentos junto con otros valores de propiedades.

Los filtros se aplican a todos los gráficos de una hoja. Si desea que se apliquen filtros diferentes a gráficos a diferentes, cree y guarde hojas de métricas diferentes. Si lo desea, puede anclar gráficos de distintas hojas al panel; de este modo, podrá verlos en paralelo.

### <a name="remove-bot-and-web-test-traffic"></a>Supresión de bots y de tráfico de prueba web
Utilice el filtro **Tráfico real o sintético** y active **Real**.

También puede filtrar por **Origen del tráfico sintético**.

### <a name="to-add-properties-to-the-filter-list"></a>Para agregar propiedades a la lista de filtros
¿Quiere filtrar telemetría en una categoría de su propia elección? Por ejemplo, quizás divida los usuarios en distintas categorías y quiera segmentar los datos por estas categorías.

[Cree su propia propiedad](app-insights-api-custom-events-metrics.md#properties). Establézcala en un [inicializador de telemetría](app-insights-api-custom-events-metrics.md#defaults) para que aparezca en toda la telemetría, incluida la telemetría estándar enviada por distintos módulos SDK.

## <a name="edit-the-chart-type"></a>Edición del tipo de gráfico
Observe que puede cambiar entre cuadrículas y gráficos:

![Seleccionar una cuadrícula o gráfico y elegir un tipo de gráfico](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Almacenamiento de la hoja de métricas
Cuando haya creado algunos gráficos, guárdelos como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirlo con otros miembros del equipo.

![Elegir un favorito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Para ver de nuevo la hoja, **vaya a la hoja de información general** y abra Favoritos:

![En la hoja de información general, elegir Favoritos](./media/app-insights-metrics-explorer/22-favorite-get.png)

Si eligió el intervalo de tiempo relativo al guardar, la hoja se actualizará con la métrica más reciente. Si eligió el intervalo de tiempo absoluto, mostrará los mismos datos cada vez.

## <a name="reset-the-blade"></a>Restablecimiento de la hoja
Si edita una hoja, pero le gustaría volver al conjunto original guardado, haga clic en Restablecer.

![En los botones situados en la parte superior del Explorador de métricas](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Para obtener una vista mucho más inmediata de la telemetría, abra [Live Stream](app-insights-live-stream.md). La mayoría de las métricas tardan unos minutos en aparecer, como consecuencia del proceso de agregación. Por el contrario, las métricas activas están optimizadas para conseguir una latencia baja. 

## <a name="set-alerts"></a>Establecer alertas
Para recibir notificaciones por correo electrónico de los valores no habituales de cualquier métrica, agregue una alerta. Puede decidir si se debe enviar un mensaje de correo electrónico a los administradores de cuentas o a direcciones de correo electrónico específicas.

![En el Explorador de métricas, elija Reglas de alertas, Agregar alerta](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Obtenga más información sobre alertas][alerts].


## <a name="continuous-export"></a>Exportación continua
Si quiere que los datos se exporten continuamente para procesarlos externamente, puede usar la [Exportación continua](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Si desea obtener vistas todavía más sofisticadas de los datos, puede [exportarlos a Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Análisis
[Análisis](app-insights-analytics.md) es una forma más versátil de analizar los datos de telemetría mediante un eficaz lenguaje de consulta. Utilícelo si desea combinar o calcular resultados a partir de métricas, o realizar una exploración en profundidad del rendimiento reciente de su aplicación. 

En el gráfico de una métrica, puede hacer clic en el icono Análisis para ir directamente a la consulta de análisis equivalente.

## <a name="troubleshooting"></a>Solución de problemas
*No veo ningún dato en el gráfico.*

* Los filtros se aplican a todos los gráficos de la hoja. Asegúrese de que, al centrarse en un gráfico, no ha establecido un filtro que excluya todos los datos en otro.

    Si desea establecer filtros diferentes en gráficos diferentes, créelos en hojas diferentes y guárdelos como elementos favoritos independientes. Si lo desea, puede anclarlos al panel para verlos en paralelo.
* Si agrupa un gráfico por una propiedad que no está definida en la métrica, no habrá nada en el gráfico. Intente borrar "Agrupar por" o elija una propiedad de agrupación diferente.
* Los datos de rendimiento (CPU, velocidad de E/S, etc.) están disponibles para servicios web de Java, aplicaciones de escritorio de Windows, [servicios y aplicaciones web IIS si instala el Monitor de estado](app-insights-monitor-performance-live-website-now.md) y [Azure Cloud Services](app-insights-azure.md). No están disponible para los sitios web de Azure.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Pasos siguientes
* [Supervisión del uso con Application Insights](app-insights-web-track-usage.md)
* [Uso de la Búsqueda de diagnóstico](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
