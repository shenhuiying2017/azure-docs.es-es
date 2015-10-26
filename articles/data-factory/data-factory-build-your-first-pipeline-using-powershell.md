<properties
	pageTitle="Compilación de la primera canalización mediante Azure PowerShell"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo con Azure PowerShell."
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
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Compilación de la primera canalización mediante Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


En este artículo, aprenderá a usar Azure PowerShell para crear su primera canalización. Este tutorial consta de los siguientes pasos:

1.	Creación de la factoría de datos.
2.	Creación de los conjuntos de datos y servicios vinculados (almacenes de datos, procesos).
3.	Creación de la canalización.

Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Revise el artículo [Información general del tutorial](data-factory-build-your-first-pipeline.md) y realice los pasos de requisitos previos antes de completar este tutorial.

## Paso 1: Creación de la factoría de datos

En este paso, use Azure PowerShell para crear una Factoría de datos de Azure llamada ADFTutorialDataFactoryPSH.

1. Inicie Azure PowerShell y ejecute los comandos siguientes. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	- Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y contraseña que usó para iniciar sesión en el Portal de vista previa de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el portal de vista previa.
2. Cambie al modo AzureResourceManager a medida que los cmdlets de la factoría de datos de Azure están disponibles.

		Switch-AzureMode AzureResourceManager
3. Cree un grupo de recursos de Azure con el nombre *ADFTutorialResourceGroup*, mediante la ejecución del siguiente comando.

		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado ADFTutorialResourceGroup. Si usa un grupo de recursos diferentes, deberá usarlo en lugar de ADFTutorialResourceGroup en este tutorial.
4. Ejecute el cmdlet **New-AzureDataFactory** para crear una factoría de datos con el nombre DataFactoryMyFirstPipelinePSH.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

	El nombre de la Factoría de datos de Azure debe ser único de forma global. Si recibe el error **El nombre de la factoría de datos "DataFactoryMyFirstPipelinePSH" no está disponible**, cambie el nombre (por ejemplo, a sunombreADFTutorialDataFactoryPSH). Use este nombre en lugar de ADFTutorialFactoryPSH mientras lleva a cabo los pasos de este tutorial.

En los pasos siguientes, aprenderá a crear los servicios vinculados, los conjuntos de datos y la canalización que va a usar en este tutorial.

## Paso 2: Creación de conjuntos de datos y servicios vinculados
En este paso, vinculará su cuenta de almacenamiento de Azure y un clúster de HDInsight de Azure a petición a su factoría de datos y luego creará un conjunto de datos que represente los datos de salida del procesamiento de Hive.

