<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Factoría de datos - Crear canalizaciones predictivas con la factoría de datos y el aprendizaje automático | Azure" description="Se describe cómo crear canalizaciones predictivas con la Factoría de datos de Azure y Aprendizaje automático de Azure" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Crear canalizaciones predictivas con la factoría de datos de Azure y el aprendizaje automático de Azure 
Puede operacionalizar modelos de [Aprendizaje automático de Azure][azure-machine-learning] dentro de las canalizaciones de la factoría de datos de Azure. Estas canalizaciones se denominan canalizaciones predictivas. Para crear una canalización predictiva, necesitará:

-	La clave de la API del modelo del área de trabajo publicada y la URL de puntuación del lote (vea la imagen siguiente)
-	Un almacenamiento de blobs de Azure que contenga el archivo CSV de entrada que se va puntuar.
-	Un almacenamiento de blobs de Azure que va a contener el archivo CSV de resultados de puntuación.

	![Machine Learning Dashboard][machine-learning-dashboard]

	La URL de puntuación de lote para el AzureMLLinkedService se obtiene como se indica en la imagen anterior, menos '**help**':  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Una **canalización predictiva** consta de estas partes:

-	Tablas de entrada y salida
-	Almacenamiento de Azure y servicios vinculados de ML de Azure
-	Una canalización con actividad de puntuación de lote de ML de Azure

## Ejemplo



1. Cree un servicio vinculado para su almacenamiento de Azure. Si los archivos de entrada y salida de puntuación van a estar en diferentes cuentas de almacenamiento, necesitará dos servicios vinculados. Este es un ejemplo de JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Cree las tablas de la factoría de datos de Azure de entrada y salida. Tenga en cuenta que a diferencia de otras tablas de la factoría de datos, estas deben contener tanto los valores **folderPath** como **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de "waitOnExternal" que se muestra en el ejemplo siguiente y su ScoringInputBlob sería la tabla de salida de una actividad diferente.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. En este ejemplo de salida se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin esto, la actividad sobrescribiría el archivo.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Cree un servicio de tipo vinculado: **AzureMLLinkedService**, proporcionando la clave de la API y la URL de la puntuación del lote de modelo.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Por último, cree una canalización que contenga una **AzureMLBatchScoringActivity**. Obtendrá la ubicación del archivo de entrada de las tablas de entrada, llamará a la API de puntuación de lote de AzureML y copiará la salida de la puntuación del lote en el blob proporcionado en la tabla de salida. A diferencia de otras actividades de la factoría de datos, AzureMLBatchScoringActivity solo puede tener una tabla de entrada y salida.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## Otras referencias

Artículo | Descripción
------ | ---------------
[Introducción a la factoría de datos de Azure][adf-introduction] | Este artículo proporciona información general del servicio de la factoría de datos de Azure y de los escenarios que admite.
[Introducción a la factoría de datos de Azure][adf-getstarted] | Este artículo ofrece un tutorial básico que proporciona instrucciones detalladas para crear y supervisar una factoría de datos de ejemplo.
[Habilitar las canalizaciones para trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial en el que se muestra cómo copiar datos de una base de datos de SQL Server local a un blob de Azure.
[Usar Pig y Hive con la factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo tiene un tutorial que muestra cómo utilizar la actividad de HDInsight para ejecutar un script de Hive y Pig para procesar los datos de entrada a fin de producir datos de salida. 
[Tutorial: Mover y procesar los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un tutorial completo en el que se muestra cómo implementar un escenario en tiempo real mediante el uso de la factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Usar actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones detalladas para crear una actividad personalizada y usarla en una canalización. 
[Solución de problemas de la factoría de datos][troubleshoot] | En este artículo se describe cómo solucionar problemas de la factoría de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminando la tabla de la Base de datos SQL de Azure). 
[Referencia del desarrollador de la factoría de datos de Azure][developer-reference] | La Referencia del desarrollador tiene el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/es-es/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
