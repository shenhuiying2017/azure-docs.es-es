<properties 
	pageTitle="Creación de canalizaciones predictivas con la actividad Ejecución de lotes de Aprendizaje automático de Azure | Microsoft Azure" 
	description="Describe cómo crear canalizaciones predictivas con Factoría de datos de Azure y Aprendizaje automático de Azure" 
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
	ms.date="08/24/2015" 
	ms.author="spelluru"/>

# Creación de canalizaciones predictivas con la actividad Ejecución de lotes de Aprendizaje automático de Azure   
## Información general

> [AZURE.NOTE]Consulte los artículos [Introducción a la Factoría de datos de Azure  
](data-factory-introduction.md) y [Creación de su primera canalización](data-factory-build-your-first-pipeline.md) para empezar a trabajar rápidamente con el servicio Factoría de datos de Azure.

Factoría de datos de Azure permite crear fácilmente canalizaciones que aprovechan un servicio web de [Aprendizaje automático de Azure][azure-machine-learning] publicado para realizar análisis predictivos. Con Factoría de datos de Azure puede hacer uso de las canalizaciones de macrodatos (por ejemplo, Pig y Hive) para procesar los datos que se han introducido desde diversos orígenes, y usar los servicios web de Aprendizaje automático de Azure para realizar predicciones sobre los datos del lote.

Factoría de datos de Azure se usa para coordinar el procesamiento y el movimiento de datos y, posteriormente, realizar la ejecución por lotes mediante Aprendizaje automático de Azure. Para ello, deberá hacer lo siguiente:

1. Crear un servicio vinculado de Aprendizaje automático de Azure. Necesitará lo siguiente:
	1. **URI de solicitud** para la API Ejecución de lotes. Para encontrar el URI de solicitud, haga clic en el vínculo **EJECUCIÓN DE LOTES** en la página de servicios web (que se muestra a continuación).
	1. **Clave de API** para el servicio web de Aprendizaje automático de Azure publicado. Para encontrar la clave de API, haga clic en el servicio web que ha publicado. 
 2. Use la actividad **AzureMLBatchExecution**.

	![Panel de aprendizaje automático](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![URI del lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Escenario: Experimentos mediante entradas y salidas de servicios web que hacen referencia a datos de Almacenamiento de blobs de Azure
En este escenario, el servicio web de Aprendizaje automático de Azure realiza predicciones mediante datos de un archivo de un almacenamiento de blobs de Azure y almacena los resultados de predicción en el almacenamiento de blobs. El siguiente JSON define una canalización de Factoría de datos de Azure con una actividad AzureMLBatchExecution. La actividad tiene el conjunto de datos **DecisionTreeInputBlob** como entrada y **DecisionTreeResultBlob** como salida. **DecisionTreeInputBlob** se pasa como entrada al servicio web mediante la propiedad JSON **webServiceInput** y **DecisionTreeResultBlob** se pasa como salida al servicio web mediante la propiedad JSON **webServiceOutputs**. Solo los conjuntos de datos que son entradas y salidas de la actividad pueden pasarse como salidas y entradas de servicio web.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]Solo las entradas y salidas de la actividad AzureMLBatchExecution pueden pasarse como parámetros al servicio web. Por ejemplo, en el fragmento JSON anterior, DecisionTreeInputBlob es una entrada a la actividad AzureMLBatchExecution, que se pasa como entrada al servicio web mediante el parámetro webServiceInput.

### Ejemplo

Este ejemplo utiliza Almacenamiento de Azure para almacenar los datos de entrada y salida.

Antes de continuar con este ejemplo y usar el Editor de Factoría de datos para crear artefactos de Factoría de datos (servicios vinculados, conjuntos de datos, canalizaciones), se recomienda que siga el tutorial [Introducción a la Factoría de datos de Azure][adf-build-1st-pipeline].
 

1. Cree un **servicio vinculado** para su **Almacenamiento de Azure**. Si los archivos de entrada y salida van a estar en diferentes cuentas de almacenamiento, necesitará dos servicios vinculados. Este es un ejemplo de JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Cree el **conjunto de datos** de **entrada** de Factoría de datos de Azure. Tenga en cuenta que a diferencia de otros conjuntos de datos de Factoría de datos, estos deben contener tanto los valores **folderPath** como **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de **external** y **externalData** que se muestra en el ejemplo siguiente y su DecisionTreeInputBlob sería el conjunto de datos de salida de una actividad diferente.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	Su archivo CSV de entrada debe tener la fila de encabezado de columna. Si está usando la **copia de actividad** para crear/mover el CSV en el Almacenamiento de blog, debe establecer la propiedad de receptor **blobWriterAddHeader** en **true**. Por ejemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si el archivo CSV no tiene la fila de encabezado, es posible que vea el siguiente error: **Error en actividad: error de lectura de la cadena. Token inesperado: StartObject. Path '', line 1, position 1**.
3. Cree el **conjunto de datos** de **salida** de Factoría de datos de Azure. En este ejemplo se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin esto, la actividad sobrescribiría el archivo.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Cree un **servicio vinculado** de tipo **AzureMLLinkedService**; para ello, proporcione la clave de API y la URL de ejecución de lotes.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Por último, cree una canalización que contenga una actividad **AzureMLBatchExecution**. Obtendrá la ubicación del archivo de entrada de los conjuntos de datos de entrada, llamará a la API de ejecución de lotes de Aprendizaje automático de Azure y copiará la salida de ejecución de lotes en el blob proporcionado en el conjunto de datos de salida. 

	> [AZURE.NOTE]La actividad AzureMLBatchExecution puede tener cero o más entradas y una o más salidas.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Las fechas y horas de **inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE]La especificación de la entrada para la actividad AzureMLBatchExecution es opcional.

