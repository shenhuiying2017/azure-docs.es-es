<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Invocar programa MapReduce desde la factoría de datos de Azure" description="Obtenga información acerca de cómo procesar datos mediante la ejecución de programas MapReduce en un clúster de HDInsight de Azure desde una factoría de datos de Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Invocar programas MapReduce desde la factoría de datos de Azure
En este artículo se describe cómo invocar un **MapReduce** desde una canalización de la factoría de datos de Azure mediante el uso de la **actividad de HDInsight** con la **transformación de MapReduce**. 

## Introducción 
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. 

- Una **actividad de copia** copia datos de un almacenamiento de origen a un almacenamiento de destino. Para obtener más información acerca de la actividad de copia, vea [Copiar datos con la factoría de datos][data-factory-copy-activity]. 
- La **Actividad de HDInsight** procesa datos mediante la ejecución de scripts de Hive o Pig o programas MapReduce en un clúster de HDInsight. La actividad de HDInsight admite tres transformaciones: **Hive**, **Pig** y **MapReduce**. La actividad de HDInsight puede consumir una o más entradas y producir una o más salidas.
 
Vea [Uso de Pig y Hive con la Factoría de datos][data-factory-pig-hive-activities] para obtener detalles acerca la ejecución de scripts de Pig/Hive en un clúster de HDInsight desde una canalización de la factoría de datos de Azure mediante transformaciones de Pig/Hive de la actividad de HDInsight. En este artículo se describe el uso de la transformación de MapReduce de la actividad de HDInsight.

## JSON de canalización
En el archivo JSON para una canalización:
 
1. Establezca el **tipo** de la **actividad** en **HDInsightActivity**.
2. Establezca el **tipo** de la **transformación** en **MapReduce**.
3. Especifique el nombre de la clase para la propiedad **className**.
4. Especifique la ruta de acceso al archivo JAR incluyendo el nombre de archivo de la propiedad **jarFilePath**.
5. Especifique el servicio vinculado que hace referencia al almacenamiento de blobs de Azure que contiene el archivo JAR de la propiedad **jarLinkedService**.   
6. Especifique los argumentos para el programa de MapReduce en la sección **argumentos**. 

Puede utilizar la transformación de MapReduce para ejecutar cualquier archivo jar de MapReduce en un clúster de HDInsight. En la siguiente definición de JSON de ejemplo de una canalización, la actividad de HDInsight se configura para ejecutar un archivo JAR de Mahout.   
 

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

## Muestra
Puede descargar un ejemplo para usar la actividad de HDInsight con la transformación de MapReduce desde: [Ejemplos de la factoría de datos en GitHub][data-factory-samples].  

## Otras referencias

Artículo | Descripción
------ | ---------------
[Introducción a la Factoría de datos de Azure][data-factory-introduction] | Este artículo presenta el servicio Factoría de datos de Azure, los conceptos, el valor que proporciona y los escenarios que admite.
[Introducción a la Factoría de datos de Azure][adf-getstarted] | Este artículo proporciona un tutorial completo que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.
[Habilitar las canalizaciones para trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial en el que se muestra cómo copiar datos de una base de datos de SQL Server local a un blob de Azure.
[Tutorial: Mover y procesar los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un tutorial completo en el que se muestra cómo implementar un escenario en tiempo real mediante el uso de la factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Usar actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones detalladas para crear una actividad personalizada y usarla en una canalización. 
[Solución de problemas de la factoría de datos][troubleshoot] | En este artículo se describe cómo solucionar problemas de la factoría de datos de Azure.  
[Referencia del desarrollador de la factoría de datos de Azure][developer-reference] | La Referencia del desarrollador tiene el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
