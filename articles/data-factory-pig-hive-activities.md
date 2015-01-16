<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Uso de Pig y Hive con la Factoría de datos de Azure" description="Obtenga información acerca de cómo procesar datos mediante la ejecución de scripts de Pig y Hive en un clúster de HDInsight de Azure desde una factoría de datos de Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Uso de Pig y Hive con la Factoría de datos
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza   una operación de procesamiento específica. Por ejemplo, una actividad de copia copia los datos desde un almacenamiento de origen hasta un almacenamiento de destino y una actividad de HDInsight con las transformaciones de Hive y Pig utiliza un clúster de HDInsight de Azure para procesar datos mediante scripts de Hive y Pig. La actividad de HDInsight puede consumir una o más entradas y producir una o más salidas. 

## En este artículo

Sección | Descripción
------- | -----------
[Ejemplo de JSON de Pig](#PigJSON) | Esta sección proporciona un esquema de JSON para la definición de una actividad de HDInsight que usa una transformación de Pig. 
[Ejemplo de JSON de Hive](#HiveJSON) | Esta sección proporciona un esquema de JSON para la definición de una actividad de HDInsight que usa una transformación de Hive. 
[Uso de scripts de Pig y Hive que están almacenados en el almacenamiento de blobs de Azure](#ScriptInBlob) | Describe cómo hacer referencia a scripts de Pig y Hive almacenados en un almacenamiento de blobs de Azure desde una actividad de HDInsight mediante una transformación de Pig y Hive.
[Consultas de Pig y Hive parametrizadas](#ParameterizeQueries) | Describe cómo especificar valores para los parámetros usados en scripts de Pig y Hive mediante la propiedad **extendedProperties** de JSON.
[Tutorial: Uso de Hive con la Factoría de datos de Azure](#Waltkthrough) | Proporciona instrucciones paso a paso para crear una canalización que usa Hive para procesar los datos.  



Al definir una actividad Pig o Hive en una canalización de JSON, la propiedad **type** se debe establecer en: **HDInsightActivity**.

## <a name="PigJSON"></a> Ejemplo de JSON de Pig

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
	
- El **tipo** de actividad está establecido en **HDInsightActivity**.
- **linkedServiceName** está establecido en **MyHDInsightLinkedService**. 
- El **tipo** de la **transformación** está establecido en **Pig**.
- Puede especificar el script de Pig en línea para la propiedad **script** o almacenar los archivos de script de un almacenamiento de blobs de Azure y hacer referencia al archivo mediante la propiedad **scriptPath**, que se explica más adelante en este artículo. 
- Los parámetros del script de Pig se especifican mediante **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 


## <a name="HiveJSON"></a> ## Ejemplo de JSON de Hive


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
	
- El **tipo** de actividad está establecido en **HDInsightActivity**.
- **linkedServiceName** está establecido en **MyHDInsightLinkedService**. 
- El **tipo** de la **transformación** está establecido en **Hive**.
- Puede especificar el script de Hive en línea para la propiedad **script** o almacenar los archivos de script de un almacenamiento de blobs de Azure y hacer referencia al archivo mediante la propiedad **scriptPath**, que se explica más adelante en este artículo. 
- Los parámetros del script de Hive se especifican mediante **extendedProperties**. Más adelante en este artículo se proporcionan más detalles. 

> [WACOM.NOTE] Vea la [Referencia para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener detalles acerca de los cmdlets, los esquemas de JSON y las propiedades del esquema. 


## <a name="ScriptInBlob"></a>Uso de scripts de Pig y Hive que se almacenan en el almacenamiento de blobs de Azure
Puede almacenar scripts de Pig y Hive en un almacenamiento de blobs de Azure asociado con el clúster de HDInsight y hacer referencia a ellos desde las actividades de Pig y Hive mediante el uso de las siguientes propiedades en JSON: 

* **scriptPath**: ruta de acceso al archivo de script de Pig o Hive
* **scriptLinkedService**: cuenta de almacenamiento de Azure que contiene el archivo de script

El siguiente ejemplo de JSON con una canalización de muestra usa una actividad de Hive que hace referencia al archivo **transformdata.hql** almacenado en la carpeta **scripts** del contenedor **adfwalkthrough** en el almacenamiento de blobs de Azure representado por **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
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

  

> [WACOM.NOTE] Vea la [Referencia para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=516908) para obtener detalles acerca de los cmdlets, los esquemas de JSON y las propiedades del esquema.

## <a name="ParameterizeQueries"></a>Consultas de Pig y Hive parametrizadas
Las actividades de Pig y Hive de la factoría de datos le permiten especificar valores para los parámetros usados en los scripts de Pig y Hive, mediante el uso de **extendedProperties**. La sección extendedProperties consta del nombre del parámetro y valor del parámetro.

Consulte el ejemplo siguiente para especificar parámetros para un script de Hive mediante **extendedProperties**. Para usar scripts de Hive  parametrizados, haga lo siguiente:

1.	Defina los parámetros en **extendedProperties**.
2.	En el script de Hive en línea (o) el archivo de script de Hive almacenado en el almacenamiento de blobs, haga referencia al parámetro mediante **${hiveconf:parameterName}**.

   
    		
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


-  

## <a name="Walkthrough"></a>Tutorial: Uso de Hive con la Factoría de datos de Azure
### Requisitos previos
1. Realice el tutorial del artículo [Introducción a la Factoría de datos de Azure][adfgetstarted].
2. Cargue el archivo **emp.txt** que ha creado en el tutorial anterior como **hiveinput\emp.txt** en el contenedor adftutorial del almacenamiento de blobs. La carpeta **hiveinput** se crea automáticamente en el contenedor **adftutorial** cuando carga el archivo emp.txt con esta sintaxis. 
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
		
3.  Cargue **hivequery.hql** en el contenedor **adftutorial** de su almacenamiento de blobs.


### Tutorial

#### Creación de la tabla de entrada
1. Cree un archivo JSON llamado **HiveInputBlobTable.json** en la carpeta **C:\ADFGetStarted\Hive** con el siguiente contenido.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
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
	
	- location **type** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **MyBlobStore** que define una cuenta de almacenamiento de Azure.
	- **folderPath** especifica el contendor o carpeta de blobs para los datos de entrada. 
	- **frequency=Day** y **interval=1** son los segmentos que están disponibles diariamente.
	- **waitOnExternal** significa que estos datos no son producidos por otra canalización, sino que se producen externamente para la factoría de datos. 
	

	Consulte la [Referencia para desarrolladores de la Factoría de datos][developer-reference] para ver las descripciones de las propiedades de JSON.  

2. Inicie **Azure PowerShell** y cambie al modo **AzureResourceManager** si es necesario.
    		
    	Switch-AzureMode AzureResourceManager

5. Cambie a la carpeta: **C:\ADFGetStarted\Hive**.
6. Ejecute el siguiente comando para crear la tabla de entrada en **ADFTutorialDataFactory**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Consulte la [Referencia de cmdlets de la Factoría de datos][cmdlet-reference] para obtener una visión general detallada de los cmdlets de la factoría de datos. 
#### Creación de la tabla de salida
        
1. Cree un archivo JSON llamado **HiveOutputBlobTable.json** con el siguiente contenido y guárdelo en la carpeta **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Ejecute el siguiente comando para crear la tabla de salida en **ADFTutorialDataFactory**.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Creación de un servicio vinculado para un clúster de HDInsight
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición. 

#### Para utilizar un clúster de HDInsight a petición
1. Cree un archivo JSON llamado **HDInsightOnDemandCluster.json** con el siguiente contenido y guárdelo en la carpeta **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Cambie a la carpeta **C:\ADFGetstarted\Hive**.
4. Ejecute el siguiente comando para crear el servicio vinculado para el clúster de HDInsight a petición.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Verá las tablas y los servicios vinculados en la hoja **Factoría de datos** del **Portal de vista previa de Azure**.    
   
#### Para utilizar su propio clúster de HDInsight: 

1. Cree un archivo JSON llamado **MyHDInsightCluster.json** con el siguiente contenido y guárdelo en la carpeta **C:\ADFGetStarted\Hive**. Reemplace el nombre de clúster, el nombre de usuario y la contraseña por valores apropiados antes de guardar el archivo JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de la Factoría de datos de Azure se pueden encontrar en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Cambie a la carpeta **C:\ADFGetstarted\Hive**.
4. Ejecute el siguiente comando para crear el servicio vinculado para su propio clúster de HDInsight.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Creación y programación de la canalización
   
1. Cree un archivo JSON llamado **DFTutorialHivePipeline.json** con el siguiente contenido y guárdelo en la carpeta **C:\ADFGetStarted\Hive**. Si desea usar su propio clúster y ha seguido los pasos para crear el servicio vinculado **MyHDInsightCluster**, sustituya **HDInsightOnDemandCluster** por **MyHDInsightCluster** en el siguiente JSON. 


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
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
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

2. Ejecute el siguiente comando para crear la canalización.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Programe la canalización.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Sustituya el valor **StartDateTime** por los tres días antes del día actual y el valor **EndDateTime** por el día actual. Tanto StartDateTime como EndDateTime son horas UTC y deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. 
	> Si no especifica **EndDateTime**, se calcula como "**StartDateTime + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de **EndDateTime**.
  	
	La tabla de salida está programada para producirse cada día, por lo que habrá tres segmentos producidos. 

4. Consulte la sección [Supervisión de los conjuntos de datos y la canalización][adfgetstartedmonitoring] del artículo [Introducción a la Factoría de datos][adfgetstarted].   

## Otras referencias

Artículo | Descripción
------ | ---------------
[Introducción a la Factoría de datos de Azure][data-factory-introduction] | Este artículo presenta el servicio Factoría de datos de Azure, los conceptos, el valor que proporciona y los escenarios que admite.
[Introducción a la Factoría de datos de Azure][adf-getstarted] | Este artículo proporciona un tutorial completo que le muestra cómo crear una factoría de datos de Azure de ejemplo que copia datos desde un blob de Azure hasta una base de datos SQL de Azure.
[Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] | Este artículo incluye un tutorial que muestra cómo copiar datos desde una base de datos SQL Server local hasta un blob de Azure.
[Tutorial: Movimiento y procesamiento de los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un completo tutorial que muestra cómo implementar un escenario en tiempo real mediante el uso de la Factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y utilizarla en un proceso. 
[Solución de problemas de la factoría de datos][troubleshoot] | Este artículo describe cómo solucionar los problemas de la Factoría de datos de Azure.  
[Referencia para desarrolladores de la Factoría de datos de Azure][developer-reference] | La referencia para desarrolladores incluye el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 


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

[Referencia para desarrolladores]: http://go.microsoft.com/fwlink/?LinkId=516908
[Portal de Azure]: http://portal.azure.com
