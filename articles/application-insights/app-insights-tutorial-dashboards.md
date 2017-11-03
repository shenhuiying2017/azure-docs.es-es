---
title: "Creación de paneles personalizados en Azure Application Insights | Microsoft Docs"
description: Tutorial para crear paneles de indicadores clave de rendimiento (KPI) personalizados con Azure Application Insights.
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0d2f98ca2fb39289b2916ddd24590924856507d6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Creación de paneles de indicadores clave de rendimiento (KPI) personalizados con Azure Application Insights

Puede crear varios paneles en Azure Portal de manera que cada uno incluya iconos en los que se visualicen datos procedentes de varios recursos de Azure en diferentes suscripciones y grupos de recursos.  Puede anclar distintos gráficos y vistas de visión de Azure Application Insights para crear paneles personalizados que le proporcionen una descripción completa del estado y el rendimiento de la aplicación.  Este tutorial le guía a través de la creación de un panel personalizado que incluye varios tipos de datos y visualizaciones de Azure Application Insights.  Aprenderá a:

> [!div class="checklist"]
> * Crear un panel personalizado en Azure
> * Agregar un icono desde la Galería de iconos
> * Agregar métricas estándar de Application Insights al panel 
> * Agregar un gráfico de métricas personalizado de Application Insights al panel
> * Agregar los resultados de una consulta de Analytics al panel 



## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Implemente una aplicación de .NET en Azure y [habilite el SDK de Application Insights](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Creación de un nuevo panel
Un solo panel puede contener recursos de varias aplicaciones, grupos de recursos y suscripciones.  Comience el tutorial creando un nuevo panel para la aplicación.  

