<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic" />

Personalización de la supervisión
=================================

En la vista previa del portal de Azure, ahora puede ver los datos de supervisión en más formas que antes, como la personalización del intervalo de tiempo y la elección de métricas adicionales.

1.  En la [vista previa del Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**, a continuación en **Sitios web** y, después, haga clic en el nombre de un sitio web para abrirlo.

2.  En el modo **Monitoring**, puede ver solicitudes, errores, [pruebas web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) y [análisis](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Si hace clic en el apartado **Requests and errors today**, se mostrará el cuadro **Metric**.

    ![Modo Monitoring](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3.  El cuadro **Metric** muestra los detalles de las métricas seleccionadas. En la parte superior del cuadro hay un gráfico y, debajo, una tabla que muestra una recopilación de dichas métricas, como el promedio, el valor máximo y el mínimo. Debajo de la tabla hay una lista de las alertas que ha definido, filtradas por las métricas que aparecen en el cuadro. De esta forma, si tiene muchas alertas, solo verá aquí las pertinentes. Todavía podrá ver todas las alertas para el sitio web haciendo clic en el apartado **Reglas de alerta** del cuadro **Sitio web**.

    ![Cuadro de métricas](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4.  Para personalizar las métricas que se muestran, haga clic con el botón secundario en el gráfico y seleccione **Edit Query**:

    ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)

5.  En el cuadro Edit Query, puede hacer dos cosas: cambiar el intervalo de tiempo y elegir otras métricas.

    ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6.  Cambiar el intervalo de tiempo es tan fácil como seleccionar un intervalo diferente (como **Past Hour**) y hacer clic en **Save** en la parte inferior de la sección. En la vista previa del Portal, ahora puede elegir **Custom**:

	![Intervalo de tiempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

1.  La opción Custom le permite elegir cualquier período de tiempo de las dos últimas semanas, por ejemplo, puede ver las dos semanas completas o solo 1 hora de ayer. Para especificar una hora diferente, escríbala en el cuadro de texto.

2.  Debajo del intervalo de tiempo, puede elegir el número de métricas que desea mostrar en el gráfico. Puede ver algunas métricas nuevas: **Memory working set** y **Average memory working set**.

3.  Cuando haga clic en Save, los cambios continuarán hasta que sale del cuadro del sitio web. Cuando regrese en otro momento, volverá a ver la métrica y el intervalo de tiempo originales.

Supervisión del plan de hospedaje web
-------------------------------------

Otra opción también nueva en la vista previa del Portal de Azure es la capacidad de supervisar métricas de rendimiento sobre las instancias en las que se ejecutan los sitios web. Para obtener acceso a estas métricas, haga clic en el icono Web Hostings Plan en el modo Resumen.

![Plan de hospedaje web](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Ahí puede ver un gráfico en el modo **Monitoring** que se comporta de igual forma que el gráfico del cuadro del sitio web, excepto en que puede ver las nuevas métricas: - Porcentaje de CPU, Porcentaje de memoria, HTTP Queue Depth o Disk Queue Depth.

Creación de gráficos paralelos
------------------------------

Con la poderosa personalización de usuario de la vista previa del Portal de Azure, puede crear gráficos paralelos para su personalización.

1.  En primer lugar, haga clic con el botón secundario en el gráfico que desea iniciar y seleccione **Customize**.

    ![Personalizar gráfico](./media/insights-how-to-customize-monitoring/Insights_Customize.png)

2.  A continuación, haga clic en **Clone** en el menú **...** para copiar la parte.

    ![Clonar parte](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)

3.  Finalmente, haga clic en **Done** en la barra de herramientas, en la parte superior de la pantalla. Ahora puede tratar esta parte como cualquier parte métrica normal. Si hace clic con el botón secundario y cambia la métrica que se muestra, podrá ver dos métricas diferentes en paralelo al mismo tiempo:

    ![Dos métricas en paralelo](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Tenga en cuenta que el intervalo de tiempo del gráfico y las métricas elegidas se restablecerán cuando salga del portal.