### Creación de un servicio vinculado de Almacenamiento de Azure
1.	Cree un archivo JSON con el nombre StorageLinkedService.json en la carpeta C:\\ADFGetStartedPSH con el siguiente contenido. Si todavía no existe, cree la carpeta ADFGetStartedPSH.

		{
		    "name": "StorageLinkedService",
		    "properties": {
		        "type": "AzureStorage",
		        "description": "",
		        "typeProperties": {
		            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    }
		}

	Reemplace **account name** por el nombre de la cuenta de almacenamiento de Azure y **account key** por la clave de acceso de la cuenta de almacenamiento de Azure. Para obtener información sobre cómo obtener la clave de acceso de almacenamiento, vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	En Azure PowerShell, cambie a la carpeta ADFGetStartedPSH.
3.	Use el cmdlet **New-AzureDataFactoryLinkedService** para crear un servicio vinculado. Este cmdlet y otros cmdlets de Factoría de datos que usa en este tutorial requieren que pase los valores para los parámetros *ResourceGroupName* y *DataFactoryName*. Como alternativa, puede usar **Get AzureDataFactory** para obtener un objeto **DataFactory** y pasarlo sin necesidad de escribir *ResourceGroupName* y *DataFactoryName* cada vez que ejecute un cmdlet. Ejecute el comando siguiente para asignar el resultado del cmdlet **Get-AzureDataFactory** a una variable **$df**.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Ahora, ejecute el cmdlet **New-AzureDataFactoryLinkedService** para crear el servicio vinculado **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si no hubiera ejecutado el cmdlet **Get-AzureDataFactory** y asignado el resultado a la variable **$df**, tendría que especificar valores para los parámetros *ResourceGroupName* y *DataFactoryName* de la siguiente forma.

		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si cierra Azure PowerShell en mitad del tutorial, tendrá que ejecutar el cmdlet **Get-AzureDataFactory** la próxima vez que inicie Azure PowerShell para completar el tutorial.

### Creación de un servicio vinculado de HDInsight de Azure
Ahora, va a crear un servicio vinculado para un clúster de HDInsight de Azure a petición que se usará para ejecutar el script de Hive.

1.	Cree un archivo JSON con el nombre HDInsightOnDemandLinkedService.json en la carpeta C:\\ADFGetStartedPSH con el siguiente contenido.


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

	Propiedad | Descripción
	-------- | -----------
	Versión | Con esto se especifica que la versión de HDInsight se crea para que sea 3.1.
	ClusterSize | Así se crea un clúster de HDInsight de un nodo.
	TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine.
	JobsContainer | Especifica el nombre del contenedor de trabajos que se creará para almacenar los registros que genere HDInsight.
	linkedServiceName | Especifica la cuenta de almacenamiento que se usará para almacenar los registros que genere HDInsight.
2. Ejecute el cmdlet **New-AzureDataFactoryLinkedService** para crear el servicio vinculado llamado HDInsightOnDemandLinkedService.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos almacenados en el almacenamiento de blobs de Azure.

1.	Cree un archivo JSON con el nombre OutputTable.json en la carpeta C:\\ADFGetStartedPSH con el siguiente contenido:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

	En el ejemplo anterior, se crea un conjunto de datos llamado **AzureBlobOutput** y se especifica la estructura de los datos que generará el script de Hive. Además, se especifica que los resultados se almacenan en el contenedor de blobs llamado **data** y la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.

2. Ejecute el comando siguiente en Azure PowerShell para crear el conjunto de datos Factoría de datos.

		New-AzureDataFactoryDataset $df -File .\OutputTable.json

## Paso 3: Creación de la primera canalización
En este paso, creará la primera canalización:

1.	Cree un archivo JSON con el nombre MyFirstPipelinePSH.json en la carpeta C:\\ADFGetStartedPSH con el siguiente contenido:

	> [AZURE.IMPORTANT]Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento en JSON.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}

	En el ejemplo anterior, se crea una canalización que consta de una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.

	El archivo de script de Hive, partitionweblogs.hql, se almacena en la cuenta de almacenamiento de Azure (especificada mediante scriptLinkedService, denominada StorageLinkedService) y en un contenedor llamado **script**.

	La sección **extendedProperties** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:PartitionedData}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de la canalización.

	En el JSON de actividad, se especifica que el script de Hive se ejecuta en el equipo que especifica el servicio vinculado: **HDInsightOnDemandLinkedService**.
2. Ejecute el comando siguiente para crear la tabla Factoría de datos.

		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Enhorabuena, ya creó correctamente su primera canalización con Azure PowerShell.

### <a name="MonitorDataSetsAndPipeline"></a> Supervisión de los conjuntos de datos y la canalización
En este paso, se usará Azure PowerShell para supervisar lo que está ocurriendo en una factoría de datos de Azure.

1.	Ejecute **Get-AzureDataFactory** y asigne el resultado a la variable **$df**.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.	Ejecute **Get-AzureDataFactorySlice** para obtener la información sobre todos los segmentos de **EmpSQLTable**, que es la tabla de salida de la canalización.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Observe que la StartDateTime que especifique aquí es la misma hora de inicio especificada en el JSON de la canalización. Debería ver una salida similar a la siguiente.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0

3.	Ejecute **Get-AzureDataFactoryRun** para obtener la información de la actividad que se ejecuta para un segmento concreto.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Debería ver una salida similar a la siguiente.

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Puede seguir ejecutando este cmdlet hasta que vea que el segmento se encuentra en el estado Listo o Con error. Cuando el segmento se encuentre en el estado Listo, busque los datos de salida en la carpeta partitioneddata del contenedor data del almacenamiento de blobs. Tenga en cuenta que la creación de un clúster de HDInsight a petición normalmente requiere algo de tiempo.

Vea [Referencia de cmdlets de factoría de datos](https://msdn.microsoft.com/library/azure/dn820234.aspx) para obtener la documentación completa sobre los cmdlets de la factoría de datos.



## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight de Azure a petición. Si desea ver cómo se usa una actividad de copia para copiar datos de un blob de Azure a SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](./data-factory-get-started.md).

## Enviar comentarios
Agradecemos sus comentarios sobre este artículo. Dedique unos minutos a enviar sus comentarios por [correo electrónico](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-powershell.md).

<!---HONumber=Oct15_HO3-->