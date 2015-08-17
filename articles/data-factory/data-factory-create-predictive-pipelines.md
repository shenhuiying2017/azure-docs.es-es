<properties 
	pageTitle="Factoría de datos - Crear canalizaciones predictivas con la factoría de datos y el aprendizaje automático | Azure" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Crear canalizaciones predictivas con la factoría de datos de Azure y el aprendizaje automático de Azure 
## Información general

Factoría de datos de Azure permite crear fácilmente canalizaciones que aprovechan un servicio web de [Aprendizaje automático de Azure][azure-machine-learning] publicado para realizar análisis predictivos. Esto permite usar Factoría de datos de Azure para coordinar el procesamiento y el movimiento de datos y, a continuación, realizar la puntuación por lotes mediante Aprendizaje automático de Azure. Para ello, deberá hacer lo siguiente:

1. Usar la actividad **AzureMLBatchScoring**.
2. **URI de solicitud** para la API Ejecución de lotes. Para encontrar el URI de solicitud, haga clic en el vínculo **EJECUCIÓN DE LOTES** en la página de servicios web (que se muestra a continuación).
3. **Clave de API** para el servicio web Aprendizaje automático de Azure publicado. Para encontrar esta información, haga clic en el servicio web que ha publicado. 

	![Panel de aprendizaje automático][machine-learning-dashboard]

Una **canalización predictiva** consta de estas partes:

-	Tablas de entrada y salida
-	Almacenamiento de Azure/SQL Azure y servicios vinculados de ML de AzureSQL Azure y almacenamiento de Azure y Azure ML vinculan de servicios
-	Una canalización con actividad de puntuación de lote de ML de Azure

> [AZURE.NOTE]Puede utilizar parámetros de servicios web expuestos por un servicio web de Aprendizaje automático de Azure publicado en las canalizaciones de Factoría de datos de Azure (ADF). Para obtener más información, consulte la sección Parámetros de servicio web en este artículo.

## Ejemplo
Este ejemplo utiliza Almacenamiento de Azure para almacenar los datos de entrada y salida. También puede utilizar Base de datos SQL de Azure en lugar de utilizar Almacenamiento de Azure.

Se recomienda que siga el tutorial [Introducción a la Factoría de datos de Azure][adf-getstarted] antes de pasar por este ejemplo y utilizar el Editor de Factoría de datos para crear artefactos de Factoría de datos (servicios vinculados, tablas y canalización) en este ejemplo.
 

