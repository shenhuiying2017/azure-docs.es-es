---
title: 'Data Factory: registro de cambios de la API de .NET | Microsoft Docs'
description: "Describe los cambios importantes, las adiciones de características y las correcciones de errores, entre otros, en una versión específica de la API de .NET para Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 55a08d22c622c89b918d1bfadd0ce34b77c3d408
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory: registro de cambios de la API de .NET
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. 

En este artículo se proporciona información sobre los cambios realizados en el SDK de Azure Data Factory en una versión específica. El paquete NuGet más reciente para Azure Data Factory se encuentra [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versión 4.11.0
Incorporación de características:

* Se han agregado los siguientes tipos de servicios vinculados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Se han agregado los siguientes tipos de origen de copia:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versión 4.10.0
* Se han agregado las siguientes propiedades opcionales a TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Se han agregado los siguientes tipos de servicios vinculados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Se han agregado los siguientes tipos de origen de copia:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Se ha agregado la propiedad [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity
  * Se ha habilitado el paso de varias entradas de servicio web a un experimento de Azure Machine Learning

## <a name="version-491"></a>Versión 4.9.1
### <a name="bug-fix"></a>Corrección de errores
* La autenticación basada en WebApi ya no se utiliza con [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versión 4.9.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las propiedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) y [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Para más información, consulte [Copias almacenadas provisionalmente](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Corrección de errores
* Se ha incorporado una sobrecarga del método [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx), que toma una instancia de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
* [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) y [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) se han marcado como opcionales en CopySink.

## <a name="version-480"></a>Versión 4.8.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las siguientes propiedades opcionales al tipo de actividad de copia para habilitar la optimización del rendimiento de copia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versión 4.7.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado los nuevos tipos StorageFormat y [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar archivos en el formato Optimized Row Columnar (ORC).
* Se han agregado las propiedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) y PolyBaseSettings a SqlDWSink.
  * Habilita el uso de PolyBase para copiar datos de SQL Data Warehouse.

## <a name="version-461"></a>Versión 4.6.1
### <a name="bug-fixes"></a>Corrección de errores
* Corrige la solicitud HTTP para enumerar las ventanas de la actividad.
  * Quita el nombre del grupo de recursos y el nombre de la factoría de datos de la carga de la solicitud.

## <a name="version-460"></a>Versión 4.6.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado las siguientes propiedades a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjuntos de datos](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Se han agregado las siguientes propiedades a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Se han agregado los nuevos tipos [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) y [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de datos cuyos datos están en formato JSON.

## <a name="version-450"></a>Versión 4.5.0
### <a name="feature-additions"></a>Incorporación de características
* Se han agregado [operaciones de lista para la ventana de actividad](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Se agregaron métodos para recuperar ventanas de la actividad con filtros basados en los tipos de entidad (es decir, fábricas de datos, conjuntos de datos, canalizaciones y actividades).
* Se han agregado los siguientes tipos de servicios vinculados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx) y [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx) y [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Se han agregado los siguientes tipos de origen de copia:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versión 4.4.0
### <a name="feature-additions"></a>Incorporación de características
* El siguiente tipo de servicio vinculado se ha agregado como orígenes de datos y receptores para actividades de copia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Servicio vinculado de SAS de Azure Storage](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Versión 4.3.0
### <a name="feature-additions"></a>Incorporación de características
* Los siguientes tipos de servicio vinculado se ha agregado como orígenes de datos para actividades de copia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde HDFS local mediante Azure Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Para más información sobre conceptos y ejemplos, consulte [Movimiento de datos desde almacenes de datos ODBC mediante Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>Versión 4.2.0
### <a name="feature-additions"></a>Incorporación de características
* Se ha agregado el nuevo tipo de actividad siguiente: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para más información acerca de la actividad, consulte [Actualización de modelos mediante la actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md).
* Se ha agregado una nueva propiedad opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a la [clase AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* Se han agregado las propiedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) y [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) a la clase [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
* Permita la configuración de los tiempos de espera para las llamadas de cliente al servicio de Factoría de datos.

## <a name="version-410"></a>Versión 4.1.0
### <a name="feature-additions"></a>Incorporación de características
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

## <a name="version-401"></a>Versión 4.0.1
### <a name="breaking-changes"></a>Cambios drásticos
Las siguientes clases se han cambiado de nombre. Los nuevos nombres eran los nombres de clases originales antes de la versión 4.0.0.

| Nombre en 4.0.0 | Nombre en 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versión 4.0.0
### <a name="breaking-changes"></a>Cambios drásticos
* Las siguientes clases o interfaces se han cambiado de nombre.

| Nombre anterior | Nombre nuevo |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabla |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Los métodos **List** devuelven ahora resultados paginados. Si la respuesta contiene una propiedad **NextLink** que no está vacía, la aplicación cliente debe seguir capturando la página siguiente hasta que se devuelvan todas las páginas.  Este es un ejemplo:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** devuelve solamente el resumen de una canalización, en lugar de todos los detalles. Por ejemplo, en un resumen de canalización, las actividades solo contienen el nombre y el tipo.

### <a name="feature-additions"></a>Incorporación de características
* La clase [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) admite dos nuevas propiedades, **SliceIdentifierColumnName** y **SqlWriterCleanupScript**, para permitir la copia idempotente en Azure SQL Data Warehouse. Consulte el artículo sobre [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) para obtener más información acerca de estas propiedades.
* Actualmente, se admite la ejecución de procedimientos almacenados en orígenes de Azure SQL Database y Azure SQL Data Warehouse como parte de la actividad de copia. Para ello, las clases [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) y [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) tienen las siguientes propiedades: **SqlReaderStoredProcedureName** y **StoredProcedureParameters**. Para más información acerca de estas propiedades, consulte los artículos acerca de [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) y [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) en Azure.com.  
