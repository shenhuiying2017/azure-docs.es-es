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
	ms.date="06/19/2015" 
	ms.author="spelluru"/>

# Uso de Pig y Hive con la Factoría de datos
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe cómo usar la actividad de HDInsight con la transformación de Pig y Hive transformación en una canalización de Factoría de datos de Azure. Vea [Invocar programas de MapReduce][data-factory-map-reduce] de Factoría de datos para obtener detalles sobre la ejecución de programas de MapReduce en un clúster de HDInsight desde una canalización de la factoría de datos de Azure.

## Tutorial: uso de Hive con la Factoría de datos de Azure
Este tutorial ofrece instrucciones paso a paso para usar una actividad de HDInsight con la transformación de Hive en una canalización de la Factoría de datos.

### Requisitos previos
1. Complete el tutorial del artículo [Introducción a la Factoría de datos de Azure][adfgetstarted].
2. Cree el archivo **hivequery.hql** en una subcarpeta llamada **Hive** en **C:\ADFGetStarted** con el siguiente contenido.
    		
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

	> [AZURE.NOTE]Para usar el motor **Tez** para ejecutar consultas de Hive en el archivo HQL, agregue "**set hive.execution.engine=tez**;" en la parte superior del archivo.
		
3.  Cargue **hivequery.hql** en el contenedor **adftutorial** del almacenamiento de blobs.


### Tutorial

#### Creación de la tabla de salida
        
1. En el **Editor de Factoría de datos**, haga clic en **Nuevo conjunto de datos** y en **Almacenamiento de blobs de Azure** desde la barra de comandos.
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

2. Haga clic en **Implementar** en la barra de comandos para implementar la tabla.