2.  En la pantalla principal del portal, seleccione **Nuevo panel**.

    ![Nuevo panel](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Escriba un nombre para el panel.
4. Eche un vistazo a la **Galería de iconos** para ver la variedad de iconos que se pueden agregar al panel.  Además de agregar iconos de la galería, puede anclar gráficos y otras vistas directamente desde Application Insights al panel.
5. Busque el icono **Markdown** y arrástrelo hasta el panel.  Este icono le permite agregar texto en formato Markdown, que es idóneo para agregar texto descriptivo al panel.
6. Agregue texto a las propiedades del icono y cambie su tamaño en el lienzo del panel.
    
    ![Edición del mosaico de Markdown](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Haga clic en **Personalización finalizada	** en la parte superior de la pantalla para salir del modo de personalización del icono y, a continuación, en **Publicar cambios** para guardar los cambios.

    ![Panel con icono de Markdown](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Adición de información general de estado
Un panel que tenga solo texto estático no resulta muy interesante; así pues, agregue ahora un icono de Application Insights para mostrar información acerca de la aplicación.  Puede agregar iconos de Application Insights desde la Galería de iconos, o puede anclarlos directamente desde las pantallas de Application Insights.  Esto le permite configurar gráficos y vistas con las que ya está familiarizado antes de anclarlos al panel.  Empiece por agregar la información general de estado estándar para la aplicación.  Esto no requiere configuración y permite una personalización mínima en el panel.


1. Seleccione **Application Insights** en el menú de Azure y, a continuación, seleccione la aplicación.
2. En **Escala de tiempo con información general**, seleccione el menú contextual y haga clic en **Anclar al panel**.  De este modo, se agrega el icono al último panel que estaba viendo.  

    ![Anclaje de Escala de tiempo con información general](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. En la parte superior de la pantalla, haga clic en **Ver panel** para volver al panel.
4. Ya se ha agregado al panel la Escala de tiempo con información general.  Haga clic y arrástrela hasta su posición y, a continuación, haga clic en **Personalización finalizada** y **Publicar cambios**.  Ahora, el panel tiene un icono con información útil.

    ![Panel con Escala de tiempo con información general](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Adición del gráfico de métricas personalizado
El panel **Métricas** panel le permite representar una métrica recopilada por Application Insights a lo largo del tiempo con filtros opcionales y agrupación.  Al igual que todo lo demás en Application Insights, puede agregar este gráfico al panel.  Para ello se requiere antes un poco de personalización.

1. Seleccione **Application Insights** en el menú de Azure y, a continuación, seleccione la aplicación.
1. Seleccione **Métricas**.  
2. Ya se ha creado un gráfico vacío, y se le pedirá que agregue una métrica.  Agregue una métrica al gráfico y, opcionalmente, agregue un filtro y una agrupación.  En el ejemplo siguiente se muestra el número de solicitudes de servidor agrupadas según se hayan completado correctamente o no.  De este modo se obtiene una vista de ejecución de las solicitudes correctas e incorrectas.

    ![Adición de métrica](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Seleccione el menú contextual para el gráfico y elija **Anclar al panel**.  De este modo, se agrega la vista al último panel con el que estaba trabajando.

    ![Anclaje de gráfico de métricas](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. En la parte superior de la pantalla, haga clic en **Ver panel** para volver al panel.

4. Ya se ha agregado al panel la Escala de tiempo con información general.  Haga clic y arrástrela hasta su posición y, a continuación, haga clic en **Personalización finalizada** y **Publicar cambios**. 

    ![Panel con métricas](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Explorador de métricas
El **Explorador de métricas** es similar a las Métricas, aunque permite una mayor personalización cuando se agrega al panel.  La elección de una u otra opción para representar las métricas depende de su preferencia y requisitos particulares.

1. Seleccione **Application Insights** en el menú de Azure y, a continuación, seleccione la aplicación.
1. Seleccione **Explorador de métricas**. 
2. Haga clic para editar el gráfico y seleccione una o varias métricas y, opcionalmente, una configuración detallada.  En el ejemplo se muestra un gráfico de líneas que hace un seguimiento del promedio de tiempo de respuesta de la página.
3. Haga clic en el icono de anclaje en la parte superior derecha para agregar el gráfico al panel y, a continuación, arrástrelo hasta su posición.

    ![Explorador de métricas](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. El icono Explorador de métricas permite personalizar más elementos una vez que se agrega al panel.  Haga clic con el botón derecho en el icono y seleccione **Editar título** para agregar un título personalizado.  Haga tantas personalizaciones como desee.

    ![Panel con Explorador de métricas](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Ahora tiene el gráfico del Explorador de métricas agregado al panel.

    ![Panel con Explorador de métricas](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Adición de una consulta de Analytics
Azure Application Insights Analytics proporciona un lenguaje de consulta completo que le permite analizar todos los datos recopilados por Application Insights.  Al igual que los gráficos y otras vistas, puede agregar el resultado de una consulta de Analytics al panel.   

Dado que Azure Applications Insights Analytics es un servicio independiente, debe compartir el panel para que incluya una consulta de Analytics.  Al compartir un panel de Azure, este se publica como un recurso de Azure que puede poner a disposición de otros usuarios y recursos.  

1. En la parte superior de la pantalla del panel, haga clic en **Compartir**.

    ![Publicación del panel](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Mantenga el mismo **Nombre del panel** y seleccione el **Nombre de la suscripción** para compartir el panel.  Haga clic en **Publicar**.  El panel ahora está disponible para otros servicios y suscripciones.  Si lo desea, puede definir los usuarios específicos que deben tener acceso al panel.
1. Seleccione **Application Insights** en el menú de Azure y, a continuación, seleccione la aplicación.
2. Haga clic en **Analytics** en la parte superior de la pantalla para abrir el portal de Analytics.

    ![Inicio de Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Escriba la siguiente consulta, que devuelve las 10 páginas más solicitadas y su número de solicitudes:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Haga clic en **Ir** para validar los resultados de la consulta.
5. Haga clic en el icono de anclaje y seleccione el nombre del panel.  La razón por la que esta opción le solicita que seleccione un panel, a diferencia de los pasos anteriores en los que se utilizaba el último panel, es que la consola de Analytics es un servicio independiente y tiene seleccionar entre todos los paneles compartidos disponibles.

    ![Anclaje de una consulta de Analytics](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Antes de volver al panel, agregue otra consulta, pero esta vez procésela como un gráfico para ver las distintas maneras de visualizar una consulta de Analytics en un panel.  Comience con la siguiente consulta, que resume las 10 operaciones principales con la mayoría de las excepciones.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Seleccione **Gráfico** y, a continuación, cambie a **Anillo** para visualizar el resultado.

    ![Gráfico de Analytics](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Haga clic en el icono de anclaje para anclar el gráfico en el panel y esta vez, seleccione el vínculo para volver al panel.
4. Los resultados de las consultas se agregan ahora al panel en el formato que seleccionó.  Haga clic y arrastre cada uno a su posición y, a continuación, haga clic en **Edición finalizada**.
5. Haga clic derecho en cada uno de los iconos y seleccione **Editar título** para darles un título descriptivo.

    ![Panel con Analytics](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Haga clic en **Publicar cambios** para confirmar los cambios del panel, que ahora incluye una serie de gráficos y visualizaciones de Application Insights.


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a crear paneles personalizados, eche un vistazo al resto de la documentación de Application Insights, que incluye un caso práctico.

> [!div class="nextstepaction"]
> [Diagnósticos detallados](app-insights-devops.md)