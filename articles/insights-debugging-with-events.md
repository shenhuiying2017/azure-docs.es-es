<properties title="How to debug with events" pageTitle="Depuración con eventos" description="Learn how to see events in Azure." authors="hanikn" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn" />

# Supervisión de los eventos que afectan a sus recursos o grupos de recursos de Azure

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/).
2. Haga clic en el botón **Examinar** en la barra de navegación de la izquierda (también conocida como **barra de salto**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. A continuación, seleccione un recurso (según los eventos en los que esté interesado). A modo de ejemplo, las capturas de pantalla de este documento incluyen el evento de grupos de recursos.
4. En la hoja **Grupos de recursos**, haga clic en el nombre del grupo de recursos. Esto le dirigirá a la hoja del de grupos de recursos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. La hoja de grupo de recursos incluye un apartado llamado **Eventos de la semana pasada**. Cada una de las barras de ese apartado representa el número de eventos que han tenido lugar en cada uno de los días de la semana pasada. Cada una de las barras tiene dos colores distintos: azul y rosa. El rosa representa los eventos con **errores** de ese día, mientras que el azul representa todos los demás eventos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. Ahora, haga clic en el apartado **Eventos de la semana pasada**. Verá una nueva hoja denominada **Eventos**, que incluye todos los eventos ocurridos la semana pasada que afectaron al grupo de recursos.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Haga clic en uno de los eventos.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Se abrirá una nueva hoja que incluirá muchos datos acerca del evento. Para los eventos **con errores**, esta página suele incluir una sección **Subestado** y una sección **Propiedades** que contienen datos útiles de cara al proceso de depuración.

