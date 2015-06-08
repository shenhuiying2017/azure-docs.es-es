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
Este artículo describe varios escenarios para utilizar el Portal de vista previa de Azure para supervisar y administrar la factoría de datos de Azure.

## <a name="AllDataFactories"></a> Ver todos los generadores de datos en una suscripción de Azure

- Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
- Haga clic en **Examinar** concentrador izquierda y haga clic en **generadores de datos**.  

	![Examinar concentrador -> generadores de datos][image-data-factory-browse-datafactories]

	Si no ve **generadores de datos**, haga clic en **todo** y, a continuación, haga clic en **datos factorries** en el **Examinar** blade.

	![Concentrador de examinar todo ->][image-data-factory-browse-everything]

- Debería ver todos los generadores de datos en el **generadores de datos** blade.

	![Módulo de generadores de datos][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Ver detalles acerca de un generador de datos

Para ver los detalles sobre una factoría de datos, realice una de las acciones siguientes:


- Haga clic en un generador de datos en el **generadores de datos** blade mostrado anteriormente.
- Haga clic en el vínculo para el generador de datos en el **panel de inicio**. **Panel de inicio** es el módulo que verá cuando inicie sesión en el portal de vista previa de Azure. Si se ha seleccionado **Agregar al panel de inicio** al crear un generador de datos (opción predeterminada), verá el generador de datos de vínculo en el panel de inicio tal como se muestra en la siguiente imagen. En este ejemplo, **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** y **LogProcessingFactory** vínculos de generador de datos están disponibles en la **panel de inicio**.


![Generador de datos en el panel de inicio][image-data-factory-datafactory-from-startboard]

En cualquier caso, verá la **factoría de datos** blade para el generador de datos seleccionado, como se muestra en la siguiente imagen.

 ![Página de inicio del generador de datos][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Vista de diagrama de la vista del generador de datos
En el **factoría de datos** blade para el generador de datos, haga clic en **diagrama** mosaico para ver la vista de diagrama del generador de datos.

![Vista de diagrama de generador de datos][image-data-factory-diagram-view]
 
### Abrir una canalización en la vista de diagrama
Puede ver todas las actividades en una canalización con el botón secundario en la vista de diagrama de la canalización de y haciendo clic en **abrir canalización**. Debería ver las actividades en la canalización, junto con los conjuntos de datos de entrada y salida para las actividades. ![Canalización abierto](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Haga clic en **factoría de datos** en la ruta de exploración en la esquina superior izquierda para volver a la vista de diagrama. La vista de diagrama muestra todas las canalizaciones. En este ejemplo, solo ha creado una canalización.

## <a name="DataFactoryLinkedServices"></a> Ver servicios vinculados en un generador de datos
En el **factoría de datos** blade para el generador de datos, haga clic en **servicios vinculados** mosaico para ver todos los servicios vinculados en una lista.

![Módulo de servicios vinculados][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Ver detalles acerca de un servicio vinculado
En el **servicios vinculados** blade, haga clic en el servicio vinculado desde la lista para ver detalles acerca de él.

![Módulo de servicio vinculado][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Conjuntos de datos de vista de un generador de datos 
En el **factoría de datos** blade para el generador de datos, haga clic en **conjuntos de datos** mosaico para ver todas las tablas en el generador de datos.

![Módulo de conjuntos de datos][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Ver detalles acerca de un conjunto de datos
Haga clic en el conjunto de datos de la lista de conjuntos de datos en la hoja CONJUNTOS DE DATOS para ver detalles sobre el conjunto de datos. Tenga en cuenta que una tabla es un conjunto de datos rectangular que tiene un esquema. Es el único tipo de conjunto de datos admitido en este momento.

![Cuadro de tabla][image-data-factory-table]

En el **tabla** blade anteriormente, ambos **actualizado recientemente sectores** y **error recientemente sectores** listas se ordenan por el **hora de última actualización**. En las situaciones siguientes, se cambia la hora de un intervalo de actualización.

-  Actualiza el estado del sector manualmente, por ejemplo, utilizando la **conjunto AzureDataFactorySliceStatus** (o), haga clic en **ejecutar** en el **sector** módulo para el sector.
-  El segmento cambia estado debido a una ejecución (por ejemplo, una ejecución iniciado, una ejecución finalizado y no se pudo, una ejecución finaliza y se realizó correctamente, etc.).
 
	
Para ver los intervalos de datos ordenados por los tiempos de inicio y fin del sector en su lugar, haga clic en **rebanadas de datos (por hora de sector)** en mosaico.
 
![Segmentos de datos por intervalo de tiempo][DataSlicesBySliceTime]

Haga clic en el título de las listas o **... (puntos suspensivos)** Para ver la lista más amplia de sectores.

![Todos los sectores de una tabla][image-data-factory-all-slices]

En el **rebanadas de datos** blade, haga clic en el **filtro** botón para ver el **filtro** cuadro que le permite **filtro** sectores para ver los intervalos específicos que desee revisar. Aparecerá el cuadro similar al siguiente al hacer clic en **filtro** en el **rebanadas de datos** blade con sectores **ordenados por fecha de actualización**.

![Módulo de filtro][image-data-factory-filter-blade]

El **filtro** blade le permite filtrar según **fecha de última actualización** y **segmentar estado**. En la tabla siguiente describe los Estados del sector y su descripción.
 
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
RetryValidation | Reintentando la validación del sector.
FailedValidation | Error de validación del segmento.
LongRetry | Un segmento estará en este estado si se especifica LongRetry en la tabla JSON y los reintentos periódicos del segmento han dado error.
ValidationInProgress | Se está realizando la validación del segmento (según las directivas definidas en la tabla JSON).

Al hacer clic en **filtro** en el **rebanadas de datos** blade con sectores **ordenados por el tiempo de intervalo**, verá un tipo diferente de **filtro** blade.

![Módulo de filtro 2][image-data-factory-filter-blade-2]


Al iniciar el **filtro** blade, el **a** campo se establece automáticamente en el momento más reciente (redondeado) para limitar el número de registros devueltos. El **de** campo se establece automáticamente también. Puede cambiar la **de** fecha haciendo clic en el **calendario** botón. El **a** fecha se cambia automáticamente al cambiar la **de** fecha.

Puede hacer clic en **anterior**/ ** siguiente ** botones para ver sectores en la versión anterior de período/siguiente período. El intervalo de tiempo para **anterior** y **siguiente** botones se establecen en función de la frecuencia de segmento y el intervalo, como se muestra en la tabla siguiente.

Frecuencia | Valor de intervalo | Fragmento de tiempo resultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 horas
Minuto | 5-29 | 1 día
Minuto | 30 a 180 | 7 días
Minuto | 180 + | 28 días (aproximado. mes natural)
Hora | 1-3 | 7 días
Hora | 4-11 | 28 días (aproximado. mes natural)
Hora | 12-72 | 182 días (aproximados. 6 meses)
Hora | 73 + | 1 año
Día | 1-6 | 1 año
Día | 7-20 | 5 años
Día | más de 21 | 10 años
Semana | 1-3 | 5 años
Semana | 4 + | 10 años
Mes | cualquiera | 10 años
 
Por ejemplo, si define **frecuencia** como **hora** y **intervalo** de **2**, haga clic en el **siguiente**/ ** anterior ** botones Mover el intervalo de tiempo **7 días** en cualquier dirección. Esta lógica se aplica a la hoja de filtro si está viendo todos los segmentos, los segmentos recientes o los segmentos de problemas.




## <a name="DataFactorySlice"></a> Ver detalles acerca de un sector
Haga clic en un sector en la lista de intervalos ya sea en el **tabla** blade o **rebanadas de datos** blade para ver detalles sobre dicho sector.

![Segmento de datos][image-data-factory-dataslice]

Si no se encuentra en el sector del **Listo** estado, puede ver los intervalos de nivel superior que no están listos y están bloqueando el intervalo actual de la ejecución en el **intervalos de nivel superior que no están listos** lista.

### <a name="DataFactoryActivtyRuns"></a> Ver que toda la actividad se ejecuta para un sector
Para un segmento, puede haber más de una ejecución. Por ejemplo, cuando un segmento da error, el servicio puede realizar algunos reintentos. También puede volver a ejecutar un segmento que ha dado error en todos los reintentos. Puede ver toda la actividad se ejecuta en el ** datos sector ** blade en la lista en la parte inferior.

## <a name="DataFactoryActivtyRunDetails"></a> Ver detalles acerca de una actividad se ejecute
Haga clic en una actividad que se ejecute desde la lista de ejecuciones de la **segmento de datos** blade para ver detalles acerca de la actividad se ejecute.

![Detalles de ejecución de actividad][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Operaciones lente - eventos de la semana pasada
En el **factoría de datos** blade (o página principal) para el generador de datos, haga clic en **los eventos de la semana pasada** en **Operations** lente para ver los eventos de la semana pasada. Esto le ayuda a obtener una vista de alto nivel de las operaciones realizadas por la factoría de datos en la semana pasada. También le ayuda a solucionar los errores con el movimiento o procesamiento de los datos.

![Eventos del generador de datos][image-data-factory-events]


## Otras referencias

Artículo | Descripción
------ | ---------------
[Monitor y administrar factoría de datos Azure con PowerShell][monitor-manage-using-powershell] | En este artículo se describe cómo supervisar un generador de datos de Azure mediante cmdlets de PowerShell de Azure. 


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

<!---HONumber=GIT-SubDir-->