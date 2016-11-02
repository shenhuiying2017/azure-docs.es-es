<properties 
	pageTitle="Actividad de Pig" 
	description="Aprenda cómo puede usar la actividad de Pig en Factoría de datos de Azure para ejecutar scripts de Pig en un clúster de HDInsight suyo propio o a petición." 
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
	ms.date="08/31/2016" 
	ms.author="spelluru"/>

# Actividad de Pig

La actividad de Pig de HDInsight en una [canalización](data-factory-create-pipelines.md) de Factoría de datos ejecuta consultas de Pig en [su propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basado en Windows/Linux. Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md), que presenta información general de la transformación de datos y las actividades de transformación admitidas.

## Sintaxis

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
	        	"inputs": [
	          		{
	            		"name": "input tables"
	          		}
	        	],
	        	"outputs": [
	          		{
	            		"name": "output tables"
	          		}
        		],
	        	"linkedServiceName": "MyHDInsightLinkedService",
	        	"typeProperties": {
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## Detalles de la sintaxis

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
name | Nombre de la actividad | Sí
description | Texto que describe para qué se usa la actividad. | No
type | HDinsightPig | Sí
inputs | Una o varias entradas consumidas por la actividad de Pig | No
outputs | Una o varias salidas producidas por la actividad de Pig | Sí
linkedServiceName | Referencia al clúster de HDInsight registrado como un servicio vinculado en la factoría de datos | Sí
script | Especifica el script de Pig en línea | No
ruta de acceso de script | Almacena el script de Pig en un almacenamiento de blobs de Azure y proporciona la ruta de acceso al archivo. Use la propiedad 'script' o 'scriptPath'. No se pueden usar las dos juntas. El nombre del archivo distingue mayúsculas de minúsculas. | No
define los campos | Especifique parámetros como pares de clave y valor para referencia en el script de Pig | No

## Ejemplo

Veamos un ejemplo de análisis de registros de juegos en el que desea identificar el tiempo dedicado por los usuarios a los juegos de su compañía.
 
El registro de juegos de ejemplo siguiente es un archivo separado por comas (,), que contiene los siguientes campos: ProfileID, SessionStart, Duration, SrcIPAddress y GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

El **script de Pig** para procesar estos datos tiene el siguiente aspecto:

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Para ejecutar este script de Pig en una canalización de Factoría de datos, tiene que hacer lo siguiente:

1. Crear un servicio vinculado para registrar [su propio clúster de proceso de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurar un [clúster de proceso de HDInsight a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Llamaremos a este servicio vinculado **HDInsightLinkedService**.
2.	Crear un [servicio vinculado](data-factory-azure-blob-connector.md) para configurar la conexión al almacenamiento de blobs de Azure que hospeda los datos. Llamaremos a este servicio vinculado **StorageLinkedService**.
3.	Crear [conjuntos de datos](data-factory-create-datasets.md)que apuntan a los datos de entrada y salida. Llamaremos al conjunto de datos de entrada **PigSampleIn** y al conjunto de datos de salida **PigSampleOut**.
4.	Copie la consulta de Pig en un archivo de almacenamiento de blobs de Azure configurado en el paso 2. Si el almacenamiento de Azure que hospeda los datos es diferente del que hospeda el archivo de consulta, cree un servicio vinculado de almacenamiento de Azure independiente. Consulte el servicio vinculado en la configuración de la actividad. Utilice **scriptPath** para especificar la ruta de acceso al archivo de script de Pig y **scriptLinkedService**.
	
	> [AZURE.NOTE] También puede proporcionar el script de Pig en línea en la definición de actividad mediante la propiedad **script**. Sin embargo, no se recomienda este enfoque cuando todos los caracteres especiales del script tienen que ser caracteres de escape y pueden provocar problemas de depuración. La práctica recomendada es seguir el paso 4.
5. Cree la canalización con la actividad HDInsightPig. Esta actividad procesa los datos de entrada mediante la ejecución de script de Pig en clúster de HDInsight.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. Implemente la canalización. Consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md) para obtener más información.
7. Supervise la canalización mediante las vistas de supervisión y administración de Factoría de datos. Consulte el artículo [Supervisión y administración de las canalizaciones de Factoría de datos](data-factory-monitor-manage-pipelines.md) para obtener más información.

## Especificación de parámetros para un script de Pig 

Tenga en cuenta el siguiente ejemplo en el que los registros de juegos se introducen diariamente en el almacenamiento de blobs de Azure y se almacenan en una carpeta en función de la fecha y hora. Desea parametrizar el script de Pig y pasar la ubicación de la carpeta de entrada dinámicamente en tiempo de ejecución y también generar la salida dividida con fecha y hora.
 
Para usar un script de Pig parametrizado, haga lo siguiente:

- Defina los parámetros en **defines**.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- En el script de Pig, consulte los parámetros utilizando '**$parameterName**' como se muestra en el ejemplo siguiente:

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 


## Otras referencias
- [Actividad de Hive](data-factory-hive-activity.md)
- [Actividad MapReduce](data-factory-map-reduce.md)
- [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas Spark](data-factory-spark.md)
- [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->