### Creación de un servicio vinculado para un clúster de HDInsight
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. El servicio Factoría de datos de Azure crea y administra el clúster de HDInsight a petición para procesar los datos. Para obtener más información acerca del servicio vinculado a petición de HDInsight, Consulte [Servicio vinculado a petición de Azure HDInsight](https://msdn.microsoft.com/library/dn893526.aspx). Para los fines de este ejemplo, vamos a usar un clúster a petición. Tenga en cuenta que se tardan más de 15 minutos en crear el clúster de HDInsight a petición y que solo se le cobrará el tiempo cuando el clúster de HDInsight esté activo y ejecutando trabajos.

#### Para utilizar un clúster de HDInsight a petición
1. Haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente: 
	1. En la propiedad **clusterSize**, especifique el tamaño del clúster de HDInsight.
	2. En la propiedad **jobsContainer**, especifique el nombre del contenedor predeterminado donde se almacenarán los registros del clúster. Para este tutorial, especifique **adfjobscontainer**.
	3. En la propiedad **timeToLive**, especifique cuánto tiempo el clúster puede estar inactivo antes de que se elimine. 
	4. En la propiedad **version**, especifique la versión de HDInsight que quiere usar. Si excluye esta propiedad, se usa la versión más reciente.  
	5. En **linkedServiceName**, especifique el elemento **StorageLinkedService** que creó en el tutorial Introducción. 

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

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.
   
   
#### Para utilizar su propio clúster de HDInsight: 

1. Haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight** en el menú.
2. En el script JSON, haga lo siguiente: 
	1. En la propiedad **clusterUri**, especifique la dirección URL para su HDInsight. Por ejemplo, https://<clustername>.azurehdinsight.net/.     
	2. En la propiedad **UserName**, escriba el nombre del usuario que tiene acceso al clúster de HDInsight.
	3. En la propiedad **Password**, especifique la contraseña del usuario. 
	4. En la propiedad **LinkedServiceName**, escriba **StorageLinkedService**. Este es el servicio vinculado que creó en el tutorial Introducción. 

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

### Creación y programación de la canalización
   
1. Haga clic en **Nueva canalización** en la barra de comandos. Si no ve el comando, haga clic en **... (puntos suspensivos)** para verlo. 
2. Reemplace el script JSON del panel derecho con el siguiente script JSON. Si quiere usar su propio clúster y ha seguido los pasos para crear el servicio vinculado **HDInsightLinkedService**, reemplace **HDInsightOnDemandLinkedService** por **HDInsightLinkedService** en el siguiente JSON. 


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
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
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

	> [AZURE.NOTE]Reemplace el valor **StartDateTime** por los tres días anteriores al día actual y el valor **EndDateTime** por el día actual. Tanto StartDateTime como EndDateTime deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La tabla de salida está programada para producirse cada día, por lo que habrá tres segmentos producidos.
	> 
	> Reemplace **la cuenta de almacenamiento** del JSON por el nombre de su cuenta de almacenamiento.
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.
4. Vea la sección [Supervisión de los conjuntos de datos y canalización][adfgetstartedmonitoring] del artículo [Introducción a la Factoría de datos][adfgetstarted]. 

	> [AZURE.NOTE]En la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD** para un segmento de una tabla de salida (seleccione la tabla de salida -> seleccione el segmento -> seleccione una ejecución de actividad en el portal), verá vínculos a los registros creados por el clúster de HDInsight. Puede revisarlos en el propio portal o descargarlos en el equipo.
  

## Ejemplo de JSON de Pig
Al definir una actividad de Pig o Hive en una canalización de JSON, la propiedad **type** se debe establecer en: **HDInsightActivity**.

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
	
- El **tipo** de actividad se establece en **HDInsightActivity**.
- **linkedServiceName** se establece en **MyHDInsightLinkedService**. Vea la sección Servicio vinculado de HDInsight a continuación para obtener información sobre cómo crear un servicio vinculado de HDInsight.
- El **tipo** de la **transformación** se establece en **Pig**.
- Puede especificar el script de Pig en línea para la propiedad **script** o almacenar los archivos de script en un almacenamiento de blobs de Azure y hacer referencia al archivo con la propiedad **scriptPath**, que se explica más adelante en este artículo. 
- Los parámetros del script de Pig se especifican con **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 


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
	
- El **tipo** de actividad se establece en **HDInsightActivity**.
- **linkedServiceName** se establece en **MyHDInsightLinkedService**. 
- El **tipo** de la **transformación** se establece en **Hive**.
- Puede especificar el script de Hive en línea para la propiedad **script** o almacenar los archivos de script en un almacenamiento de blobs de Azure y hacer referencia al archivo con la propiedad **scriptPath**, que se explica más adelante en este artículo. 
- Los parámetros del script de Hive se especifican con **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 

> [AZURE.NOTE]Vea la [Referencia para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener información sobre los cmdlets, los esquemas de JSON y las propiedades del esquema.


## Uso de scripts de Pig y Hive en la actividad de HDInsight
Puede almacenar scripts de Pig y Hive en un almacenamiento de blobs de Azure asociado con el clúster de HDInsight y hacer referencia a ellos desde las actividades de Pig y Hive mediante el uso de las siguientes propiedades en JSON:

* **scriptPath**: ruta de acceso al archivo de script de Pig o Hive.
* **scriptLinkedService**: cuenta de almacenamiento de Azure que contiene el archivo de script.

El siguiente ejemplo de JSON para una canalización de ejemplo usa una actividad de Hive que hace referencia al archivo **transformdata.hql** almacenado en la carpeta **scripts** del contenedor **adfwalkthrough** en el almacenamiento de blobs de Azure representado por **StorageLinkedService**.

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


> [AZURE.NOTE]Para usar el motor **Tez** para ejecutar una consulta de Hive, ejecute "**set hive.execution.engine=tez**;" antes de ejecutar la consulta de Hive.
> 
> Vea la [Referencia para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener información sobre los cmdlets, los esquemas de JSON y las propiedades del esquema.

## Consultas de Pig y Hive parametrizadas
Las actividades de Pig y Hive de la Factoría de datos le permiten especificar valores para los parámetros usados en los scripts de Pig y Hive, mediante **extendedProperties**. La sección extendedProperties consta del nombre del parámetro y valor del parámetro.

Consulte el ejemplo siguiente para especificar parámetros para un script de Hive con **extendedProperties**. Para usar scripts de Hive parametrizados, haga lo siguiente:

1.	Defina los parámetros en **extendedProperties**.
2.	En el script de Hive en línea (o) el archivo de script de Hive almacenado en el almacenamiento de blobs, haga referencia al parámetro con **${hiveconf:parameterName}**.

   
    		
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
[Tutorial: desplazamiento y procesamiento de archivos de registro mediante la Factoría de datos][adf-tutorial] | En este artículo se proporciona un tutorial completo en el que se muestra cómo implementar un escenario prácticamente real mediante la Factoría de datos de Azure para transformar los datos de archivos de registro en información.
[Referencia para desarrolladores de la Factoría de datos de Azure][developer-reference] | La Referencia para desarrolladores incluye contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 

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
 

<!---HONumber=July15_HO4-->