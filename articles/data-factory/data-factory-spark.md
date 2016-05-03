<properties 
	pageTitle="Invocar programas Spark desde Data Factory de Azure" 
	description="Obtenga información sobre cómo invocar programas Spark desde Data Factory de Azure mediante la actividad MapReduce." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Invocar programas Spark desde Data Factory
## Introducción
Puede usar la actividad MapReduce en una canalización de Data Factory para ejecutar programas Spark en su clúster de HDInsight Spark. Consulte el artículo [Actividad MapReduce](data-factory-map-reduce.md) para obtener información detallada sobre el uso de la actividad antes de leer este artículo.

## Ejemplo de Spark en GitHub
[Spark - Data Factory sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) (Spark: ejemplo de Data Factory en GitHub) muestra cómo usar la actividad MapReduce para invocar un programa Spark. El programa Spark se limita a copiar datos de un contenedor de blobs de Azure a otro.

## Entidades de Factoría de datos
La carpeta **Spark-ADF/src/ADFJsons** contiene archivos para entidades de Data Factory (servicios vinculados, conjuntos de datos, canalización).

Hay dos **servicios vinculados** en este ejemplo: Almacenamiento de Azure y Azure HDInsight. Debe especificar sus valores clave y nombre de Almacenamiento de Azure en **StorageLinkedService.json** y clusterUri, userName y contraseña en **HDInsightLinkedService.json**.

Hay dos **conjuntos de datos** en este ejemplo: **input.json** y **output.json**. Estos archivos se encuentran en la carpeta **Conjuntos de datos**. Estos archivos representan conjuntos de datos de entrada y salida para la actividad MapReduce

Hay una **canalización** en el ejemplo de la carpeta **ADFJsons/Pipeline**. Esta es la entidad más importante que debe revisar para saber cómo invocar un programa Spark mediante la actividad MapReduce.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

Como puede ver, la actividad MapReduce está configurada para invocar **spark-adf-job-bin.jar** en el contenedor **libs** de su Almacenamiento de Azure (especificado en StorageLinkedService.json). El código fuente para este programa está en la carpeta Spark-ADF/src/main/java/com/adf/spark, llama a spark-submit y ejecuta trabajos Spark.

Este programa MapReduce (spark adf-job-bin.jar) que se ejecuta en su clúster de HDInsight Spark invoca un programa Spark **sparkdemoapp\_2.10-1.0.jar** y pasa los argumentos que recibe a través de la actividad MapReduce (que se muestra en JSON anterior) al programa Spark. **sparkdemoapp\_2.10-1.0.jar** contiene código fuente de Scala que copia datos de un contenedor de blobs de Azure a otro. Puede reemplazar esta aplicación de demostración jar por cualquier otra que contenga cualquier trabajo que intente ejecutar mediante Spark.

Resumiendo, la **actividad MapReduce** invoca el programa MapReduce **spark-adf-job-bin.jar** que invoca el programa Spark **sparkdemoapp\_2.10-1.0.jar**. Para ejecutar su propio programa Spark, reemplace sparkdemoapp\_2.10-1.0.jar por el suyo.

> [AZURE.NOTE] Tiene que usar su propio clúster de HDInsight Spark con este enfoque para invocar programas Spark mediante la actividad MapReduce. No se admite el uso de un clúster de HDInsight a petición.


## Otras referencias
- [Actividad de Hive](data-factory-hive-activity.md)
- [Actividad de Pig](data-factory-pig-activity.md)
- [Actividad MapReduce](data-factory-map-reduce.md)
- [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->