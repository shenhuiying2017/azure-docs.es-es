<properties 
	pageTitle="Factoría de datos - Crear canalizaciones predictivas con la factoría de datos y el aprendizaje automático | Azure" 
	description="Describe cómo crear crear canalizaciones predictivas mediante el generador de datos Azuer y aprendizaje automático de Azure" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Crear canalizaciones predictivas con la factoría de datos de Azure y el aprendizaje automático de Azure 
## Información general
Puede hacer operativa publicado [aprendizaje automático de Azure][azure-machine-learning] modelos dentro de las canalizaciones de generador de datos de Azure. Estas canalizaciones se denominan canalizaciones predictivas. Para crear una canalización predictiva, necesitará:

-	La clave de la API del modelo del área de trabajo publicada y la URL de puntuación del lote (vea la imagen siguiente)
-	Un blob de Azure que contiene el archivo CSV de entrada (o) de una base de datos de SQL Azure que contiene la entrada de datos de almacenamiento examinados. 
-	Almacenamiento de blobs de Azure que contendrá el archivo CSV de resultados de la puntuación (o) de una base de datos de SQL Azure que contendrá los datos de salida. 

	![Panel de aprendizaje de máquina][machine-learning-dashboard]

	El lote de puntuación dirección URL para el AzureMLLinkedService se obtiene como del indicado en la imagen anterior, menos ' ** Ayuda **': https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Un **canalización predictivo** consta de estas partes:

-	Tablas de entrada y salida
-	SQL Azure y almacenamiento de Azure y Azure ML vinculan de servicios
-	Una canalización con actividad de puntuación de lote de ML de Azure

> [AZURE.NOTE]Puede utilizar parámetros de servicios Web expuestos por un servicio Web de aprendizaje del equipo de Azure publicado en las canalizaciones de generador de datos de Azure (ADF). Para obtener más información, vea la sección de parámetros de servicio Web en este artículo.

## Ejemplo
Este ejemplo utiliza el almacenamiento de Azure para almacenar los datos de entrada y salidos. También puede utilizar la base de datos de SQL Azure en lugar de utilizar el almacenamiento de Azure.

Se recomienda que siguió el [empezar a trabajar con el generador de datos de Azure][adf-getstarted] tutorial antes de pasar por este ejemplo y utilice el Editor de generador de datos para crear artefactos de generador de datos (servicios vinculados, tablas, canalización) en este ejemplo.
 

1. Cree un servicio vinculado para su almacenamiento de Azure. Si los archivos de entrada y salida de puntuación van a estar en diferentes cuentas de almacenamiento, necesitará dos servicios vinculados. Este es un ejemplo de JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Cree las tablas de la factoría de datos de Azure de entrada y salida. Tenga en cuenta que a diferencia de otras tablas de generador de datos, éstos deben contener ambos **folderPath** y **nombre de archivo** valores. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de "waitOnExternal" que se muestra en el ejemplo siguiente y su ScoringInputBlob sería la tabla de salida de una actividad diferente.

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
	
	Un lote de puntuación archivo csv debe tener la fila de encabezado de columna. Si está utilizando la **copia actividad** para crear o mover el csv en el almacenamiento de blobs, debe establecer la propiedad de receptor **blobWriterAddHeader** a **true**. Por ejemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si el archivo csv no tiene la fila de encabezado, verá el siguiente error: **Error en la actividad: Error al leer la cadena. Token inesperado: StartObject. Ruta de acceso ', línea 1, posición 1**.
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


4. Crear un servicio de tipo vinculado: **AzureMLLinkedService**, con la clave de API y el modelo de lotes puntuación dirección URL.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Por último, crear una canalización que contiene un **AzureMLBatchScoringActivity**. Obtendrá la ubicación del archivo de entrada de las tablas de entrada, llamará a la API de puntuación de lote de AzureML y copiará la salida de la puntuación del lote en el blob proporcionado en la tabla de salida. A diferencia de otras actividades de la factoría de datos, AzureMLBatchScoringActivity solo puede tener una tabla de entrada y salida.

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


## Parámetros de servicio Web
Puede utilizar parámetros de servicios Web expuestos por un servicio Web de aprendizaje del equipo de Azure publicado en las canalizaciones de generador de datos de Azure (ADF). Puede crear un experimento en aprendizaje automático de Azure y publicarlo como un servicio web y, a continuación, usar ese servicio web en varias canalizaciones ADF o actividades, pasando entradas diferentes a través de los parámetros del servicio Web.

