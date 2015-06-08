<properties 
	pageTitle="Invocar programa MapReduce desde la factoría de datos de Azure" 
	description="Obtenga información acerca de cómo procesar datos mediante la ejecución de programas de MapReduce en un clúster de HDInsight de Azure de un generador de datos de Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Invocar programas MapReduce desde la factoría de datos de Azure
Este artículo describe cómo invocar un **MapReduce** programa desde una canalización del generador de datos de Azure mediante el uso de la **HDInsight actividad** con **MapReduce transformación**.

## Introducción 
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe el uso de la transformación de MapReduce de la actividad de HDInsight.
 
Consulte [usar Pig y Hive con el generador de datos][data-factory-pig-hive-activities] para obtener más información acerca de cómo ejecutar Pig y Hive scripts en HDInsight de un clúster de una canalización del generador de datos de Azure mediante Pig y Hive transformaciones de la actividad de HDInsight.

## JSON para actividades de HDInsight mediante la transformación de MapReduce 

En la definición de JSON para la actividad de HDInsight:
 
1. Establecer el **tipo** de la **actividad** a **HDInsightActivity**.
2. Establecer el **tipo** de la **transformación** a **MapReduce**.
3. Especifique el nombre de la clase para **className** propiedad.
4. Especifique la ruta de acceso al archivo JAR incluyendo el nombre de archivo **jarFilePath** propiedad.
5. Especificar el servicio vinculado que hace referencia al almacenamiento de blobs de Azure que contiene el archivo JAR de **jarLinkedService** propiedad.   
6. Especifique los argumentos para el programa de MapReduce en el **argumentos** sección. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

Puede utilizar la transformación de MapReduce para ejecutar cualquier archivo jar de MapReduce en un clúster de HDInsight. En la siguiente definición de JSON de ejemplo de una canalización, la actividad de HDInsight se configura para ejecutar un archivo JAR de Mahout.

## Muestra
Puede descargar un ejemplo para usar la actividad de HDInsight con la transformación de MapReduce desde: [ejemplos de generador de datos en GitHub][data-factory-samples].

## Otras referencias

Artículo | Descripción
------ | ---------------
[Tutorial: Mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial] | Este artículo proporciona un tutorial de end-to-end que muestra cómo implementar un verdadero casi escenario mediante el generador de datos de Azure para transformar los datos de archivos de registro en perspectivas. En este tutorial, usará tanto Pig y Hive transformaciones para procesar los datos. 
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de cmdlets, script JSON, funciones, etc.... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->