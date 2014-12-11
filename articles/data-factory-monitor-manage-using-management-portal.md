<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="Supervisión y administración de la Factoría de datos de Azure mediante el Portal de vista previa de Azure" description="Learn how to use Azure Management Portal to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Supervisión de la Factoría de datos de Azure mediante el Portal de vista previa de Azure

- [Vista de todas las factorías de datos de una suscripción de Azure](#AllDataFactories)
- [Ver detalles acerca de una factoría de datos](#DataFactoryDetails)
- [Ver una vista de diagrama de una factoría de datos](#DataFactoryDiagram)
- [Vista de los servicios vinculados de una factoría de datos](#DataFactoryLinkedServices)
- [Vista de detalles acerca de un servicio vinculado](#DataFactoryLinkedService) 
- [Vista de conjuntos de datos de una factoría de datos](#DataFactoryDatasets)
- [Vista de detalles acerca de un conjunto de datos](#DataFactoryDataset)
- [Vista de detalles acerca de un sector](#DataFactorySlice) 
- [Vista de todas las ejecuciones de actividad de un segmento](#DataFactoryActivtyRuns) 
- [Vista de detalles acerca de una ejecución de actividad](#DataFactoryActivtyRunDetails)
- [Modos de operaciones: eventos de la semana pasada](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Vista de todas las factorías de datos de una suscripción de Azure

- Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal].
- Haga clic en el centro **EXAMINAR** de la izquierda y haga clic en **Factorías de datos**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Si no ve **Factorías de datos**, haga clic en **Todo** y luego en **Factorías de datos** en la hoja **Examinar**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- Verá todas las factorías de datos en la hoja **Factorías de datos**.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Ver detalles acerca de una factoría de datos

Para ver los detalles sobre una factoría de datos, realice una de las acciones siguientes: 


- Haga clic en una factoría de datos en la hoja **Factorías de datos** mostrada anteriormente.
- Haga clic en el vínculo de la factoría de datos en el **Panel de inicio**. **Panel de inicio** es la hoja que ve cuando inicia sesión en el Portal de vista previa de Azure. Si había seleccionado **Agregar al Panel de inicio** al crear una factoría de datos (opción predeterminada), verá el vínculo de factoría de datos en el Panel de inicio como se muestra en la siguiente imagen. En este ejemplo, los vínculos a las factorías de datos **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** y **LogProcessingFactory** están disponibles en el **Panel de inicio**.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

En cualquier caso, verá la hoja **FACTORÍA DE DATOS** para la factoría de datos seleccionada como se muestra en la siguiente imagen. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Vista de una vista de diagrama de la factoría de datos
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Diagrama** para ver la vista de diagrama de la factoría de datos. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Vista de los servicios vinculados de una factoría de datos
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Servicios vinculados** para ver todos los servicios vinculados en una lista. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Vista de detalles acerca de un servicio vinculado
En la hoja **SERVICIOS VINCULADOS**, haga clic en el servicio vinculado de la lista para ver detalles acerca de él. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Vista de conjuntos de datos de una factoría de datos 
En la hoja **FACTORÍA DE DATOS** de la factoría de datos, haga clic en el icono **Conjuntos de datos** para ver todas las tablas de la factoría de datos.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Vista de detalles acerca de un conjunto de datos
Haga clic en el conjunto de datos de la lista de conjuntos de datos en la hoja CONJUNTOS DE DATOS para ver detalles sobre el conjunto de datos. Tenga en cuenta que una tabla es un conjunto de datos rectangular que tiene un esquema. Es el único tipo de conjunto de datos que se admite en este momento. 

![Table Blade][image-data-factory-table]

En la hoja **TABLA** anterior, verá los **segmentos recientes** así como los **segmentos con problemas**. Haga clic en **... (puntos suspensivos)** para ver todos los segmentos. 

![All Slices of a Table][image-data-factory-all-slices]

En la hoja **Segmentos de datos**, puede usar un filtro para ver los segmentos específicos que desea revisar.


## <a name="DataFactorySlice"></a> Vista de detalles acerca de un sector
Haga clic en un segmento en la lista de segmentos en la hoja **TABLA** o la hoja **Segmentos de datos** para ver detalles sobre ese segmento. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Vista de todas las ejecuciones de actividad de un segmento
Para un segmento, puede haber más de una ejecución. Por ejemplo, cuando un segmento da error, el servicio puede realizar algunos reintentos. También puede volver a ejecutar un segmento que ha dado error en todos los reintentos. Puede ver todas las ejecuciones de actividad en la hoja** Segmento de datos** de la lista de la parte inferior. 

## <a name="DataFactoryActivtyRunDetails"></a>  Vista de detalles acerca de una ejecución de actividad
Haga clic en una ejecución de actividad de la lista de ejecuciones en la hoja **Segmento de datos** para ver los detalles sobre la ejecución de actividad. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Modos de operaciones: eventos de la semana pasada
En la hoja **FACTORÍA DE DATOS** (o la página principal) de la factoría de datos, haga clic en **Eventos de la pasada semana** en el modo **Operaciones** para ver los eventos de la semana pasada. Esto le ayuda a obtener una vista de alto nivel de las operaciones realizadas por la factoría de datos en la semana pasada. También le ayuda a solucionar los errores con el movimiento o procesamiento de los datos. 

![ Data Factory Events][image-data-factory-events]


## Otras referencias

Artículo | Descripción
------ | ---------------
[Supervisión y administración de la Factoría de datos de Azure mediante PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo supervisar una factoría de datos de Azure mediante cmdlets de PowerShell de Azure. 
[Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] | Este artículo incluye un tutorial que muestra cómo copiar datos de una base de datos SQL Server local en un blob de Azure.
[Uso de Pig y Hive con la factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo incluye un tutorial que muestra cómo usar la actividad de HDInsight para ejecutar un script de hive/pig con el fin de procesar datos de entrada para producir datos de salida. 
[Tutorial: Movimiento y procesamiento de los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un completo tutorial que muestra cómo implementar un escenario en tiempo real mediante el uso de la Factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo proporciona un tutorial con instrucciones paso a paso para la creación de una actividad personalizada y su uso en una canalización. 
[Solución de problemas de la factoría de datos][troubleshoot] | Este artículo describe cómo solucionar los problemas de la Factoría de datos de Azure.
[Referencia para desarrolladores de la Factoría de datos de Azure][developer-reference] | La referencia para desarrolladores incluye el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 
[Referencia de cmdlets de la Fábrica de datos de Azure][cmdlet-reference] | Este contenido de referencia incluye detalles acerca de todos los **cmdlets de la factoría de datos**.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

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
