<properties title="How to scale a website" pageTitle="How to scale a website" description="Learn how to scale your hosting plan in Azure." authors="stepsic" />

Escalación de un sitio web
==========================

En la vista previa del Portal de Azure, puede establecer manualmente el número de instancias de su plan de hospedaje web, o bien, puede establecer parámetros para que se realice la escalación de manera automática. Antes de configurar la escalación para su plan de hospedaje web, debería considerar si la escalación se ve afectada por el tamaño de la instancia. Los tamaños más grandes tienen más núcleos y memoria y, por tanto, tendrán un mejor rendimiento para el mismo número de instancias.

La escalación afecta al plan de hospedaje web completo. Al crear un sitio web, tendrá la opción de crear un nuevo plan de hospedaje web o un plan de hospedaje web existente. Cuando tenga un plan de hospedaje web, todos los sitios compartirán las mismas instancias para que se escalen juntos.

Escalado de un plan de hospedaje web
------------------------------------

1.  En [la vista previa del portal de Azure](https://portal.azure.com/), haga clic en **Examinar** y, a continuación, en **Sitios web** y haga clic en el nombre del sitio web para abrir el cuadro.

2.  La sección **Escala** de la lente **Operaciones** del cuadro Sitio web le indicará el estado del plan de hospedaje web: **Off** para cuando se está escalando manualmente, **Performance** para cuando está escalando mediante una o varias métricas de rendimiento y **Schedule** para cuando están habilitados varios perfiles de escalado automático.

    ![Scale part](./media/insights-how-to-scale/Insights_ScalePartOff.png)

3.  Si hace clic en la parte, pasará al cuadro **Escala**. En la parte superior del cuadro de escala, puede ver un historial de acciones de escalado automático para su plan de hospedaje web.

    ![Scale blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

4.  Puede ajustar manualmente el número de máquinas virtuales que ejecutan su plan de hospedaje web con el control deslizante **Instance**.

5.  Si desea que el número de instancias se ajuste automáticamente en función de la carga, seleccione **Performance** en **modo de escalado automático**. En este momento no puede seleccionar **Schedule** en la vista previa del Portal de Azure.

    ![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

6.  Una vez que selecciona Performance, hay dos cambios:
    -   **Rango de instancias** ahora le permite elegir un número de instancias máximo y mínimo. El escalado automático le mantendrá siempre en este rango, con independencia de la carga.
    -   Puede definir las métricas de rendimiento en la sección **Target Metrics** .

7.  La sección **Porcentaje de CPU** le permite establecer un objetivo para la CPU promedio en todas las instancias de su plan de hospedaje web. Tendrá lugar un escalado vertical cuando el CPU promedio exceda el valor máximo definido.

Con el escalado automático habilitado, verá **Performance** en la parte del cuadro del sitio web y verá el historial de escalado en el gráfico:

![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScalePartBladeOn.png)

Tenga en cuenta que en la vista previa del Portal de Azure, no puede cambiar el número de instancias de un plan de hospedaje web compartido.

Escalado avanzado
-----------------

Una opción nueva de la vista previa del Portal de Azure es que puede escalar en función de otras métricas que no sea el Porcentaje de CPU e incluso puede tener un conjunto complejo de reglas de escalado horizontal y reducción vertical.

### Escalado en función de otras métricas de rendimiento

Además de la CPU, puede escalar también basándose en:

-   La memoria promedio: si el porcentaje promedio de memoria utilizada en las instancias supera o no alcanza unos umbrales especificados, las instancias se agregarán o se quitarán.
-   La profundidad de la cola HTTP o la profundidad de la cola de disco: si el número de mensajes en la cola de solicitudes HTTP o de disco supera o no alcanza un umbral especificado, las instancias se agregarán o se quitarán.

Hay dos forma distintas de escalar mediante otra métrica. Si desea escalar únicamente mediante una métrica, seleccione la comilla angular junto al control deslizante **Porcentaje de CPU** . Se abrirá el cuadro Detalles de métrica:

![Entry point to scale metrics](./media/insights-how-to-scale/Insights_ScaleMetricChevron.png)

Para escalar por más de una métrica a la vez, haga clic en **Add Metric** en la barra de comandos:

![Add metrics](./media/insights-how-to-scale/Insights_AddMetric.png)

El cuadro Detalles de métrica contiene todos los controles que necesita para configurar el perfil óptimo de escalado. En la parte superior, elija la nueva métrica por la que desea escalar.

### Escalación con varios pasos

El gráfico de la métrica siguiente consta de dos secciones: **Scale up rules** y **Scale down rules**. El servicio se escalará si **cualquiera** de las reglas de escalado horizontal se cumple. Y a la inversa, el servicio se reducirá si **todas** las reglas de reducción vertical se cumplen.

Para cada regla que elija, existen las siguientes opciones: - 
- Condition: puede ser mayor o menor que
- Threshold: el número que esta métrica tiene que superar para desencadenar la acción
- Over Past: el número de minutos que esta métrica se promedia
- Scale up or down by: el tamaño de la acción de escalado
- Cool down: el tiempo que tiene que esperar esta regla tras una acción de escalado anterior para volver a escalarse.

![Multiple scale rules](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con varias reglas de escalado, puede ser más agresivo acerca del escalado horizontal (o reducción vertical) a medida que cambia el rendimiento. Por ejemplo, puede definir dos reglas de escalado:

1.  Escalar horizontalmente por 1 instancia si el porcentaje de CPU es superior al 60 %

2.  Escalar horizontalmente por 3 instancias si el porcentaje de CPU es superior al 85 %

Con esta regla adicional, si la carga excede del 85 % antes de una acción de escalado, obtendrá dos instancias adicionales en lugar de una.

