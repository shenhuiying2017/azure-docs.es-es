<properties 
	pageTitle="Supervisión y administración de la Factoría de datos de Azure mediante el Portal de vista previa de Azure" 
	description="Obtenga información acerca de cómo usar el Portal de administración de Azure para supervisar y administrar las factorías de datos de Azure que ha creado." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Supervisión de la Factoría de datos de Azure mediante el Portal de vista previa de Azure
Este artículo describe varios escenarios para utilizar el Portal de vista previa de Azure para supervisar y administrar Factoría de datos de Azure.

## <a name="AllDataFactories"></a> Vista de todas las factorías de datos de una suscripción de Azure

- Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
- Haga clic en el concentrador **EXAMINAR** de la izquierda y haga clic en **Factorías de datos**.  

	![Concentrador EXAMINAR -> Factorías de datos][image-data-factory-browse-datafactories]

	Si no ve **Factorías de datos**, haga clic en **Todos** y luego en **Factorías de datos** en la hoja **Examinar**.

	![Concentrador EXAMINAR -> Todo][image-data-factory-browse-everything]

- Debería ver todas las factorías de datos en la hoja **Factorías de datos**.

	![Hoja Factorías de datos][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Vista de detalles sobre una factoría de datos

Para ver los detalles sobre una factoría de datos, realice una de las acciones siguientes:


- Haga clic en una factoría de datos en la hoja **Factorías de datos** mostrada anteriormente.
- Haga clic en el vínculo de la factoría de datos en el **Panel de inicio**. **Panel de inicio** es la hoja que ve cuando inicia sesión en el Portal de vista previa de Azure. Si había seleccionado **Agregar al Panel de inicio** al crear una factoría de datos (opción predeterminada), debería ver el vínculo de factoría de datos en el Panel de inicio como se muestra en la siguiente imagen. En este ejemplo, los vínculos a las factorías de datos **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** y **LogProcessingFactory** están disponibles en el **Panel de inicio**.


![Factoría de datos del Panel de inicio][image-data-factory-datafactory-from-startboard]

En cualquier caso, verá la hoja **FACTORÍA DE DATOS** para la factoría de datos seleccionada como se muestra en la siguiente imagen.

 ![Página principal de Factoría de datos][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Vista de una vista de diagrama de la factoría de datos
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en la ventana **Diagrama** para ver la vista de diagrama de la factoría de datos.

![Vista de diagrama de Factoría de datos][image-data-factory-diagram-view]
 
### Abrir una canalización en la vista de diagrama
Para ver todas las actividades en una canalización haga clic con el botón secundario en la canalización de la vista de diagrama y haga clic en **Abrir canalización**. Debería ver las actividades en la canalización junto con los conjuntos de datos de entrada y salida para las actividades. ![Abrir canalización](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Haga clic en **Factoría de datos** en la ruta de navegación de la esquina superior izquierda para volver a la vista de diagrama. La vista de diagrama muestra todas las canalizaciones. En este ejemplo, solo ha creado una canalización.

## <a name="DataFactoryLinkedServices"></a> Ver servicios vinculados en una factoría de datos
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en la ventana **Servicios vinculados** para ver todos los servicios vinculados en una lista.

![Hoja Servicios vinculados][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Ver detalles sobre un servicio vinculado
En la hoja **SERVICIOS VINCULADOS**, haga clic en el servicio vinculado de la lista para ver detalles sobre él.

![Hoja Servicio vinculado][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Ver conjuntos de datos en una factoría de datos 
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en la ventana **Conjuntos de datos** para ver todas las tablas de la factoría de datos.

![Hoja Conjuntos de datos][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Ver detalles sobre un conjunto de datos
Haga clic en el conjunto de datos de la lista de conjuntos de datos en la hoja CONJUNTOS DE DATOS para ver detalles sobre el conjunto de datos. Tenga en cuenta que una tabla es un conjunto de datos rectangular que tiene un esquema. Es el único tipo de conjunto de datos admitido en este momento.

![Hoja de tabla][image-data-factory-table]

En la hoja **TABLA** anterior, las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.

-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	
Para ver los segmentos de datos ordenados por las horas de inicio y finalización, haga clic en la ventana **Segmentos de datos (por hora del segmento)**.
 
![Segmentos de datos por tiempo de segmento][DataSlicesBySliceTime]

Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos.

![Todos los segmentos de una tabla][image-data-factory-all-slices]

En la hoja **Segmentos de datos**, haga clic en el botón de **filtro** para ver la hoja de **filtro** que le permite **filtrar** segmentos para ver los segmentos específicos que desea revisar. Verá una hoja similar a la siguiente cuando haga clic en **Filtro** en la hoja **Segmentos de datos** con los segmentos **ordenados por fecha de actualización**.

![Hoja de filtro][image-data-factory-filter-blade]

La hoja **Filtro** le permite filtrar según la **última hora de actualización** y el **estado del segmento**. En la tabla siguiente se describen todos los estados de segmentos y su descripción.
 
Estado de segmento | Descripción
------------ | ------------
PendingExecution | El procesamiento de datos no se ha iniciado aún.
InProgress | El procesamiento de datos está en curso.
Ready | El procesamiento de datos se ha completado y el segmento de datos está listo.
Failed | Error de ejecución de la ejecución que produce el segmento.
Skip | Se omite el procesamiento del segmento.
Retry | Reintento de la ejecución que produce el segmento.
Timed Out | Se agotó el tiempo de espera del procesamiento de datos del sector.
PendingValidation | El segmento de datos está esperando la validación contra las directivas de validación antes de procesarse.
RetryValidation | Reintento de la validación del segmento.
FailedValidation | Error de validación del segmento.
LongRetry | Un segmento estará en este estado si se especifica LongRetry en la tabla JSON y los reintentos periódicos del segmento han dado error.
ValidationInProgress | Se está realizando la validación del segmento (según las directivas definidas en la tabla JSON).

Al hacer clic en **Filtro** en la hoja **Segmentos de datos** con los segmentos **ordenados por la hora de segmento**, verá un tipo distinto de hoja de **filtro**.

![Hoja de filtro 2][image-data-factory-filter-blade-2]


Al iniciar la hoja de **filtro**, el campo **Para** se establecerá automáticamente en la hora más reciente (redondeada) para limitar el número de registros devueltos. El campo **Desde** se establece también automáticamente. Para cambiar la fecha **Desde** haga clic en el botón **Calendario**. La fecha **Hasta** se cambia automáticamente al cambiar la fecha **Desde**.

Puede hacer clic en los botones **Anterior**/**Siguiente** para ver segmentos en el período anterior o siguiente. El intervalo de tiempo para los botones **Anterior** y **Siguiente** se establece en el intervalo y la frecuencia de segmentos como se muestra en la tabla siguiente.

Frecuencia | Rango de valor de intervalo | Fragmento de tiempo resultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 horas
Minuto | 5-29 | 1 día
Minuto | 30-180 | 7 días
Minuto | + 180 | 28 días (aproximado, mes del calendario)
Hora | 1-3 | 7 días
Hora | 4-11 | 28 días (aproximado, mes del calendario)
Hora | 12-72 | 182 días (aproximado, 6 meses)
Hora | + 73 | 1 año
Día | 1-6 | 1 año
Día | 7-20 | 5 años
Día | + 21 | 10 años
Semana | 1-3 | 5 años
Semana | + 4 | 10 años
Mes | cualquiera | 10 años
 
Por ejemplo, si define la **frecuencia** como **Hora** y el **intervalo** de **2**, haga clic en los botones **Siguiente**/**Anterior** para mover el intervalo de tiempo **7 días** en cualquier dirección. Esta lógica se aplica a la hoja de filtro si está viendo todos los segmentos, los segmentos recientes o los segmentos de problemas.




## <a name="DataFactorySlice"></a> Ver detalles sobre un sector
Haga clic en un segmento de la lista de segmentos en la hoja **TABLA** o la hoja **Segmentos de datos** para ver detalles sobre ese segmento.

![Segmento de datos][image-data-factory-dataslice]

Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.

### <a name="DataFactoryActivtyRuns"></a> Ver todas las ejecuciones de actividad para un segmento
Para un segmento, puede haber más de una ejecución. Por ejemplo, cuando un segmento da error, el servicio puede realizar algunos reintentos. También puede volver a ejecutar un segmento que ha dado error en todos los reintentos. Puede ver todas las ejecuciones de actividad en la hoja **Segmento de datos** de la lista de la parte inferior.

## <a name="DataFactoryActivtyRunDetails"></a> Ver detalles sobre una ejecución de actividad
Haga clic en una ejecución de actividad de la lista de ejecuciones de la hoja **Segmento de datos** para ver los detalles sobre la ejecución de actividad.

![Detalles de ejecución de actividad][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Modos de operaciones: eventos de la semana pasada
En la hoja **FACTORÍA DE DATOS** (o la página principal) de la factoría de datos, haga clic en **Eventos de la pasada semana** en el modo **Operaciones** para ver los eventos de la semana pasada. Esto le ayuda a obtener una vista de alto nivel de las operaciones realizadas por la factoría de datos en la semana pasada. También le ayuda a solucionar los errores con el movimiento o procesamiento de los datos.

![Eventos de Factoría de datos][image-data-factory-events]


## Otras referencias

Artículo | Descripción
------ | ---------------
[Supervisar y administrar la factoría de datos de Azure con PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo supervisar una factoría de datos de Azure mediante cmdlets de Azure PowerShell. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=62-->