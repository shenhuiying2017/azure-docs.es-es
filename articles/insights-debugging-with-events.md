<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn" />

Supervisión de los eventos que afectan a sus sitios web de Azure o a sus grupos de recursos
===========================================================================================

1.  Inicie sesión en la [vista previa del portal de Azure](https://portal.azure.com/).

2.  Haga clic en el botón **Examinar** en el lado izquierdo de la barra de navegación (también denominada **barra de acceso rápido**).

	![Centro de exploración](./media/insights-debugging-with-events/Insights_Browse.png)

1.  A continuación, seleccione **Resource groups** o **Sitios web** (según el tipo de evento en el que esté interesado). A modo de ejemplo, las capturas de pantalla de este documento incluyen el evento de grupos de recursos.

2.  En el cuadro **Resource groups**, haga clic en el nombre del grupo de recursos. Esto le dirigirá al cuadro de grupos de recursos.

    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_SelectRG.png)

3.  El cuadro de grupos de recursos incluye un apartado denominado **Events in the past week**. Cada una de las barras de ese apartado representa el número de eventos que han tenido lugar en cada uno de los días de la semana pasada. Cada una de las barras tiene dos colores distintos: azul y rosa. El rosa representa los eventos **con errores** de ese día, mientras que el azul representa todos los demás eventos.

	![Grupos de recursos](./media/insights-debugging-with-events/Insights_RGBlade.png)

1.  Ahora, haga clic en el apartado **Events in the past week**. Verá un nuevo cuadro denominado cuadro **Events**, que incluye todos los eventos ocurridos la semana pasada que afectaron al grupo de recursos.

	![Grupos de recursos](./media/insights-debugging-with-events/Insights_AllEvents.png)

1.  Haga clic en uno de los eventos.

	![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

Se abrirá un nuevo cuadro que incluirá muchos datos acerca del evento. Para los eventos **con errores**, esta página suele incluir una sección **Substatus** y una sección **Properties** que contienen datos útiles de cara al proceso de depuración.

