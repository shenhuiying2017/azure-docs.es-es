<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Personalización de la supervisión

En la vista previa del portal de Azure, ahora puede ver los datos de supervisión en más formas que antes, como la personalización del intervalo de tiempo y la elección de métricas adicionales.

1.  En la [vista previa del Portal de Azure][vista previa del Portal de Azure], haga clic en **Examinar** y luego en un recurso que desee supervisar.
2.  El modo **Supervisión** contiene las métricas más importantes para cada recurso de Azure. Por ejemplo, Sitios web incluye Solicitudes, Errores, [Pruebas web][Pruebas web] y [Análisis][Análisis]. Si hace clic en el elemento **Solicitudes y errores de hoy**, se mostrará la hoja **Métrica**.
    ![Modo Supervisión][Modo Supervisión]
3.  El cuadro **Metric** muestra los detalles de las métricas seleccionadas. En la parte superior del cuadro hay un gráfico y, debajo, una tabla que muestra una recopilación de dichas métricas, como el promedio, el valor máximo y el mínimo. Debajo de la tabla hay una lista de las alertas que ha definido, filtradas por las métricas que aparecen en el cuadro. De esta forma, si tiene muchas alertas, solo verá aquí las pertinentes. Aún podrá ver todas las alertas para el sitio web al hacer clic en el elemento **Reglas de alerta** de la hoja **Sitio web**.
    ![Hoja Métrica][Hoja Métrica]
4.  Para personalizar las métricas que se muestran, haga clic con el botón secundario en el gráfico y seleccione **Editar consulta**:
    ![Editar consulta][Editar consulta]
5.  En el cuadro Edit Query, puede hacer dos cosas: cambiar el intervalo de tiempo y elegir otras métricas.
    ![Editar consulta][1]
6.  Cambiar el intervalo de tiempo es tan fácil como seleccionar un intervalo diferente (como **Past Hour**) y hacer clic en **Save** en la parte inferior de la sección. En la vista previa del Portal, ahora puede elegir **Personalizado**:
    ![Intervalo de tiempo personalizado][Intervalo de tiempo personalizado]:
7.  La opción Custom le permite elegir cualquier período de tiempo de las dos últimas semanas, por ejemplo, puede ver las dos semanas completas o solo 1 hora de ayer. Para especificar una hora diferente, escríbala en el cuadro de texto.
8.  Debajo del intervalo de tiempo, puede elegir el número de métricas que desea mostrar en el gráfico. Puede ver algunas métricas nuevas: **Memory working set** y **Average memory working set**.

9.  Cuando haga clic en Save, los cambios continuarán hasta que sale del cuadro del sitio web. Cuando regrese en otro momento, volverá a ver la métrica y el intervalo de tiempo originales.

## Supervisión de nuevos recursos

Una opción nueva en la vista previa del Portal de Azure es la capacidad de supervisar métricas de rendimiento para una variedad de recursos nuevos, entre los que se incluyen los siguientes:

-   Planes de hospedaje web
-   Caché de Redis
-   Cuenta de documentos de base de datos

Los planes de hospedaje web son algo más complicados que otros recursos, ya que representan el rendimiento de las instancias en las que se ejecutan sus **sitios web**. Para acceder a las métricas de plan de hospedaje web, haga clic en el icono Plan de hospedaje web en el modo Resumen de su sitio web.

![Plan de hospedaje web][Plan de hospedaje web]

Ahí puede ver un gráfico en el modo **Monitoring** que se comporta de igual forma que el gráfico del cuadro del sitio web, excepto en que puede ver las nuevas métricas:

-   Porcentaje de CPU
-   Porcentaje de memoria
-   Profundidad de la cola HTTP
-   Profundidad de la cola de disco

## Creación de gráficos paralelos

Con la poderosa personalización de usuario de la vista previa del Portal de Azure, puede crear gráficos paralelos para su personalización.

1.  En primer lugar, haga clic con el botón secundario en el gráfico que desea iniciar y seleccione **Personalizar**
    ![Personalizar gráfico][Personalizar gráfico]
2.  A continuación, haga clic en **Clonar** en el menú **...** para copiar el elemento
    ![Clonar elemento][Clonar elemento]
3.  Finalmente, haga clic en **Done** en la barra de herramientas, en la parte superior de la pantalla. Ahora puede tratar esta parte como cualquier parte métrica normal. Si hace clic con el botón secundario y cambia la métrica que se muestra, podrá ver dos métricas diferentes en paralelo al mismo tiempo:
    ![Dos métricas en paralelo][Dos métricas en paralelo]

Tenga en cuenta que el intervalo de tiempo del gráfico y las métricas elegidas se restablecerán cuando salga del portal.

  [vista previa del Portal de Azure]: https://portal.azure.com/
  [Pruebas web]: http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409
  [Análisis]: http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409
  [Modo Supervisión]: ./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png
  [Hoja Métrica]: ./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png
  [Editar consulta]: ./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png
  [1]: ./media/insights-how-to-customize-monitoring/Insights_EditQuery.png
  [Intervalo de tiempo personalizado]: ./media/insights-how-to-customize-monitoring/Insights_CustomTime.png
  [Plan de hospedaje web]: ./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png
  [Personalizar gráfico]: ./media/insights-how-to-customize-monitoring/Insights_Customize.png
  [Clonar elemento]: ./media/insights-how-to-customize-monitoring/Insights_ClonePart.png
  [Dos métricas en paralelo]: ./media/insights-how-to-customize-monitoring/Insights_SideBySide.png
