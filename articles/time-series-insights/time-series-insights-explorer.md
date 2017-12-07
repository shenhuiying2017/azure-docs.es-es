---
title: "Exploración de los datos con el explorador de Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describe cómo usar el explorador de Azure Time Series Insights en el explorador web para tener rápidamente una visión global de los macrodatos y validar el entorno de IoT."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/30/2017
ms.openlocfilehash: 04e5465a56b8da9f2a078e513d5176f134fd011d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="azure-time-series-insights-explorer"></a>Explorador de Azure Time Series Insights
En este artículo se analizan las distintas características y opciones disponibles dentro de la aplicación web del explorador de Time Series Insights. Puede usar el explorador de Time Series Insights en el explorador web para crear visualizaciones de los datos.
 
Azure Time Series Insights es un servicio de análisis, almacenamiento y administración totalmente administrado que facilita el análisis y la exploración de miles de millones de eventos de IoT de forma simultánea. Ofrece una visión global de los datos que permite validar rápidamente la solución de IoT y evitar el costoso tiempo de inactividad de los dispositivos críticos. Puede detectar las tendencias ocultas, descubrir anomalías y realizar análisis de causa principal casi en tiempo real. El explorador de Time Series Insights está actualmente en versión preliminar pública.

## <a name="prerequisites"></a>Requisitos previos

Antes de que pueda usar el explorador de Time Series Insights, debe:
- Crear un entorno de Time Series Insights
- Proporcionar acceso a su cuenta en el entorno
- Agregar un origen del evento para ingerir datos y almacenarlos

## <a name="explore-and-query-data"></a>Explorar y consultar los datos
Minutos después de conectar el origen del evento al entorno de Times Series Insights, puede explorar y consultar los datos de serie temporal.