### Pasar valores de parámetros de servicio Web
Agregar un **transformación** sección a la **AzureMLBatchScoringActivty** sección en la canalización de JSON para especificar valores para parámetros de servicio Web en esa sección, como se muestra en el ejemplo siguiente:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


También puede usar [funciones del generador de datos](https://msdn.microsoft.com/library/dn835056.aspx) en pasar valores para la Web, servicio parámetros como se muestra en el ejemplo siguiente:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Los parámetros del servicio Web distinguen mayúsculas de minúsculas, así que asegúrese de que los nombres especificados en la actividad JSON coinciden con las expuestas por el servicio Web.

### SQL azure lectores y escritores
Un escenario común para utilizar parámetros de servicio Web es el uso de Azure SQL lectores y escritores. El módulo de lector se utiliza para cargar datos en un experimento de servicios de administración de datos fuera de Azure máquina aprendizaje Studio y el módulo de escritor es guardar los datos de sus experimentos en servicios de administración de datos fuera de Azure máquina aprendizaje Studio. Para obtener más información acerca de lector/escritor SQL Azure/Blob de Azure, consulte [lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) temas en MSDN Library. El ejemplo en la sección anterior utiliza el lector de Blob de Azure y escritor de Blob de Azure. En esta sección se describe el uso de lector de SQL Azure y el escritor SQL Azure.

#### Lector SQL Azure
En Azure ML Studio, puede crear un experimento y publicar un servicio Web con un lector de SQL Azure para la entrada. El lector de SQL Azure tiene propiedades de conexión que se pueden exponer como parámetros de servicios Web, lo que permite los valores de las propiedades de conexión que se pasan en tiempo de ejecución en el lote de solicitudes de puntuación.

En tiempo de ejecución, los detalles de la tabla de la factoría de datos de entrada se utilizará el servicio del generador de datos para rellenar los parámetros del servicio Web. Tenga en cuenta que debe utilizar nombres predeterminados (nombre del servidor de base de datos, nombre de la base de datos, nombre de cuenta de usuario de servidor, contraseña de cuenta de usuario de servidor) para los parámetros del servicio Web para esta integración con el servicio de generador de datos para trabajar.

Si tiene cualquier Web adicionales parámetros de servicio, use la **webServiceParameters** sección de la actividad de JSON. Si especifica valores para parámetros de lector de SQL Azure en esta sección, los valores invalidará los valores que ha seleccionado en el servicio de SQL Azure vinculados de entrada. No se recomienda que especificar valores para el lector de SQL Azure directamente en la sección webServiceParameters. Use la sección para pasar los valores para los parámetros adicionales.

Para utilizar un lector de SQL Azure a través de una canalización del generador de datos de Azure, haga lo siguiente:

- Crear un **SQL Azure vinculados servicio**. 
- Crear un generador de datos **tabla** que utiliza **AzureSqlTableLocation**.
- Establecer ese generador de datos **tabla** como el **entrada** para el **AzureMLBatchScoringActivity** en la canalización de JSON. 



#### Sistema de escritura SQL Azure
Como con el lector de SQL Azure, un sistema de escritura de SQL Azure también pueden tener sus propiedades expuestos como parámetros de servicio Web. Un sistema de escritura de SQL Azure usa configuración desde el servicio vinculado asociado a la tabla de entrada o la tabla de salida. La siguiente tabla describe cuando la entrada se usan servicios vinculados frente a salida vinculado servicio.

<table>
<tr>
<td>Entrada/salida</td>
<td><b>La entrada es SQL Azure</b></td>
<td><b>La entrada es el Blob de Azure</b></td>
</tr>
<tr>
<td><b>Salida es SQL Azure</b></td>
<td><p>El servicio de generador de datos usa la información de la cadena de conexión del servicio de entrada vinculada para generar los parámetros del servicio web con nombres: "Nombre de servidor de base de datos", "Nombre de la base de datos", "Nombre de cuenta de usuario de servidor", "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe utilizar estos nombres predeterminados para los parámetros del servicio Web en Azure ML Studio.</p>
<p>Si el lector de SQL Azure y el escritor SQL de Azure en el modelo de Azure ML comparten los mismos parámetros de servicio Web mencionados anteriormente, está bien. Si no comparten la misma paramers de servicio Web, por ejemplo, si el sistema de escritura de SQL Azure usa los nombres de parámetros: servidor nombre1, Nombre1 de base de datos, Nombre1 de cuenta de usuario de servidor, usuario del servidor de base de datos cuenta password1 (con '1' al final) y, a continuación, debe pasar los valores para estos parámetros de servicio web de salida en la sección webServiceParameters de actividad de JSON.</p>
<p>
Puede pasar valores de cualquier otro parámetro del servicio Web utilizando la sección webServiceParameters de actividad de JSON.  
</p>

</td>
<td>
<p>El servicio de generador de datos usa la información de la cadena de conexión del servicio de salida vinculado para generar los parámetros del servicio web con nombres: "Nombre de servidor de base de datos", "Nombre de la base de datos", "Nombre de cuenta de usuario de servidor", "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe utilizar estos nombres predeterminados para los parámetros del servicio Web en Azure ML Studio.</p>
<p>Puede pasar valores de cualquier otro parámetro del servicio Web utilizando la sección webServiceParameters de actividad de JSON. <p>Blob de entrada se utilizará como ubicación de entrada.</p>
</td>
</tr>
<tr>
<td><b>Es de salida Blob de Azure</b></td>
<td>El servicio de generador de datos usa la información de la cadena de conexión del servicio de entrada vinculada para generar los parámetros del servicio web con nombres: "Nombre de servidor de base de datos", "Nombre de la base de datos", "Nombre de cuenta de usuario de servidor", "Contraseña de cuenta de usuario de servidor". Tenga en cuenta que debe utilizar estos nombres predeterminados para los parámetros del servicio Web en Azure ML Studio.
</td>
<td>
<p>Debe pasar los valores de los parámetros del servicio Web utilizando la sección WebServiceParameters de actividad de JSON.</p> 

<p>BLOB se utilizará como ubicaciones de entrada y salidas.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Sistema de escritura de SQL Azure puede encontrar infracciones de clave si está sobrescribiendo una columna de identidad. Debe asegurarse de que se estructura de la tabla de salida para evitar esta situación.
> 
> Puede utilizar tablas de ensayo con una actividad de procedimiento almacenado para combinar filas o truncar los datos antes de puntuación. Si utiliza este enfoque, establezca la configuración de simultaneidad de la executionPolicy en 1.

### Ejemplo de uso de parámetros de servicio Web
#### Canalización con AzureMLBatchScoringActivity con parámetros de servicio Web

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
En el ejemplo anterior de JSON:

- El modelo de Azure ML utiliza Azure SQL lector y escritor de SQL Azure
- Cuando se expone a través del servicio Web, se utilizan los nombres predeterminados para los parámetros
	- Para el **lector**: nombre del servidor, nombre de la base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor de base de datos.
	- Para el **escritor**: base de datos servidor nombre1, Nombre1 de base de datos, Nombre1 de cuenta de usuario de servidor y password1 de cuenta de usuario de servidor.
	
		Tenga en cuenta que el lector y escritor no comparten los parámetros en este caso.  
- El servicio de generador de datos genera automáticamente valores para parámetros de servicio Web con los nombres **el nombre del servidor de base de datos**, **nombre de base de datos**, **nombre de cuenta de usuario de Server**, y **contraseña de cuenta de usuario de servidor**, que coinciden con los nombres del lector de entrada. Por lo tanto, no es necesario pasar explícitamente los valores para estos parámetros a través de **webServiceParameters** en la actividad siguiente JSON.  
- Los parámetros de escritor (las que con el sufijo '1') no se rellenan automáticamente por el servicio del generador de datos. Por lo tanto, debe especificar valores para estos parámetros en el **webServiceParameters** sección de la actividad de JSON.  
- **ID de cliente**, **puntúan etiquetas**, y **puntúan probabilidades** se guardan como columnas separadas por comas. 
- El **nombre de la tabla de datos** en este ejemplo corresponde a una tabla en la base de datos de salida.




## Otras referencias

Artículo | Descripción
------ | ---------------
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de cmdlets, script JSON, biblioteca de clases. NET, funciones, etc.... 

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

<!---HONumber=GIT-SubDir--> 