1. Cree un **servicio vinculado** para su **Almacenamiento de Azure**. Si los archivos de entrada y salida de puntuación van a estar en diferentes cuentas de almacenamiento, necesitará dos servicios vinculados. Este es un ejemplo de JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Cree la **tabla** de **entrada** de Factoría de datos de Azure. Tenga en cuenta que a diferencia de otras tablas de Factoría de datos, estas deben contener tanto los valores **folderPath** como **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de **external** y **externalData** que se muestra en el ejemplo siguiente y su ScoringInputBlob sería la tabla de salida de una actividad diferente.

		{
		  "name": "ScoringInputBlob",
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
	
	Un archivo CSV de puntuación por lotes debe tener la fila de encabezado de columna. Si está usando la **copia de actividad** para crear/mover el CSV en el Almacenamiento de blog, debe establecer la propiedad de receptor **blobWriterAddHeader** en **true**. Por ejemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si el archivo CSV no tiene la fila de encabezado, es posible que vea el siguiente error: **Error en actividad: error de lectura de la cadena. Token inesperado: StartObject. Path '', line 1, position 1**.
3. Cree la **tabla** de **salida** de Factoría de datos de Azure. En este ejemplo se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin esto, la actividad sobrescribiría el archivo.

		{
		  "name": "ScoringResultBlob",
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

4. Cree un **servicio vinculado** de tipo **AzureMLLinkedService**; para ello, proporcione la clave de API y la URL de puntuación del lote de modelo.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Por último, cree una canalización que contenga una **AzureMLBatchScoringActivity**. Obtendrá la ubicación del archivo de entrada de las tablas de entrada, llamará a la API de puntuación de lote de AzureML y copiará la salida de la puntuación del lote en el blob proporcionado en la tabla de salida. A diferencia de otras actividades de la factoría de datos, AzureMLBatchScoringActivity solo puede tener una tabla de entrada y salida.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchScoring",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "ScoringInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "ScoringResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
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

## Parámetros del servicio web
Puede utilizar parámetros de servicios web expuestos por un servicio web de Aprendizaje automático de Azure publicado en las canalizaciones de Factoría de datos de Azure (ADF). Puede crear un experimento en Aprendizaje automático de Azure y publicarlo como un servicio web y, a continuación, usar ese servicio web en varias actividades y canalizaciones ADF pasando por distintas entradas a través de los parámetros de servicio web.

### Paso de valores para los parámetros de servicio web
Agregue una sección **typeProperties** a la sección **AzureMLBatchScoringActivty** en la canalización JSON para especificar valores para los parámetros del servicio web en esa sección, como se muestra en el siguiente ejemplo:

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


También puede usar [Funciones de Factoría de datos](https://msdn.microsoft.com/library/dn835056.aspx) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.

### Módulos Lector y Escritor

Un escenario común para el uso de parámetros de servicio web es el uso de Lectores y escritores SQL de Azure. El módulo de lector se utiliza para cargar datos en un experimento desde los servicios de administración de datos fuera de Estudio de aprendizaje automático de Azure y el módulo de escritor es para guardar datos de los experimentos en los servicios de administración de datos fuera de Estudio de aprendizaje automático de Azure. Para obtener información acerca del lector/escritor SQL Azure/Blob, consulte los temas [Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library. El ejemplo de la sección anterior utilizó el lector de Blob de Azure y el lector de Blob de Azure. En esta sección se trata el uso del lector SQL Azure y el escritor SQL Azure.

#### SQL Azure como origen de datos
En Estudio de aprendizaje automático de Azure, puede crear un experimento y publicar un servicio web con un lector SQL de Azure para la entrada. El lector SQL de Azure tiene propiedades de conexión que pueden visualizarse como parámetros de servicio web, lo que permite a los valores para las propiedades de conexión que se pasen en tiempo de ejecución en la solicitud de puntuación por lotes.

En tiempo de ejecución, el servicio de Factoría de datos usará información de la tabla de Factoría de datos de entrada para completar los parámetros de servicio web. Tenga en cuenta que debe usar nombres predeterminados (nombre de servidor de base de datos, nombre de base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor) para que los parámetros de servicio web para esta integración con el servicio de Factoría de datos funcionen.

Si tiene parámetros de servicio web adicionales, use la sección **webServiceParameters** de JSON de actividad. Si especifica valores para los parámetros de Lector SQL de Azure en esta sección, los valores sobrescribirán los valores seleccionados del servicio vinculado de SQL Azure de entrada. No recomiendo que especifique valores para Lector SQL de Azure directamente en la sección webServiceParameters. Use la sección para pasar valores para parámetros adicionales.

Para utilizar Lector SQL de Azure a través de la canalización de Factoría de datos de Azure, realice lo siguiente:

- Cree un **servicio vinculado de SQL Azure**. 
- Cree una **tabla** de Factoría de datos que use **AzureSqlTable**.
- Establezca la **tabla** de Factoría de datos como la **entrada** para **AzureMLBatchScoringActivity** en el JSON de canalización. 



#### SQL Azure como un receptor de datos
Como con el Lector SQL Azure, un Escritor SQL Azure también puede tener sus propiedades expuestas como parámetros de servicio web. Un Escritor SQL de Azure usa la configuración del servicio vinculado asociado con la tabla de entrada o la entrada de salida. La siguiente tabla describe cuándo se usa el servicio vinculado de entrada frente el servicio vinculado de salida.

| Entrada/salida | La entrada es SQL Azure | La entrada es Blob de Azure |
| ------------ | ------------------ | ------------------- |
| La salida es SQL Azure | <p>El servicio Factoría de datos usa la información de cadena de conexión del servicio vinculado de ENTRADA para generar los parámetros del servicio web con los nombres: "Nombre de servidor de base de datos", "Nombre de base de datos", "Nombre de cuenta de usuario de servidor" y "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe usar estos nombres predeterminados para los parámetros del servicio web en Estudio de aprendizaje automático de Azure.</p><p>Si el Lector SQL de Azure y el Escritor SQL de Azure en el modelo de Aprendizaje automático de Azure comparten los mismos parámetros del servicio web mencionados anteriormente, significa que todo es correcto. Si no comparten los mismos parámetros del servicio web, por ejemplo, si el Escritor SQL de Azure usa los nombres de parámetros: Database server name1, Database name1, Server user account name1, Server user account password1 (con "1" al final), debe pasar estos valores para estos parámetros del servicio web de SALIDA en la sección webServiceParameters de JSON de actividad.</p><p>Puede pasar valores para cualquier otro parámetro del servicio web con la sección webServiceParameters de JSON de actividad.</p> | <p>El servicio Factoría de datos usa la información de cadena de conexión del servicio vinculado de SALIDA para generar los parámetros del servicio web con los nombres: "Nombre de servidor de base de datos", "Nombre de base de datos", "Nombre de cuenta de usuario de servidor" y "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe usar estos nombres predeterminados para los parámetros del servicio web en Estudio de aprendizaje automático de Azure.</p><p>Puede pasar valores para cualquier otro parámetro del servicio web con la sección webServiceParameters de JSON de actividad. <p>El blob de entrada se usará como ubicación de entrada.</p> |
|La salida es Blob de Azure | El servicio Factoría de datos usa la información de cadena de conexión del servicio vinculado de ENTRADA para generar los parámetros de servicio web con los nombres: "Nombre de servidor de base de datos", "Nombre de base de datos", "Nombre de cuenta de usuario de servidor" y "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe usar estos nombres predeterminados para los parámetros de servicio web en Estudio de aprendizaje automático de Azure. | <p>Debe pasar valores para cualquier otro parámetro del servicio web con la sección webServiceParameters de JSON de actividad.</p><p>Los blobs se usarán como ubicaciones de entrada y salida.</p> |
    

> [AZURE.NOTE]Escritor SQL de Azure puede detectar infracciones de clave si se sobrescribe una columna de identidad. Debe asegurarse de que estructura la tabla de salida para evitar esta situación.
> 
> Puede usar tablas de ensayo con una actividad de procedimiento almacenado para combinar filas o truncar los datos antes de la puntuación. Si usa este enfoque, establezca la configuración de concurrencia de executionPolicy en 1.

#### Blob de Azure como origen

Al usar el módulo Lector en un experimento de Aprendizaje automático de Azure, puede especificar Blob de Azure como entrada. Los archivos en el almacenamiento de blobs de Azure pueden ser los archivos de salida (por ejemplo, 000000\_0) que se generan mediante un script de Pig y Hive en HDInsight. El módulo Lector permite leer archivos (sin extensiones) mediante la configuración de la propiedad **Ruta al contenedor, directorio o blob** del módulo Lector para señalar el contenedor o la carpeta que contiene los archivos, como se muestra a continuación. Tenga en cuenta que el asterisco (*) **especifica que todos los archivos de la carpeta/contenedor (es decir, data/aggregateddata/year=2014/month-6/*)** se leerán como parte del experimento.

![Propiedades de Blob de Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### Ejemplo de uso de parámetros de servicio web
#### Canalización con AzureMLBatchScoringActivity con parámetros de servicio web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchScoring",
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
	        "linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	        "typeProperties": {
	          "webServiceParameters": {
	            "Database server name1": "output.database.windows.net",
	            "Database name1": "outputDatabase",
	            "Server user account name1": "outputUser",
	            "Server user account password1": "outputPassword",
	            "Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
	            "Data table name": "BikeBuyerPredicted"
	          }
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
En el ejemplo JSON anterior:

- El modelo ML de Azure utiliza tanto Lector SQL de Azure y Escritor SQL de Azure
- Cuando se expone mediante servicio web, los nombres predeterminados se usan para los parámetros
	- Para el **lector**: nombre de servidor de base de datos, nombre de base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor.
	- Para el **escritor**: nombre de servidor de base de datos1, nombre de base de datos1, nombre de cuenta de usuario de servidor1 y contraseña de cuenta de usuario de servidor1.
	
		Tenga en cuenta que el lector y el escritor no comparten parámetros en este caso.  
- El servicio Factoría de datos genera automáticamente valores para los parámetros de servicio web con los nombres **Nombre de servidor de base de datos**, **Nombre de base de datos**, **Nombre de cuenta de usuario de servidor** y **Contraseña de cuenta de usuario de servidor**, que coinciden con los nombres del lector de entrada. Por lo tanto, no tiene que pasar de forma explícita los valores para estos parámetros a través de **webServiceParameters** en el JSON de actividad siguiente.  
- El servicio Factoría de datos no completa automáticamente los parámetros de escritor (los que tienen el sufijo "1"). Por lo tanto, tiene que especificar valores para estos parámetros en la sección **webServiceParameters** de JSON de actividad.  
- **Id. de cliente**, **etiquetas puntuadas** y **probabilidades puntuadas** se guardan como columnas separadas por comas. 
- El **nombre de la tabla de datos** en este ejemplo se corresponde con una tabla en la base de datos de salida.
- Las fechas y horas de **inicio** y **finalización** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx).




## Otras referencias

Artículo | Descripción
------ | ---------------
[Referencia para desarrolladores de Factoría de datos de Azure][developer-reference] | La Referencia para desarrolladores incluye contenido de referencia completo para cmdlets, script de JSON, biblioteca de clase .NET, funciones, etc. 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=August15_HO6-->