1. Para comenzar, abra el [explorador de Time Series Insights](https://insights.timeseries.azure.com/) en el explorador web y seleccione un entorno en el lado izquierdo de la ventana. Todos los entornos a los que tiene acceso se muestran en orden alfabético.

2. Una vez que selecciona un entorno, use las configuraciones **FROM** y **TO** que aparecen en la parte superior, o bien haga clic y arrastre hasta el intervalo de tiempo deseado.  Haga clic en la lupa que se encuentra en la parte superior derecha o haga clic con el botón derecho en el intervalo de tiempo seleccionado y seleccione **Buscar**.  

3. También puede actualizar automáticamente la disponibilidad cada minuto si selecciona el botón **Auto On** (Activado automáticamente).

4. Observe que el icono de la nube de Azure lo dirige a su entorno en Azure Portal.

   ![Entorno de Time Series Insights](media/time-series-insights-explorer/explorer1.png)

5. A continuación, verá un gráfico que muestra un recuento de todos los eventos durante el intervalo de tiempo seleccionado.  A continuación tiene varios controles:

    **Terms Editor Panel** (Panel Editor de términos): el espacio para los términos es donde consulta al entorno.  Se encuentra en el lado izquierdo de la pantalla y habilita lo siguiente: 
      - **Measure** (Medida): este menú desplegable muestra todas las columnas numéricas (dobles)
      - **Split By** (Dividir por): este menú desplegable muestra las columnas de categoría (cadenas)
      - Puede habilitar la interpolación escalonada, mostrar el valor mínimo y el valor máximo, y ajustar el eje Y desde el panel de control que está junto a la medida.  Además, puede ajustar si los datos que se muestran son un recuento, un promedio o la suma de los datos.
      - Puede agregar hasta cinco términos para verlos en el mismo eje X.  Use el botón **copy-down** para agregar un término adicional o haga clic en el botón **Add** (Agregar) para agregar un término nuevo.
     
        ![Panel Editor de términos](media/time-series-insights-explorer/explorer2.png)

      - **Predicate** (Predicado): el predicado permite filtrar rápidamente los eventos con el conjunto de operados que aparecen a continuación. Si realiza una búsqueda mediante selección o clic, el predicado se actualizará automáticamente según esa búsqueda.      Los tipos de operando compatibles incluyen:

         |Operación  |Tipos admitidos  |Notas  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, TimeSpan       |         |
         |=, !=, <>     | String, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | String, Bool, Double, DateTime, TimeSpan, NULL        |  Todos los operandos deben ser del mismo tipo o deben ser una constante NULL.        |
         |HAS     | String        |  Solo los literales de cadenas constantes se permiten en el lado derecho. No se permiten cadenas vacías ni NULL.       |

      - **Ejemplos de consultas**
      
         ![Consultas de ejemplo](media/time-series-insights-explorer/explorer9.png)

6. La herramienta de control deslizante **Interval Size** (Tamaño del intervalo) permite acercar y alejar los intervalos del mismo intervalo de tiempo.  Esto proporciona un control más preciso del movimiento entre intervalos de tiempo de gran tamaño que muestran tendencias uniformes hasta en intervalos tan pequeños como un milisegundo, lo que permite ver cortes de los datos granulares y de alta resolución. El punto inicial predeterminado del control deslizante está establecido como la vista más óptima de los datos de la selección, lo que permite equilibrar la resolución, la velocidad de la consulta y la granularidad.

7. La herramienta **Time brush** (Pincel para tiempo) facilita la navegación de un intervalo de tiempo a otro, lo que pone la experiencia del usuario intuitiva en el centro para lograr un movimiento sin problemas entre los intervalos de tiempo.

8. El comando **Save** (Guardar) le permite guardar la consulta actual y habilitarla para su uso compartido con otros usuarios del entorno. Con **Open** (Abrir), puede ver todas las consultas guardadas y cualquier consulta compartida de otros usuarios en los entornos a los que tiene acceso. 

   ![Consultas](media/time-series-insights-explorer/explorer3.png)

9. La herramienta **Perspective View** (Vista de perspectiva) proporciona una vista simultánea de hasta cuatro consultas únicas. El botón de vista de perspectiva está en la esquina superior derecha del gráfico.  

   ![Vista de perspectiva](media/time-series-insights-explorer/explorer4.png)

10. **Chart** (Gráfico) le permite explorar visualmente los datos. Las herramientas del gráfico son:

   - Selección o clic, que permite la selección de un intervalo de tiempo específico o de una serie de datos única.  
   - Dentro de la selección de un intervalo de tiempo, puede acercar o explorar los eventos.  
   - Dentro de una serie de datos, puede dividir la serie por otra columna, agregar la serie como término nuevo, mostrar solo la serie seleccionada, excluir la serie seleccionada, anclar esa serie o explorar los eventos de la serie seleccionada.
   - En el área de filtro que se encuentra a la izquierda del gráfico, puede ver todas las series de datos que aparecen y reordenar por nombre o valor, ver todas las series de datos o específicamente las series ancladas o desancladas.  También puede seleccionar una serie de datos única y dividirla por otra columna, agregar la serie como término nuevo, mostrar solo la serie seleccionada, excluir la serie seleccionada, anclar esa serie o explorar los eventos de la serie seleccionada.
   - Cuando se ve varios términos de manera simultánea, puede apilarlos, desapilarlos, ver datos adicionales sobre una serie de datos y usar el mismo eje Y en todos los términos con los botones que aparecen en la esquina superior derecha del gráfico.
 
   ![Herramienta de gráfico](media/time-series-insights-explorer/explorer5.png) 

11. El **mapa térmico** se puede usar para detectar rápidamente una serie de datos única o anómala en una consulta determinada. Solo un término de búsqueda se puede visualizar como un mapa térmico.    

   ![Mapa térmico](media/time-series-insights-explorer/explorer6.png)

12. **Events** (Eventos): cuando elige explorar eventos cuando selecciona o hace clic con el botón derecho arriba, aparece disponible el panel de eventos.  En el panel puede ver todos los eventos sin procesar y exportar los eventos como archivos JSON o CSV. Tenga en cuenta que Time Series Insights almacena todos los datos sin procesar.

   ![Eventos](media/time-series-insights-explorer/explorer7.png)

13. Haga clic en la pestaña **STATS** después de explorar los eventos para exponer los patrones y las estadísticas de columna.  

   - **Patterns** (Patrones): esta característica muestra de manera proactiva los patrones con mayor importancia estadística de una región de datos seleccionada. Esto evita tener que buscar en miles de eventos para comprender cuáles son los patrones que garantizan más tiempo y energía. Además, Time Series Insights permite ir directamente a estos patrones con importancia estadística para seguir realizando el análisis. Esta característica también resulta útil para las investigaciones de análisis final de datos históricos. 

   - **Column stats** (Estadísticas de columna): las estadísticas de columna proporcionan gráficos y tablas que desglosan los datos de cada columna de la serie de datos seleccionada durante el intervalo de tiempo seleccionado.  
 
      ![STATS](media/time-series-insights-explorer/explorer8.png) 

Ya ha visto las distintas características y opciones disponibles dentro de la aplicación web del explorador de Time Series Insights. 

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
>[Diagnóstico y solución de problemas de su entorno Time Series Insights](time-series-insights-diagnose-and-solve-problems.md)
