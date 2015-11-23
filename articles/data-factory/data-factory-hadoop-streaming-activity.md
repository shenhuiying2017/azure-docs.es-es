<properties 
	pageTitle="Actividad de streaming de Hadoop" 
	description="Aprenda cómo puede usar la actividad de streaming de Hadoop en una factoría de datos de Azure para ejecutar programas de streaming de Hadoop en un clúster de HDInsight suyo propio o a petición." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Actividad de streaming de Hadoop
Puede usar la actividad HDInsightStreamingActivity para invocar un trabajo de streaming de Hadoop desde una canalización de Factoría de datos de Azure. El siguiente fragmento de código JSON muestra la sintaxis para usar HDInsightStreamingActivity en un archivo JSON de canalización.

La actividad de streaming de HDInsight en una [canalización](data-factory-create-pipelines.md) de Factoría de datos ejecuta programas de streaming de Hadoop en [su propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno basado en Windows/Linux [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en las [actividades de transformación de datos](data-factory-data-transformation-activities.md) que presenta una descripción general de la transformación de datos y las actividades de transformación admitidas.

## Ejemplo

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

Tenga en cuenta lo siguiente:

1. Establezca **linkedServiceName** en el nombre del servicio vinculado que apunta al clúster de HDInsight en el que se va a ejecutar el trabajo de MapReduce de streaming.
2. Establezca el tipo de la actividad en **HDInsightStreaming**.
3. Para la propiedad **mapper**, especifique el nombre del ejecutable del asignador. En el ejemplo anterior, cat.exe es el ejecutable del asignador.
4. Para la propiedad **reducer**, especifique el nombre del ejecutable del reductor. En el ejemplo anterior, wc.exe es el ejecutable del reductor.
5. Para la propiedad **input** type, especifique el archivo de entrada (incluida la ubicación) para el asignador. En el ejemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample es el contenedor de blobs, example/data/Gutenberg es la carpeta y davinci.txt es el blob.
6. Para la propiedad **output** type, especifique el archivo de salida (incluida la ubicación) para el reductor. La salida del trabajo de streaming de Hadoop se escribirá en la ubicación especificada para esta propiedad.
7. En la sección **filePaths**, especifique las rutas de acceso para los archivos ejecutables del asignador y del reductor. En el ejemplo: "adfsample/example/apps/wc.exe", adfsample es el contenedor de blobs, example/apps es la carpeta y wc.exe es el ejecutable.
8. Para la propiedad **fileLinkedService**, especifique el servicio vinculado de Almacenamiento de vinculados que representa el almacenamiento de Azure que contiene los archivos especificados en la sección filePaths.
9. Para la propiedad **arguments**, especifique los argumentos para el trabajo de streaming.

> [AZURE.NOTE]Como se muestra en el ejemplo, deberá especificar un conjunto de datos de salida para la actividad de streaming de Hadoop para la propiedad **outputs**. Esto es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. No es necesario especificar ningún conjunto de datos de entrada para la actividad de la propiedad **inputs**.

	

<!---HONumber=Nov15_HO3-->