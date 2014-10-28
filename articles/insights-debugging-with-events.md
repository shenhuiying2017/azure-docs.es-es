<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Supervisión de los eventos que afectan a sus recursos o grupos de recursos de Azure

1.  Inicie sesión en la [vista previa del portal de Azure][vista previa del portal de Azure].
2.  Haga clic en el botón **Examinar** en el lado izquierdo de la barra de navegación (también denominada **barra de acceso rápido**).
    ![Centro de exploración][Centro de exploración]
3.  A continuación, seleccione cualquier recurso (según los eventos en los que esté interesado). A modo de ejemplo, las capturas de pantalla de este documento incluyen el evento de grupos de recursos.
4.  En el cuadro **Resource groups**, haga clic en el nombre del grupo de recursos. Esto le dirigirá a la hoja de grupos de recursos.
    ![Grupos de recursos][Grupos de recursos]
5.  El cuadro de grupos de recursos incluye un apartado denominado **Events in the past week**. Cada una de las barras de ese apartado representa el número de eventos que han tenido lugar en cada uno de los días de la semana pasada. Cada una de las barras tiene dos colores distintos: azul y rosa. El rosa representa los eventos **Con error** de ese día, mientras que el azul representa todos los demás eventos.
    ![Grupos de recursos][1]
6.  Ahora, haga clic en el apartado **Events in the past week**. Verá una nueva hoja denominada **Eventos**, que incluye todos los eventos ocurridos la semana pasada que afectaron al grupo de recursos.
    ![Grupos de recursos][2]
7.  Haga clic en uno de los eventos.
    ![Grupos de recursos][3]
    Se abrirá una nueva hoja que incluirá muchos datos acerca del evento. Para los eventos **con errores**, esta página suele incluir una sección **Substatus** y una sección **Properties** que contienen datos útiles de cara al proceso de depuración.

  [vista previa del portal de Azure]: https://portal.azure.com/
  [Centro de exploración]: ./media/insights-debugging-with-events/Insights_Browse.png
  [Grupos de recursos]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