## Escenario: Experimentos mediante módulos Lector y Escritor para hacer referencia a datos de varios almacenamientos

Otro escenario común al crear experimentos de Aprendizaje automático de Azure es usar módulos Lector y Escritor. El módulo lector se usa para cargar datos en un experimento y el módulo escritor para guardar los datos de los experimentos. Para obtener información sobre los módulos lector y escritor, consulte los temas [Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library.

Al usar los módulos lector y escritor, es recomendable emplear un parámetro de servicio web para cada propiedad de estos módulos. Estos parámetros web permiten configurar los valores en tiempo de ejecución. Por ejemplo, podría crear un experimento con un módulo lector que usa una base de datos SQL de Azure: XXX.database.windows.net. Una vez implementado el servicio web, quiere habilitar los consumidores del servicio web con el fin de especificar otro servidor Azure SQL Server denominado YYY.database.windows.net. Puede usar un parámetro de servicio web para permitir que se configure este valor.

> [AZURE.NOTE]Las entradas y salidas de servicio web son diferentes de los parámetros de servicio web. En el primer escenario, ha visto cómo pueden especificarse una entrada y una salida para un servicio web de Aprendizaje automático de Azure. En este escenario, pasará parámetros para un servicio web que corresponden a las propiedades de los módulos lector y escritor.

Echemos un vistazo a un escenario de uso de parámetros de servicio web. Tiene un servicio web implementado de Aprendizaje automático de Azure que usa un módulo lector para leer datos de uno de los orígenes de datos de Aprendizaje automático de Azure admitidos (por ejemplo: Base de datos SQL de Azure). Después de realizar la ejecución de lotes, los resultados se escriben con un módulo escritor (Base de datos SQL de Azure). No hay entradas ni salidas de servicio web definidas en los experimentos. En este caso, se recomienda que configure los parámetros de servicio web relevantes para los módulos lector y escritor. De esta forma, se podrán configurar los módulos lector y escritor cuando se use la actividad AzureMLBatchExecution. Los parámetros de servicio web se especifican en la sección **globalParameters** de la actividad JSON como se indica a continuación.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

También puede usar [Funciones de Factoría de datos](https://msdn.microsoft.com/library/dn835056.aspx) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.

### Uso de un módulo lector para leer datos de varios archivos de blob de Azure
Las canalizaciones de macrodatos (Pig, Hive, etc...) pueden generar uno o más archivos de salida sin extensiones. Por ejemplo, cuando se especifica una tabla externa de Hive, los datos de dicha tabla se pueden almacenar en el almacenamiento de blobs de Azure con el siguiente nombre 000000\_0. Puede usar el módulo lector en un experimento para leer varios archivos y usarlos para realizar predicciones.

Al usar el módulo lector en un experimento de Aprendizaje automático de Azure, puede especificar Blob de Azure como entrada. Los archivos en el almacenamiento de blobs de Azure pueden ser los archivos de salida (por ejemplo, 000000\_0) que se generan mediante un script de Pig y Hive en HDInsight. El módulo lector permite leer archivos (sin extensiones) mediante la configuración de la propiedad **Ruta al contenedor, directorio o blob** de dicho módulo para que señale el contenedor o la carpeta que contiene los archivos, como se muestra a continuación. Tenga en cuenta que el asterisco (*) **especifica que todos los archivos de la carpeta o contenedor (es decir, data/aggregateddata/year=2014/month-6/*)** se leerán como parte del experimento.

![Propiedades de Blob de Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Ejemplo 
#### Canalización con la actividad AzureMLBatchExecution con parámetros de servicio web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
En el ejemplo JSON anterior:

- El servicio web implementado de Aprendizaje automático de Azure usa un módulo lector y otro escritor para leer y escribir datos desde y hacia una base de datos SQL de Azure. Este servicio web expone los cuatro parámetros siguientes: nombre de servidor de base de datos, nombre de base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor.  
- Las fechas y horas de **inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).
 

## Preguntas más frecuentes

**P:** Estoy usando la actividad AzureMLBatchScoring. ¿Debo cambiar y usar la actividad AzureMLBatchExecution?

**R:** Sí. Si va a usar la actividad AzureMLBatchScoring para integrarse con Aprendizaje automático de Azure, se recomienda que use la última actividad AzureMLBatchExecution. Estamos en proceso de suspender la actividad AzureMLBatchScoring y se eliminará en una versión futura.

La actividad AzureMLBatchExecution se introdujo en la versión de agosto de 2015 del SDK de Azure y Azure PowerShell.

La actividad AzureMLBatchExecution no requiere una entrada (si no se necesitan dependencias de entrada). También permite ser explícito sobre si usar o no entradas y salidas de servicio web. Si decide usar entradas o salidas de servicio web, podrá especificar los conjuntos de datos de Blob de Azure pertinentes para usar. Además, podrá especificar claramente los valores para los parámetros de servicio web que proporciona el servicio web.

Si desea seguir usando la actividad AzureMLBatchScoring, consulte el artículo [Actividad de puntuación por lotes de Aprendizaje automático de Azure](data-factory-create-predictive-pipelines.md) para obtener más información.


**P:** Tengo varios archivos generados por medio de mis canalizaciones de macrodatos. ¿Puedo usar la actividad AzureMLBatchExecution para trabajar en todos los archivos?

**R:** Sí. Consulte **Uso de un módulo lector para leer datos de varios archivos de blob de Azure** para obtener más información.


## Otras referencias

- [Entrada de blog de Azure: Introducción a la factoría de datos de Azure y al Aprendizaje automático de Azure](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Oct15_HO3-->