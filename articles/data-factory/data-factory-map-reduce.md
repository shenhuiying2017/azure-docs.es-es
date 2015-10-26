<properties 
	pageTitle="Invocar programa MapReduce desde la factoría de datos de Azure" 
	description="Obtenga información sobre cómo procesar datos mediante la ejecución de programas MapReduce en un clúster de HDInsight de Azure desde una factoría de datos de Azure." 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Invocar programas MapReduce desde la factoría de datos de Azure
En este artículo se describe cómo invocar un programa **MapReduce** desde una canalización de la factoría de datos de Azure mediante el uso de la **actividad MapReduce de HDInsight**.

## Introducción 
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe el uso de la transformación de MapReduce de la actividad de HDInsight.
 
Vea el artículo [Pig](data-factory-pig-activity) y [Hive](data-factory-hive-activity.md) para obtener detalles acerca la ejecución de scripts de Pig/Hive en un clúster de HDInsight desde una canalización de la factoría de datos de Azure mediante transformaciones de Pig/Hive de la actividad de HDInsight.

## JSON para actividades de HDInsight mediante la transformación de MapReduce 

En la definición de JSON para la actividad de HDInsight:
 
1. Establezca el **tipo** de la **actividad** en **HDInsight**.
3. Especifique el nombre de la clase para la propiedad **className**.
4. Especifique la ruta de acceso al archivo JAR incluyendo el nombre de archivo de la propiedad **jarFilePath**.
5. Especifique el servicio vinculado que hace referencia al almacenamiento de blobs de Azure que contiene el archivo JAR de la propiedad **jarLinkedService**.   
6. Especifique los argumentos para el programa de MapReduce en la sección **argumentos**. En tiempo de ejecución, verá unos argumentos adicionales (por ejemplo, mapreduce.job.tags) desde el marco de trabajo MapReduce. Para diferenciar sus argumentos con los argumentos de MapReduce, considere el uso tanto de opción como valor como argumentos tal como se muestra en el siguiente ejemplo (-s, --input, --output etc... son opciones seguidas inmediatamente por sus valores).

 

		{
		  "name": "MahoutMapReduceSamplePipeline",
		  "properties": {
		    "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		    "activities": [
		      {
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

Puede utilizar la transformación de MapReduce para ejecutar cualquier archivo jar de MapReduce en un clúster de HDInsight. En la siguiente definición de JSON de ejemplo de una canalización, la actividad de HDInsight se configura para ejecutar un archivo JAR de Mahout.

## Muestra
Puede descargar un ejemplo para usar la actividad de HDInsight con la transformación de MapReduce desde: [Ejemplos de la factoría de datos en GitHub](data-factory-samples.md).


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=Oct15_HO3-->