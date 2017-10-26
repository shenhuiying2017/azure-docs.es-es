---
title: "Creación y uso compartido de paneles de datos de Azure Log Analytics | Microsoft Docs"
description: "Con este tutorial le resultará más fácil comprender cómo los paneles de Log Analytics pueden mostrar todas las búsquedas de registros guardadas, lo que le permite ver su entorno en una sola vista."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 272945134b534a5ded794379ce5e96b0902a4227
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Creación y uso compartido de paneles de datos de Log Analytics

Los paneles de Log Analytics todas permiten visualizar todas las búsquedas de registro guardadas, lo que le proporciona la posibilidad de buscar, correlacionar y compartir datos operativos de TI en la organización.  Este tutorial trata la creación de una búsqueda de registros que se usará para admitir un panel compartido al que accederá su equipo de soporte técnico de operaciones de TI.  Aprenderá a:

> [!div class="checklist"]
> * Crear un panel compartido en Azure Portal.
> * Visualizar una búsqueda de registro de rendimiento. 
> * Agregar una búsqueda de registros a un panel compartido. 
> * Personalizar un icono en un panel compartido.

Para completar el ejemplo en este tutorial, debe disponer de una máquina virtual existente [conectada al área de trabajo de Log Analytics](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Creación de un panel compartido

Lo primero que verá después de iniciar sesión en Microsoft Azure Portal es un [panel](../azure-portal/azure-portal-dashboards.md).<br> ![Panel de Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Aquí puede agrupar los datos operacionales que sean más importantes para la TI en todos los recursos de Azure, incluidos los datos de telemetría de Azure Log Analytics.  Antes de que pasemos a la visualización de una búsqueda de registros, creemos primero un panel y compartámoslo.  Esto nos lleva a posponer este procedimiento antes de realizar nuestra búsqueda de registros de rendimiento de ejemplo, que se representará como un gráfico de líneas y se agregará al panel.  

Para crear un panel, haga clic en el botón **Panel nuevo** situado junto al nombre del panel actual.<br> ![Creación de un nuevo panel en Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Esta acción crea un panel nuevo, vacío y privado, y activa el modo de personalización, donde puede asignar un nombre al panel y agregar o reorganizar los iconos. Edite el nombre del panel y especifique *Panel de ejemplo* para este tutorial y, después, seleccione **Personalización finalizada**.<br><br> ![Guardado del panel personalizado de Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Al crear un panel, será privado de forma predeterminada, lo que significa que usted será la única persona que puede verlo. Para que puedan verlo otros usuarios, utilice el botón **Compartir** que aparece junto al resto de los comandos del panel.<br> ![Uso compartido de un nuevo panel en Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Se le pedirá que elija una suscripción y un grupo de recursos en el que se vaya a publicar el panel. Para mayor comodidad, la experiencia de publicación del portal lo guiará por un patrón donde colocará paneles en un grupo de recursos denominado " **paneles**".  Compruebe la suscripción seleccionada y, después, haga clic en **Publicar**.  El acceso a la información mostrada en el panel se controla con el [control de acceso basado en recursos de Azure](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualización de una búsqueda de registros

Puede crear consultas básicas en una sola línea desde el portal de búsqueda de registros en Azure Portal. El portal de búsqueda de registros se puede utilizar sin necesidad de iniciar un portal externo y se puede usar para realizar una serie de funciones con búsquedas de registros, incluida la creación de reglas de alertas, la creación de grupos de equipos y la exportación de los resultados de la consulta. 

El [portal de análisis avanzado](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) es un portal dedicado que proporciona funcionalidades avanzadas que no están disponibles en el portal de búsqueda de registros. Las características incluyen la capacidad para editar una consulta en varias líneas, ejecutar código de forma selectiva, Intellisense sensible al contexto y análisis inteligente. En el portal de análisis avanzado, creará una vista de rendimiento con forma gráfica, la guardará para futuras búsquedas y la anclará al panel compartido creado anteriormente.   

Inicie el portal de análisis avanzado desde un vínculo del portal de búsqueda de registros.<br> ![Inicio del portal de análisis avanzado](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

En el portal de análisis, escriba la siguiente consulta para devolver solo registros de uso del procesador para los equipos de Windows y Linux, agrupada por Equipo y TimeGenerated, y mostrada en un gráfico visual:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Guarde la consulta seleccionando el botón **Guardar consulta** en la esquina superior derecha.<br> ![Guardado de una consulta desde el portal de análisis avanzado](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> En el panel de control **Guardar consulta**, proporcione un nombre como *Máquinas virtuales de Azure - Uso del procesador* y, después, haga clic en **Guardar**.  De esta forma, puede crear una biblioteca de consultas comunes con la que realizar búsquedas o modificarla sin tener que volver a escribirla completamente.  Por último, ancle esto al panel compartido que creó anteriormente seleccionando el botón **Pin chart to your Azure dashboard** (Anclar gráfico al panel de Azure) en la esquina de la parte central derecha de la página.  

Ahora que tenemos una consulta anclada al panel, podrá observar que tiene un título genérico y un comentario debajo de él.<br> ![Ejemplo de panel de Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Debemos cambiarle el nombre a uno descriptivo que permita que las personas que lo vean lo entiendan fácilmente.  Haga clic con el botón derecho en el icono y seleccione **Editar mosaico**.  Una vez haya terminado de personalizar el título y el subtítulo para el icono, haga clic en **Actualizar**.  Aparecerá un banner en el que se le preguntará si desea publicar los cambios o rechazarlos.  Haga clic en **Publicar cambios** y, después, cierre el panel de control **Editar mosaico**.  

![Configuración completada del panel de ejemplo](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha aprendido a crear un panel en Azure Portal y a agregarle una búsqueda de registros.  Vaya al siguiente tutorial para conocer las diferentes respuestas que puede implementar según los resultados de búsqueda de registros.  

> [!div class="nextstepaction"]
> [Respuesta a eventos con las alertas de Log Analytics](log-analytics-tutorial-response.md)