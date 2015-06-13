<properties 
	pageTitle="Uso de Pig y Hive con la Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo procesar datos mediante la ejecución de scripts de Pig y Hive en un clúster de HDInsight de Azure desde una factoría de datos de Azure." 
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

# Uso de Pig y Hive con la Factoría de datos
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. Este artículo describe cómo utilizar la actividad de HDInsight con Pig y Hive transformación en una canalización del generador de datos de Azure. Consulte [invocar programas MapReduce de generador de datos][data-factory-map-reduce] para obtener más información acerca de cómo ejecutar MapReduce programas en HDInsight de un clúster de una canalización del generador de datos de Azure.

## Tutorial: Uso de Hive con el generador de datos de Azure
Este tutorial proporciona instrucciones paso a paso para usar una actividad de HDInsight con la transformación de Hive en una canalización del generador de datos.

### Requisitos previos
1. Completar el tutorial de [empezar a trabajar con el generador de datos de Azure][adfgetstarted] artículo.
2. Cargar **emp.txt** archivo que creó en el tutorial anterior como **hiveinput\emp.txt** en el contenedor de adftutorial en el almacenamiento de blobs. El **hiveinput** carpeta se crea automáticamente en el **adftutorial** contenedor al cargar el archivo emp.txt con esta sintaxis.

	> [AZURE.NOTE]El archivo emp.txt es sólo un archivo ficticio para este tutorial. Los datos de entrada reales provienen de la **hivesampletable** que ya existe en el clúster de HDInsight. La canalización no utiliza el archivo emp.txt en absoluto.
	
2. Crear **hivequery.hql** archivos en una subcarpeta denominada **Hive** en **C:\ADFGetStarted** con el siguiente contenido.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]Para usar el **Tez** motor para ejecutar consultas de Hive en el archivo HQL, agregue "** establecer hive.execution.engine=tez**;" en la parte superior del archivo.
		
3.  Cargar la **hivequery.hql** a la **adftutorial** contenedor en el almacenamiento de blobs


### Tutorial

#### Creación de la tabla de entrada
1. En el **factoría de datos** módulo para la **ADFTutorialDataFactory**, haga clic en **autor e implementar** para iniciar el Editor del generador de datos.
	
	![Módulo de generador de datos][data-factory-blade]

2. En el **editor factoría de datos**, haga clic en **nuevo conjunto de datos**, y, a continuación, haga clic en **almacenamiento Azure Blob** desde la barra de comandos.
3. Reemplace el script JSON en el panel derecho con el siguiente script JSON:    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Tenga en cuenta lo siguiente:**
	
	- ubicación **tipo** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **StorageLinkedService** que define una cuenta de almacenamiento de Azure.
	- **folderPath** especifica la container\folder de blob para los datos de entrada. 
	- **frecuencia = día** y **intervalo = 1** significa los sectores están disponibles diariamente
	- **waitOnExternal** significa que estos datos no se crea por otra canalización, en su lugar se genera externamente en el generador de datos. 
	

	Consulte [datos de referencia del programador de fábrica][developer-reference] para obtener descripciones de las propiedades de JSON.

2. Haga clic en **implementar** en la barra de comandos para implementar la tabla.
  
#### Creación de la tabla de salida
        
1. En el **editor factoría de datos**, haga clic en **nuevo conjunto de datos**, y, a continuación, haga clic en **almacenamiento Azure Blob** desde la barra de comandos.
2. Reemplace el script JSON en el panel derecho con el siguiente script JSON:

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Haga clic en **implementar** en la barra de comandos para implementar la tabla.


### Creación de un servicio vinculado para un clúster de HDInsight
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición.

#### Para utilizar un clúster de HDInsight a petición
1. Haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight a petición** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterSize** propiedad, especifique el tamaño del clúster de HDInsight.
	2. Para el **jobsContainer** propiedad, especifique el nombre del contenedor predeterminado donde se almacenarán los registros de clúster. Para este tutorial, especifique **adfjobscontainer**.
	3. Para el **timeToLive** propiedad, especifique cuánto tiempo el clúster puede estar inactivo antes de eliminarla. 
	4. Para el **versión** propiedad, especificar la versión de HDInsight que desea utilizar. Si excluye esta propiedad, se utiliza la versión más reciente.  
	5. Para el **linkedServiceName**, especifique **StorageLinkedService** tutorial de introducción que se han creado en Get. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.
   
   
#### Para utilizar su propio clúster de HDInsight: 

1. Haga clic en **nuevo cálculo** en la barra de comandos y seleccione **clúster de HDInsight** en el menú.
2. Haga lo siguiente en la secuencia de comandos JSON: 
	1. Para el **clusterUri** propiedad, escriba la dirección URL para su HDInsight. Por ejemplo: https://<clustername>.azurehdinsight.net/     
	2. Para el **nombre de usuario** propiedad, escriba el nombre de usuario que tenga acceso al clúster de HDInsight.
	3. Para el **contraseña** propiedad, escriba la contraseña del usuario. 
	4. Para el **LinkedServiceName** propiedad, escriba **StorageLinkedService**. Éste es el servicio vinculado que creó en el tutorial de introducción. 

