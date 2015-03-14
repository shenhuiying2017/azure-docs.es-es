<properties 
	pageTitle="Escalación de un sitio web" 
	description="Aprenda a escalar su plan de hospedaje en Azure." 
	authors="stepsic-microsoft-com" 
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
	ms.author="stepsic"/>

# Escalación de un sitio web

En el Portal de vista previa de Azure, puede establecer manualmente el número de instancias de su plan de hospedaje web, o bien, puede establecer parámetros para que se realice la escalación de manera automática. Antes de configurar la escalación para su plan de hospedaje web, debería considerar si la escalación se ve afectada por el tamaño de la instancia. Los tamaños más grandes tienen más núcleos y memoria y, por tanto, tendrán un mejor rendimiento para el mismo número de instancias.

La escalación afecta al plan de hospedaje web completo. Al crear un sitio web, tendrá la opción de crear un nuevo plan de hospedaje web o un plan de hospedaje web existente. Cuando tenga un plan de hospedaje web, todos los sitios compartirán las mismas instancias para que se escalen juntos.

## Escalado de un plan de hospedaje web

1. En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar** luego en **Sitios web**y, a continuación, haga clic en el nombre del sitio web para abrir la hoja.
2. El apartado **Escala** del modo **Operaciones** de la hoja del sitio web le indicará el estado del plan de hospedaje web: **Desactivado** para cuando se está escalando manualmente, **Rendimiento** para cuando está escalando mediante una o varias métricas de rendimiento y **Programa** para cuando están habilitados varios perfiles de escalado automático.  
    ![Scale part](./media/insights-how-to-scale/Insights_ScalePartOff.png)
3. Si hace clic en el apartado, pasará a la hoja **Escala**. En la parte superior de la hoja de escala, puede ver un historial de acciones de escalado automático para su plan de hospedaje web.  

    ![Scale blade](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
4. Puede ajustar manualmente el número de máquinas virtuales que ejecutan su plan de hospedaje web con el control deslizante **Instancia**.
5. Si desea que el número de instancias se ajuste automáticamente en función de la carga, seleccione **Rendimiento** en **Modo de escalado automático**. En este momento, no puede seleccionar **Programa** en el Portal de vista previa de Azure.  
    ![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png) 
6. Una vez que selecciona Performance, hay dos cambios:
    - **Rango de instancias** ahora le permite elegir un número de instancias máximo y mínimo. El escalado automático le mantendrá siempre en este rango, con independencia de la carga.
    - Puede definir las métricas de rendimiento en la sección **Métricas de rendimiento**.
7. La sección **Porcentaje de la CPU** le permite establecer un objetivo para la CPU promedio en todas las instancias de su plan de hospedaje web. Tendrá lugar un escalado vertical cuando el CPU promedio exceda el valor máximo definido.

Con la opción de escala automática habilitada, verá el elemento **Rendimiento** en el apartado de la hoja del sitio web, así como el historial de escala en el gráfico:

![Scale blade with CPU Percentage](./media/insights-how-to-scale/Insights_ScalePartBladeOn.png) 

Tenga en cuenta que en la vista previa del Portal de Azure, no puede cambiar el número de instancias de un plan de hospedaje web compartido.

## Escalado avanzado

Una opción nueva de la vista previa del Portal de Azure es que puede escalar en función de otras métricas que no sea el Porcentaje de CPU e incluso puede tener un conjunto complejo de reglas de escalado horizontal y reducción vertical.

### Escalado en función de otras métricas de rendimiento
Además de la CPU, puede escalar también basándose en:

- La memoria promedio: si el porcentaje promedio de memoria utilizada en las instancias supera o no alcanza unos umbrales especificados, las instancias se agregarán o se quitarán.
- La profundidad de la cola HTTP o la profundidad de la cola de disco: si el número de mensajes en la cola de solicitudes HTTP o de disco supera o no alcanza un umbral especificado, las instancias se agregarán o se quitarán.

Hay dos forma distintas de escalar mediante otra métrica. Si desea escalar únicamente mediante una métrica, seleccione la comilla angular junto al control deslizante **Porcentaje de la CPU**. Se abrirá la hoja Detalles de métrica:

![Entry point to scale metrics](./media/insights-how-to-scale/Insights_ScaleMetricChevron.png)

Para escalar por más de una métrica a la vez, haga clic en **Agregar métricas** en la barra de comandos:

![Add metrics](./media/insights-how-to-scale/Insights_AddMetric.png)

La hoja Detalles de métrica contiene todos los controles que necesita para configurar el perfil óptimo de escalado. En la parte superior, elija la nueva métrica por la que desea escalar.

### Escalación con varios pasos

El gráfico de la métrica siguiente consta de dos secciones: **Reglas de incremento de escala** y **Reglas de reducción de escala**. El servicio incrementará de escala si se cumple **cualquiera** de las reglas de incremento de escala. Por el contrario, el servicio reducirá de escala si se cumplen **todas** las reglas de reduccción de escala.

Para cada regla que elija, existen las siguientes opciones:

- Condición: mayor que o menor que.
- Umbral: el número que debe superar esta métrica para desencadenar la acción.
- Sobrexcedido: el número de minutos sobre el que se excede el promedio de esta métrica.
- Aumento o reducción de escala: el tamaño de la acción de escala.
- Espera: el tiempo que tiene que esperar esta regla tras una acción de escalado anterior para volver a escalar.

![Multiple scale rules](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con varias reglas de escalado, puede ser más agresivo acerca del escalado horizontal (o reducción vertical) a medida que cambia el rendimiento. Por ejemplo, puede definir dos reglas de escalado:

1. Escalar horizontalmente por 1 instancia si el porcentaje de CPU es superior al 60 %
2. Escalar horizontalmente por 3 instancias si el porcentaje de CPU es superior al 85 %

Con esta regla adicional, si la carga excede del 85 % antes de una acción de escalado, obtendrá dos instancias adicionales en lugar de una. 

<!--HONumber=46--> 
