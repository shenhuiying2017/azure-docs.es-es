<properties 
	pageTitle="Personalización de la supervisión" 
	description="Obtenga información acerca de cómo personalizar los gráficos de supervisión en Azure." 
	authors="alancameronwills" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="awills"/>

# Personalización de la supervisión

Su aplicación de Azure presenta diversas variedades de métricas que puede supervisar, y puede representarlas durante un período de tiempo que elija.

1. En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar** y luego en el recurso que le interese supervisar.
2. El modo **Supervisión** contiene las métricas más importantes para cada recurso de Azure. Por ejemplo, Sitios web incluye Solicitudes, Errores, [Pruebas web](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) y [Análisis](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Si hace clic en el apartado **Solicitudes y errores hoy** se mostrará la hoja **Métrica**.  
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. La hoja **Métrica** le muestra detalles acerca de las métricas que seleccione. En la parte superior del cuadro hay un gráfico y, debajo, una tabla que muestra una recopilación de dichas métricas, como el promedio, el valor máximo y el mínimo. Debajo de la tabla hay una lista de las alertas que ha definido, filtradas por las métricas que aparecen en el cuadro. De esta forma, si tiene muchas alertas, solo verá aquí las pertinentes. Todavía podrá ver todas las alertas para el sitio web haciendo clic en el apartado **Reglas de alerta** en la hoja **Sitio web**.  
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Para personalizar las métricas que se muestran, haga clic con el botón secundario en el gráfico y seleccione **Editar consulta**:  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)
5. En el cuadro Editar consulta, puede hacer dos cosas: cambiar el intervalo de tiempo y elegir otras métricas.  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Cambiar el intervalo de tiempo es tan fácil como seleccionar un intervalo diferente (como **Hora pasada**) y hacer clic en **Guardar** en la parte inferior de la hoja. En el portal de vista previa, ahora puede elegir **Personalizar**:  
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. La opción Personalizar le permite elegir cualquier período de tiempo de las dos últimas semanas, por ejemplo, puede ver las dos semanas completas o solo 1 hora de ayer. Para especificar una hora diferente, escríbala en el cuadro de texto.
8. Debajo del intervalo de tiempo, puede elegir el número de métricas que desea mostrar en el gráfico. Puede ver algunas métricas nuevas: **Memoria: conjunto de trabajo** y **Memoria media: conjunto de trabajo**.

9. Cuando haga clic en Guardar, los cambios continuarán hasta que sale de la hoja del sitio web. Cuando regrese en otro momento, volverá a ver la métrica y el intervalo de tiempo originales.

## Supervisión de nuevos recursos

Una opción nueva en la vista previa del Portal de Azure es la capacidad de supervisar métricas de rendimiento para una variedad de recursos nuevos, entre los que se incluyen los siguientes:
- Planes de hospedaje web
- Caché de Redis
- Cuenta de documentos de base de datos

Los planes de hospedaje web son algo más complicados que otros recursos, ya que representan el rendimiento de las instancias en las que se ejecutan sus **sitios web**. Para acceder a las métricas de plan de hospedaje web, haga clic en el icono Plan de hospedaje web en el modo Resumen de su sitio web.

![Web hosting plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Ahí puede ver un gráfico en el modo **Supervisión** que se comporta de igual forma que el gráfico de la hoja del sitio web, excepto en que puede ver las nuevas métricas:

- Porcentaje de CPU
- Porcentaje de memoria
- Profundidad de la cola HTTP
- Profundidad de la cola de disco

## Creación de gráficos paralelos

Con la poderosa personalización de usuario de la vista previa del Portal de Azure, puede crear gráficos paralelos para su personalización.

1. En primer lugar, haga clic con el botón secundario en el gráfico que desea iniciar y seleccione **Personalizar**  
    ![Customize chart](./media/insights-how-to-customize-monitoring/Insights_Customize.png)
2. A continuación, haga clic en **Clonar** en el **...** menú para copiar el apartado  
    ![Clone part](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)
3. Finalmente, haga clic en **Listo** en la barra de herramientas de la parte superior de la pantalla. Ahora puede tratar esta parte como cualquier parte métrica normal. Si hace clic con el botón secundario y cambia la métrica que se muestra, podrá ver dos métricas diferentes en paralelo al mismo tiempo:  
    ![Two metrics Side by Side](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Tenga en cuenta que el intervalo de tiempo del gráfico y las métricas elegidas se restablecerán cuando salga del portal.


<!--HONumber=46--> 

<!--HONumber=46--> 