2. Haga clic en **implementar** en la barra de comandos para implementar el servicio vinculado.

### Creación y programación de la canalización
   
1. Haga clic en **nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (Puntos suspensivos)** para verlo. 
2. Reemplace el JSON en el panel derecho con el siguiente script JSON. Si desea utilizar su propio clúster y seguir los pasos para crear el **HDInsightLinkedService** vinculado servicio, reemplace **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** en el siguiente JSON. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Reemplace **StartDateTime** valor con los tres días antes de la fecha actual y **finalización** valor con la fecha actual. StartDateTime y finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La tabla de salida está programada para producirse cada día, por lo que habrá tres segmentos producidos.
	
	> [AZURE.NOTE]Reemplace **la cuenta de almacenamiento** en JSON con el nombre de la cuenta de almacenamiento.
	
	Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.
2. Haga clic en **implementar** en la barra de comandos para implementar la canalización.
4. Consulte [supervisar los conjuntos de datos y canalización][adfgetstartedmonitoring] sección de [comenzar con el generador de datos][adfgetstarted] artículo. 

	> [AZURE.NOTE]En el **Detalles de ejecución de la actividad** módulo para un segmento de una tabla de resultados (seleccione la tabla de salida -> seleccione sector -> seleccione Ejecutar una actividad en el portal), verá vínculos a los registros creados por el clúster de HDInsight. Puede revisarlas en el propio portal o descargarlos en el equipo.
  

## Ejemplo de JSON de Pig
Al definir una actividad Pig o Hive en una canalización de JSON, el **tipo** propiedad debe establecerse en: **HDInsightActivity**.

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Tenga en cuenta lo siguiente:**
	
- Actividad **tipo** está establecido en **HDInsightActivity**.
- **linkedServiceName** está establecido en **MyHDInsightLinkedService**. Vea la sección de servicio de HDInsight vinculada a continuación para obtener más información acerca de cómo crear un servicio HDInsight vinculado.
- El **tipo** de la **transformación** está establecido en **Pig**.
- Puede especificar Pig en línea de comandos para la **secuencia de comandos** propiedad o un almacén de archivos de script en un Azure almacenamiento de blobs y consulte el archivo usando **scriptPath** propiedad, que se explica más adelante en este artículo. 
- Especificar parámetros para el script de Pig mediante el **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 


## Ejemplo de JSON de Hive


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Tenga en cuenta lo siguiente:**
	
- Actividad **tipo** está establecido en **HDInsightActivity**.
- **linkedServiceName** está establecido en **MyHDInsightLinkedService**. 
- El **tipo** de la **transformación** está establecido en **Hive**.
- Puede especificar en línea del script de Hive para el **secuencia de comandos** propiedad o un almacén de archivos de script en un Azure almacenamiento de blobs y consulte el archivo usando **scriptPath** propiedad, que se explica más adelante en este artículo. 
- Especificar parámetros para el script de Hive mediante el **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 

> [AZURE.NOTE]Consulte [material de referencia](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener más información sobre los cmdlets, esquemas JSON y propiedades en el esquema.


## Uso de scripts de Pig y Hive en la actividad de HDInsight
Puede almacenar scripts de Pig y Hive en un almacenamiento de blobs de Azure asociado con el clúster de HDInsight y hacer referencia a ellos desde las actividades de Pig y Hive mediante el uso de las siguientes propiedades en JSON:

* **scriptPath** : ruta de acceso al archivo de script Pig o Hive
* **scriptLinkedService** : cuenta de almacenamiento de Azure que contiene el archivo de script

El siguiente ejemplo JSON de una canalización de ejemplo utiliza una actividad de Hive que hace referencia a **transformdata.hql** archivo almacenado en **secuencias de comandos** carpeta en el **adfwalkthrough** contenedor en el almacenamiento de blobs de Azure representado por la **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
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


> [AZURE.NOTE]Para usar el **Tez** motor para ejecutar una consulta de Hive, ejecute "** establecer hive.execution.engine=tez**;" antes de ejecutar la consulta de Hive.
> 
> Consulte [material de referencia](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener más información sobre los cmdlets, esquemas JSON y propiedades en el esquema.

## Consultas de Pig y Hive parametrizadas
Las actividades de datos fábrica Pig y Hive le permiten especificar los valores de parámetros utilizados en los scripts de Pig y Hive, mediante el uso de **extendedProperties**. La sección extendedProperties consta del nombre del parámetro y valor del parámetro.

Vea el ejemplo siguiente para especificar los parámetros de un script de Hive mediante **extendedProperties**. Para usar scripts de Hive parametrizados, haga lo siguiente:

1.	Definir los parámetros de **extendedProperties**.
2.	En el script de Hive en línea (o) almacenados en el almacenamiento de blog de archivo de script de Hive, consulte el parámetro utilizando **${hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## Otras referencias

Artículo | Descripción
------ | ---------------
[Tutorial: Mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial] | Este artículo proporciona un tutorial de end-to-end que muestra cómo implementar un verdadero casi escenario mediante el generador de datos de Azure para transformar los datos de archivos de registro en perspectivas.
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de cmdlets, script JSON, funciones, etc.... 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir--> 