Azure Data Factory admite las siguientes actividades de transformación que se pueden agregar a las canalizaciones tanto individualmente como encadenadas a otra actividad.

| Actividad de transformación de datos | Entorno de procesos |
|:--- |:--- |
| [Hive](../articles/data-factory/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/data-factory-spark.md) | HDInsight [Hadoop] |
| [Actividades de Machine Learning: ejecución de Batch y recurso de actualización](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) |MV de Azure |
| [Procedimiento almacenado](../articles/data-factory/data-factory-stored-proc-activity.md) |SQL Azure, Almacenamiento de datos SQL de Azure o SQL Server |
| [U-SQL de análisis con Data Lake](../articles/data-factory/data-factory-usql-activity.md) |Análisis con Azure Data Lake |
| [DotNet](../articles/data-factory/data-factory-use-custom-activities.md) |HDInsight [Hadoop] o Lote de Azure |

> [!NOTE]
> Puede usar la actividad MapReduce para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte [Invocar programas Spark desde Data Factory de Azure](../articles/data-factory/data-factory-spark.md) para obtener más información.
> Puede crear una actividad personalizada para ejecutar scripts de R en su clúster de HDInsight con R instalado. Consulte [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Ejecutar script de R con Data Factory de Azure).
> 
> 

