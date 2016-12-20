---
title: "Supervisión y administración de canalizaciones de la Factoría de datos de Azure"
description: "Obtenga información sobre cómo usar la Aplicación de supervisión y administración para supervisar y administrar factorías de datos y canalizaciones de Azure"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 0bb9269d20c157221faa5604e68acc3410a5247b
ms.openlocfilehash: d8e5e8e99d40af959f8fa1dd4bf7b636a9f2343b


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración
> [!div class="op_single_selector"]
> * [Uso del Portal de Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Uso de la Aplicación de supervisión y administración](data-factory-monitor-manage-app.md)
> 
> 

En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones y crear alertas para recibir notificaciones sobre los errores mediante la **aplicación de supervisión y administración**. También puede ver el siguiente vídeo para aprender a usar la aplicación de supervisión y administración.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
> 
> 

## <a name="launching-the-monitoring-and-management-app-a"></a>Inicio de la aplicación de supervisión y administración
Para iniciar la aplicación de supervisión y administración, haga clic en el icono **Monitoring & Manage** (Supervisión y administración), de la hoja **DATA FACTORY** de su factoría de datos.

![Icono de supervisión de la página principal de Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

La Aplicación de supervisión y administración debería iniciarse en una pestaña o ventana independiente.  

![aplicación de supervisión y administración](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Si ve que el explorador web está atascado en "Autorizando...", deshabilite o desactive la opción **Block third party cookies and site data (Bloquear cookies y datos de sitios de terceros)**, o bien déjela habilitada y cree una excepción para **login.microsoftonline.com** e intente volver a iniciar la aplicación.
> 
> 

Si no ve las ventanas de actividad en la lista de la parte inferior, haga clic en el botón **Actualizar** de la barra de herramientas para actualizar dicha lista. Además, establezca los valores correctos en los filtros **Hora de inicio** y **Hora de finalización**.  

## <a name="understanding-the-monitoring-and-management-app"></a>Descripción de la Aplicación de supervisión y administración
Hay tres pestañas (**Resource Explorer** [Explorador de recursos], **Monitoring Views** [Vistas de supervisión] y **Alerts** [Alertas]) a la izquierda y la primera de ellas está seleccionada de manera predeterminada. 

### <a name="resource-explorer"></a>Explorador de recursos
Verá lo siguiente: 

* **Vista de árbol** del explorador de recursos en el panel izquierdo.
* **Vista de diagrama** en la parte superior.
* **Activity Windows** (Ventanas de actividad) en la parte inferior del panel central.
* **Propiedades**/**Activity Window Explorer** (Explorador de ventanas de actividad) en el panel derecho. 

En el Explorador de recursos, aparecen todos los recursos (canalizaciones, conjuntos de datos, servicios vinculados) de la factoría de datos en una vista de árbol. Al seleccionar un objeto en el Explorador de recursos, observará lo siguiente: 

* La entidad de Data Factory asociada se resaltará en la vista de diagrama.
* Las ventanas de actividad asociadas (haga clic [aquí](data-factory-scheduling-and-execution.md) para obtener más información sobre las ventanas de actividad) se resaltarán en la lista Activity Windows (Ventanas de actividad) de la parte inferior.  
* Las propiedades del objeto seleccionado aparecen en la ventana Propiedades, en el panel derecho. 
* La definición de JSON del objeto seleccionado, si procede. Por ejemplo: un servicio vinculado, un conjunto de datos o una canalización. 

![Explorador de recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte el artículo sobre [programación y ejecución](data-factory-scheduling-and-execution.md) para obtener información conceptual detallada sobre la ventana de actividad. 

### <a name="diagram-view"></a>Vista de diagrama
La Vista de diagrama de una factoría de datos ofrece un panel único para supervisar y administrar la factoría de datos y sus recursos. Cuando seleccione una entidad de Data Factory (conjunto de datos o canalización) en la vista de diagrama, observará lo siguiente:

* La entidad de Data Factory se selecciona en la vista de árbol.
* Las ventanas de actividad asociadas se resaltan en la lista Ventanas de actividad.
* Las propiedades del objeto seleccionado aparecen en la ventana Propiedades.

Si la canalización está habilitada (es decir, no está en estado de pausa), se indicará con una línea verde. 

![Canalización en ejecución](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Observe que hay tres botones de comando para la canalización en la vista de diagrama. Puede usar el segundo botón para pausar la canalización. La pausa no finalizará las actividades que se están ejecutando, sino que estas continuarán hasta su finalización. El tercer botón pausa la canalización y finaliza las actividades que se están ejecutando. El primer botón reanuda la canalización. Cuando la canalización esté en pausa, observará que el icono de canalización cambia de color tal como se muestra a continuación.

![Pausar o reanudar en el icono](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Puede seleccionar al mismo tiempo dos o más canalizaciones (mediante la tecla CTRL) y usar los botones de la barra de comandos para pausar o reanudar varias canalizaciones a la vez.

![Pausar o reanudar en la barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Para ver todas las actividades de la canalización, haga clic con el botón derecho en el icono de canalización y haga clic en **Abrir canalización**.

![Menú Abrir canalización](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

En la vista de canalización que se abre, verá todas las actividades de la canalización. En este ejemplo, solamente hay una actividad: la actividad de copia. Para volver a la vista anterior, haga clic en el nombre de la factoría de datos en el menú de la ruta de navegación en la parte superior.

![Canalización abierta](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

En la vista de canalización, al hacer clic en un conjunto de datos de salida o pasar el mouse por él, verá el elemento emergente de las ventanas de actividad de ese conjunto de datos.

![Notificación emergente de Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Puede hacer clic en una ventana de actividad para ver sus detalles en la ventana **Propiedad** del panel derecho. 

![Propiedades de la ventana de actividad](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

En el panel derecho, cambie a la pestaña **Activity Window Explorer** (Explorador de ventanas de actividad) para ver más información.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

También verá las **variables resueltas** de cada intento de ejecución de actividad en la sección **Intentos**. 

![Variables resueltas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Cambie a la pestaña **Script** para ver la definición del script de JSON del objeto seleccionado.   

![Pestaña de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Puede ver las ventanas de actividad en tres lugares:

* El elemento emergente de ventanas de actividad en la vista de diagrama (panel central)
* El Explorador de ventanas de actividad en el panel derecho.
* La lista Ventanas de actividad en el panel inferior.

En el elemento emergente de ventanas de actividad y en el Explorador de ventanas de actividad, puede desplazarse a la semana anterior y la semana siguiente usando las flechas izquierda y derecha.

![Flechas izquierda/derecha del Explorador de ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

En la parte inferior de la vista de diagrama, aparecen los botones para acercar, alejar, hacer zoom para ajustar, hacer zoom al 100% y bloquear el diseño. El botón para bloquear el diseño impide que se muevan por error tablas y canalizaciones en la vista de diagrama, y está activo de forma predeterminada. Puede desactivarlo y mover las entidades por el diagrama. Si lo desactiva, puede usar el último botón para colocar automáticamente las tablas y las canalizaciones. También puede acercar o alejar usando la rueda del mouse.

![Comandos de zoom de la vista de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Ventanas de actividad
La lista Ventanas de actividad de la parte inferior del panel central muestra todas las ventanas de actividad del conjunto de datos seleccionado en el Explorador de recursos o en la vista de diagrama. De forma predeterminada, la lista está en orden descendente, lo que significa que en la parte superior aparece la ventana de actividad más reciente. 

![Lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista no se actualiza automáticamente, por lo que debe usar el botón Actualizar de la barra de herramientas para actualizarla manualmente.  

Las ventanas de actividad pueden estar en uno de los siguientes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Description</th>
</tr>
<tr>
    <td rowspan="8">En espera</td><td>ScheduleTime</td><td>No ha llegado la hora de que se ejecute la ventana de actividad.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias de nivel superior no están listas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>No están disponibles los recursos de proceso.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas con la ejecución de otras ventanas de actividad.</td>
</tr>
<tr>
<td>ActivityResume</td><td>La actividad está en pausa y no puede ejecutar las ventanas de actividad hasta que se reanude.</td>
</tr>
<tr>
<td>Retry</td><td>Se vuelve a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>En espera de que se vuelva a intentar la validación.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Validación en curso.</td>
</tr>
<td>-</td>
<td>La ventana de actividad se está procesando.</td>
</tr>
<tr>
<td rowspan="4">Con error</td><td>TimedOut</td><td>La ejecución tardó más tiempo del permitido por la actividad.</td>
</tr>
<tr>
<td>Canceled</td><td>Cancelado por acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td>-</td><td>No se pudo generar o validar la ventana de actividad.</td>
</tr>
<td>Ready</td><td>-</td><td>La ventana de actividad está lista para su uso.</td>
</tr>
<tr>
<td>Skipped</td><td>-</td><td>La ventana de actividad no se ha procesado.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Una ventana de actividad que existía con un estado distinto, pero que se ha restablecido.</td>
</tr>
</table>


Al hacer clic en una de las ventanas de actividad de la lista, podrá ver sus detalles en **Activity Windows Explorer** (Explorador de ventanas de actividad) o en la ventana **Properties** (Propiedades) de la derecha.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Actualizar las ventanas de actividad
Los detalles no se actualizan automáticamente, por lo que debe usar el botón **Actualizar** (segundo botón) de la barra de comandos para actualizar manualmente la lista de ventanas de actividad.  

### <a name="properties-window"></a>Ventana Propiedades
La ventana Propiedades está en el panel del extremo derecho de la Aplicación de supervisión y administración. 

![Ventana Propiedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Muestra propiedades del elemento seleccionado en el Explorador de recursos en la vista de árbol, la vista de diagrama o la lista de ventanas de actividad. 

### <a name="activity-window-explorer"></a>Activity Window Explorer
La ventana **Activity Window Explorer** (Explorador de ventanas de actividad) se encuentra en el panel del extremo derecho de la aplicación de supervisión y administración. Muestra detalles de la ventana de actividad seleccionada en la notificación emergente Ventanas de actividad o en la lista Ventanas de actividad. 

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Puede cambiar a una ventana de actividad diferente haciendo clic en ella en la vista de calendario de la parte superior. También puede usar los botones de **flecha izquierda**/**flecha derecha** de la parte superior para ver las ventanas de actividad de las semanas anterior o siguiente.

Puede usar los botones de la barra de herramientas del panel inferior para **volver a ejecutar** la ventana de actividad o **actualizar** los detalles del panel. 

### <a name="script"></a>Script
Puede usar la pestaña **Script** para ver la definición de JSON de la entidad de Data Factory seleccionada (servicio vinculado, conjunto de datos y canalización). 

![Pestaña de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>Usar vistas de sistema
La aplicación de supervisión y administración incluye vistas del sistema pregeneradas (**Ventanas de actividad recientes**, **Ventanas de actividades con error** y **Ventanas de actividad en curso**) que permiten ver las ventanas de actividad recientes, las que contienen errores o las que están en curso de su factoría de datos. 

Cambie a la pestaña **Monitoring Views** (Vistas de supervisión) de la izquierda haciendo clic en ella. 

![Pestaña Vistas de supervisión](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Actualmente, hay tres vistas del sistema compatibles. Seleccione una opción para ver las ventanas de actividad recientes, las ventanas de actividad con error o las ventanas de actividad en curso en la lista Ventanas de actividad (en la parte inferior del panel central). 

Al seleccionar la opción **Recent activity windows** (Ventanas de actividad reciente), se ven todas las ventanas de actividad reciente en orden descendente de **hora del último intento**. 

Puede usar la vista **Failed activity windows** (Ventanas de actividad con error) para ver todas las ventanas de actividad que contengan errores de la lista. Seleccione una ventana de actividad con error de la lista para ver sus detalles en la ventana **Properties** (Propiedades) (o) **Activity Window Explorer** (Explorador de ventanas de actividad). También puede descargar los registros de una ventana de actividad con error. 

## <a name="sorting-and-filtering-activity-windows"></a>Ordenar y filtrar ventanas de actividad
Cambie la configuración de **hora de inicio** y **hora de finalización** en la barra de comandos para filtrar las ventanas de actividad. Después de cambiar la hora de inicio y de finalización, haga clic en el botón situado junto a la hora de finalización para actualizar la lista Ventanas de actividad.

![Horas de inicio y finalización](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Actualmente, todas las horas están en formato UTC en la aplicación de supervisión y administración. 
> 
> 

En la **lista de ventanas de actividad**, haga clic en el nombre de una columna (por ejemplo, Estado). 

![Menú de columna de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Puede realizar las siguientes acciones:

* Ordenar en orden ascendente.
* Ordenar en orden descendente.
* Filtrar por uno o más valores (Listo, En espera, etc.).

Cuando especifique un filtro en una columna, verá que el botón de filtro se habilita para esa columna, a fin de indicar que los valores de la columna están filtrados. 

![Filtro de columna de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Puede usar la misma ventana emergente para borrar filtros. Para borrar todos los filtros de la lista Ventanas de actividad, haga clic en el botón para borrar el filtro de la barra de comandos. 

![Borrar todos los filtros de la lista Ventanas de actividad](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="performing-batch-actions"></a>Realizar acciones por lotes
### <a name="rerun-selected-activity-windows"></a>Volver a ejecutar las ventanas de actividad seleccionadas
Seleccione una ventana de actividad, haga clic en la flecha hacia abajo del primer botón de la barra de comandos y seleccione **Rerun** (Volver a ejecutar) / **Rerun with upstream in pipeline** (Volver a ejecutar con canal de subida en la canalización). Al seleccionar la opción **Rerun with upstream in pipeline** (Volver a ejecutar con canal de subida en la canalización), también se vuelven a ejecutar todas las ventanas de actividad con canal de subida. 
    ![Volver a ejecutar una ventana de actividad](./media/data-factory-monitor-manage-app/ReRunSlice.png)

También puede seleccionar varias ventanas de actividad en la lista y volver a ejecutarlas al mismo tiempo. Puede filtrar las ventanas de actividad según el estado (por ejemplo, **Con error**) y, después, volver a ejecutar las ventanas de actividad con error tras corregir el problema que hace que se produzca un error en las ventanas de actividad. Vea la siguiente sección para obtener más información sobre el filtrado de ventanas de actividad en la lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausar y reanudar varias canalizaciones
Puede seleccionar al mismo tiempo dos o más canalizaciones (mediante la tecla CTRL) y usar los botones de la barra de comandos (resaltados con un rectángulo rojo en la imagen siguiente) para pausarlas o reanudarlas a la vez.

![Suspender o reanudar en la barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>Crear alertas
La página de alertas le permite crear una alerta nueva, así como ver, editar o eliminar las alertas existentes. También puede habilitar o deshabilitar una alerta. Haga clic en la pestaña Alertas para ver la página de alertas.

![Pestaña Alertas](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Para crear una alerta
1. Haga clic en **Agregar alerta** para agregar una alerta. Verá la página Detalles. 
   
    ![Crear alertas: página de detalles](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. Especifique el **nombre** y la **descripción** de la alerta y haga clic en **Siguiente**. Verá la página **Filtros** .
   
    ![Crear alertas: página de filtros](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Seleccione el **evento**, el **estado** y el **subestado** (opcional) sobre los que desea que el servicio Data Factory le alerte y haga clic en **Siguiente**. Verá la página **Destinatarios** .
   
    ![Crear alertas: página de destinatarios](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
4. Seleccione la opción **Email subscription admins** (Administradores de suscripciones de correo electrónico) o **escriba un correo electrónico de administrador adicional** y haga clic en **Finalizar**. Debería ver la alerta en la lista. 
   
    ![Lista de alertas](./media/data-factory-monitor-manage-app/AlertsList.png)

En la lista de alertas, use los botones asociados con la alerta para editarla, eliminarla, habilitarla o deshabilitarla. 

### <a name="eventstatussubstatus"></a>Evento/estado/subestado
En la tabla siguiente se ofrece una lista de los eventos y los estados (y subestados) disponibles.

| Nombre del evento | Estado | Subestado |
| --- | --- | --- |
| Ejecución de actividad iniciada |Started |Iniciando |
| Ejecución de actividad finalizada |Correcto |Correcto |
| Ejecución de actividad finalizada |Con error |Asignación de recursos errónea<br/><br/>Ejecución con errores<br/><br/>Timed Out<br/><br/>Failed Validation<br/><br/>Abandoned |
| Creación de clúster de HDI a petición iniciada |Started |-|
| Creación correcta de clúster de HDI a petición |Correcto |-|
| Clúster de HDI a petición eliminado |Correcto |-|

### <a name="to-editdeletedisable-an-alert"></a>Para editar, eliminar o deshabilitar una alerta
![Botones de alertas](./media/data-factory-monitor-manage-app/AlertButtons.png)




<!--HONumber=Nov16_HO3-->


