# Información general
## [Información general de Azure Data Lake Store](data-lake-store-overview.md)
## [Comparación de Azure Data Lake Store con Azure Storage](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store para el procesamiento de macrodatos](data-lake-store-data-scenarios.md)
## [Aplicaciones de código abierto que funcionan con Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

# Introducción
## [Uso del portal](data-lake-store-get-started-portal.md)
## [Uso de PowerShell](data-lake-store-get-started-powershell.md)
## [Uso de la CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)


# Procedimientos
## Carga y movimiento de datos
### [Uso de Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Uso de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Uso de DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Uso de Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Carga de datos de orígenes sin conexión](data-lake-store-offline-bulk-data-upload.md)
### [Migración de Azure Data Lake Store entre regiones](data-lake-store-migration-cross-region.md)

## Protección de datos
### [Introducción a la seguridad](data-lake-store-security-overview.md)
### [Control de acceso en Data Lake Store](data-lake-store-access-control.md)
### [Protección de los datos en Data Lake Store](data-lake-store-secure-data.md)
### [Cifrado](data-lake-store-encryption.md)

## Autenticación con Data Lake Store
### [Opciones de autenticación](data-lakes-store-authentication-using-azure-active-directory.md)
### [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Uso de Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Uso del SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Uso de la API de REST](data-lake-store-end-user-authenticate-rest-api.md)
#### [Uso de Python](data-lake-store-end-user-authenticate-python.md)
### [Autenticación entre servicios](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Uso del SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)

## Trabajo con Data Lake Store
### Operaciones de administración de cuenta
#### [Uso de .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [Uso de la API de REST](data-lake-store-get-started-rest-api.md)
#### [Uso de Python](data-lake-store-get-started-python.md)
### Operaciones del sistema de archivos
#### [Uso del SDK de .NET](data-lake-store-data-operations-net-sdk.md)
#### [Uso de SDK de Java](data-lake-store-get-started-java-sdk.md)
#### [Uso de la API de REST](data-lake-store-data-operations-rest-api.md)
#### [Uso de Python](data-lake-store-data-operations-python.md)

## Rendimiento
### [Guía para la optimización del rendimiento de Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
### [Guía sobre la optimización del rendimiento para poder usar PowerShell con Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
### [Guía para la optimización del rendimiento de Spark en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
### [Guía para la optimización del rendimiento de Hive en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
### [Guía para la optimización del rendimiento de MapReduce en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-mapreduce.md)
### [Guía para la optimización del rendimiento de Storm en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-storm.md)

## Integración con servicios de Azure
### Con HDInsight
#### [Uso de Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Uso de Azure PowerShell (almacenamiento predeterminado)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Uso de Azure PowerShell (almacenamiento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Uso de la plantilla de Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Acceso desde máquinas virtuales en redes virtuales de Azure](data-lake-store-connectivity-from-vnets.md)
### [Uso con Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Uso con Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)
### [Uso con Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Uso con Stream Analytics](data-lake-store-stream-analytics.md)
### [Uso con Power BI](data-lake-store-power-bi.md)
### [Uso con Data Catalog](data-lake-store-with-data-catalog.md)
### [Uso con PolyBase en SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Uso de SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Más opciones para la integración de Azure](data-lake-store-integrate-with-other-services.md)

## administración
### [Acceso a los registros de diagnóstico](data-lake-store-diagnostic-logs.md)
### [Planeamiento para lograr alta disponibilidad](data-lake-store-disaster-recovery-guidance.md)

# Referencia
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (administración de cuentas)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (administración de sistemas de archivos)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [CLI de Azure](https://docs.microsoft.com/cli/azure/dls)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/)
## [Blog de Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Comentarios sobre UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Precios](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Foro de Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
