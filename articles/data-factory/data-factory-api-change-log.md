<properties 
	pageTitle="Factoría de datos - Registro de cambios de la API de .NET | Microsoft Azure" 
	description="Describe los cambios importantes, las adiciones de características y las correcciones de errores, entre otros, en una versión específica de la API de .NET para Factoría de datos de Azure." 
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
	ms.date="07/05/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: registro de cambios de la API de .NET 
En este artículo se proporciona información sobre los cambios realizados en el SDK de Factoría de datos de Azure en una versión específica. El paquete NuGet más reciente para la Factoría de datos de Azure se encuentra [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

## Versión 4.9.1
_Fecha de lanzamiento: 2016.07.05_

### Corrección de errores

- La autenticación basada en WebApi ya no se utiliza con [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## Versión 4.9.0
_Fecha de lanzamiento: 2016.06.10_

### Incorporación de características

- Se han agregado las propiedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) y [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Para más información, consulte [Copias almacenadas provisionalmente](data-factory-copy-activity-performance.md#staged-copy).


### Corrección de errores

- Se ha incorporado una sobrecarga del método [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) que toma una instancia de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
- [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) y [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) se han marcado como opcionales en CopySink.

## Versión 4.8.0
_Fecha de lanzamiento: 2016.05.25_

### Incorporación de características
- Se han agregado las siguientes propiedades opcionales al tipo de actividad de copia para habilitar la optimización del rendimiento de copia:
	- [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
	- [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## Versión 4.7.0
Fecha de lanzamiento: 20-05-2016

### Incorporación de características
* Se han agregado los nuevos tipos StorageFormat y [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar archivos en el formato Optimized Row Columnar (ORC).
* Se han agregado las propiedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) y PolyBaseSettings a SqlDWSink.
    * Habilita el uso de PolyBase para copiar datos del Almacenamiento de datos SQL.

## Versión 4.6.1
Fecha de lanzamiento: 26-04-2016

### Corrección de errores
* Corrige la solicitud HTTP para enumerar las ventanas de la actividad.
    * Quita el nombre del grupo de recursos y el nombre de la factoría de datos de la carga de la solicitud.

## Versión 4.6.0
Fecha de lanzamiento: 14/04/2016

### Incorporación de características

- Se han agregado las siguientes propiedades a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
	- [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
	- [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
	- [Conjuntos de datos](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Se han agregado las siguientes propiedades a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
	- [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Se han agregado los nuevos tipos [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) y [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de datos cuyos datos están en formato JSON.

## Versión 4.5.0
Fecha de lanzamiento: 24/02/2016

### Incorporación de características
* Se han agregado [operaciones de lista para la ventana de actividad](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Se agregaron métodos para recuperar ventanas de la actividad con filtros basados en los tipos de entidad (es decir, fábricas de datos, conjuntos de datos, canalizaciones y actividades).
* Se han agregado los siguientes tipos de servicios vinculados:
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx) y [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos:
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx) y [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Se han agregado los siguientes tipos de origen de copia:
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## Versión 4.4.0
Fecha de lanzamiento: 28.01.2016

### Incorporación de características

- El siguiente tipo de servicio vinculado se ha agregado como orígenes de datos y receptores para actividades de copia:
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Servicio vinculado de SAS de Almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service).

## Versión 4.3.0
Fecha de lanzamiento: 25.11.2015

### Incorporación de características

- Los siguientes tipos de servicio vinculado se ha agregado como orígenes de datos para actividades de copia:
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde HDFS local mediante Azure Data Factory](data-factory-hdfs-connector.md).
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde almacenes de datos ODBC mediante Data Factory de Azure](data-factory-odbc-connector.md).

## Versión 4.2.0
Fecha de lanzamiento: 10-11-2015

### Incorporación de características

- Se ha agregado el nuevo tipo de actividad siguiente: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obtener más información acerca de la actividad, consulte [Actualización de modelos de Aprendizaje automático de Azure mediante la actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Se ha agregado una nueva propiedad opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a la [clase AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
- Se han agregado las propiedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) y [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) a la clase [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
- Permita la configuración de los tiempos de espera para las llamadas de cliente al servicio de Factoría de datos.


## Versión 4.1.0
Fecha de lanzamiento: 28-10-2015

### Incorporación de características
* Se han agregado los siguientes tipos de servicios vinculados:
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Se han agregado los siguientes tipos de actividades:
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos:
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Se han agregado los siguientes tipos de origen y el receptor para la actividad de copia:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## Versión 4.0.1
Fecha de lanzamiento: 13/10/2015

### Cambios drásticos
Las clases siguientes se han cambiado de nombre. Los nombres nuevos eran los nombres originales de las clases antes de la versión 4.0.0.
 
Nombre en 4.0.0 | Nombre en 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Versión 4.0.0
Fecha de lanzamiento: 02/10/2015

### Cambios drásticos



- Las siguientes clases o interfaces se han cambiado de nombre.

| Nombre anterior | Nombre nuevo |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabla | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- La **versión de API** para esta versión es: **2015-10-01**.

- Los métodos **List** devuelven ahora resultados paginados. Si la respuesta contiene una propiedad **NextLink** que no está vacía, la aplicación cliente debe seguir capturando la página siguiente hasta que se devuelvan todas las páginas. Este es un ejemplo:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- La API de canalización de **List** devuelve solamente el resumen de una canalización, en lugar de todos los detalles. Por ejemplo, en un resumen de canalización, las actividades solo contienen el nombre y el tipo.

### Incorporación de características
- La clase [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) admite dos nuevas propiedades, **SliceIdentifierColumnName** y **SqlWriterCleanupScript**, para permitir la copia idempotente en el Almacenamiento de datos SQL de Azure. Consulte el artículo de [Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md), concretamente las secciones [Mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) y [Mecanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2), para obtener más detalles sobre estas propiedades.

- Actualmente, se admite la ejecución de procedimientos almacenados en orígenes de Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure como parte de la actividad de copia. Para ello, las clases [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) y [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) tienen las siguientes propiedades: **SqlReaderStoredProcedureName** y **StoredProcedureParameters**. Consulte los artículos [Base de datos SQL de Azure](data-factory-azure-sql-connector.md#sqlsource) y [Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) en Azure.com para obtener detalles sobre estas propiedades.

<!---HONumber=AcomDC_0720_2016-->