<properties
	pageTitle="Compilación de la primera Data Factory | Microsoft Azure"
	description="En este tutorial se crea un ejemplo de canalización de Data Factory de Azure con la API de REST de Data Factory."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/16/2016"
	ms.author="spelluru"/>

# Compilación de la primera instancia de Data Factory de Azure mediante la API de REST de Data Factory
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-build-your-first-pipeline.md)
- [Uso del Editor de Data Factory.](data-factory-build-your-first-pipeline-using-editor.md)
- [Uso de PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Uso de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Uso de la plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [Uso de la API de REST](data-factory-build-your-first-pipeline-using-rest-api.md)

En este artículo aprenderá a usar la API de REST de Data Factory para crear su primera factoría de datos de Azure.

## Requisitos previos

- Lea el artículo [Información general del tutorial](data-factory-build-your-first-pipeline.md). Este artículo le ayuda a conocer los conceptos básicos de Data Factory de Azure.
- Instale [Curl](https://curl.haxx.se/dlwiz/) en su máquina. Utilice la herramienta CURL con comandos de REST para crear una factoría de datos.
- Siga las instrucciones de [este artículo](../resource-group-create-service-principal-portal.md) para:
	1. Cree una aplicación web denominada **ADFGetStartedApp** en Azure Active Directory.
	2. Obtenga el **Id. de cliente** y la **Clave secreta**.
	3. Obtenga el **Identificador de inquilino**.
	4. Asigne la aplicación **ADFGetStartedApp** al rol **Colaborador de Data Factory**.
- Instale [Azure PowerShell](../powershell-install-configure.md).
- Inicie **PowerShell** y ejecute el comando siguiente. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	1. Ejecute **Login-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que utiliza para iniciar sesión en el Portal de Azure.
	2. Ejecute **Get-AzureRmSubscription** para ver todas las suscripciones de esta cuenta.
	3. Ejecute **Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription.| Set-AzureRmContext** to select the subscription that you want to work with. Replace **NameOfAzureSubscription** with the name of your Azure subscription. 
3. Cree un grupo de recursos de Azure con el nombre **ADFTutorialResourceGroup** ejecutando el siguiente comando en PowerShell.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado ADFTutorialResourceGroup. Si usa un otro grupo de recursos, deberá usar su nombre en lugar de ADFTutorialResourceGroup en este tutorial.

## Creación de definiciones de JSON
Cree los siguientes archivos JSON en la carpeta en la que se encuentra curl.exe.

### datafactory.json 
> [AZURE.IMPORTANT] El nombre debe ser único globalmente, por lo que quizás desee usar el prefijo/sufijo ADFCopyTutorialDF para que sea un nombre único.

	{  
	    "name": "FirstDataFactoryREST",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Reemplace **accountname** y **accountkey** por el nombre y la clave de su cuenta de almacenamiento de Azure. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}


### hdinsightondemandlinkedservice.json

	{
		"name": "HDInsightOnDemandLinkedService",
		"properties": {
			"type": "HDInsightOnDemand",
			"typeProperties": {
				"version": "3.2",
				"clusterSize": 1,
				"timeToLive": "00:30:00",
				"linkedServiceName": "AzureStorageLinkedService"
			}
		}
	}

En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

| Propiedad | Descripción |
| :------- | :---------- |
| Versión | Especifica que la versión de HDInsight creada sea la 3.2. | 
| ClusterSize | Tamaño del clúster de HDInsight. | 
| TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine. |
| linkedServiceName | Especifica la cuenta de almacenamiento que se usa para almacenar los registros que genera HDInsight. |

Tenga en cuenta lo siguiente:

- Data Factory crea un clúster de HDInsight **basado en Windows** con el código JSON anterior. También podría hacer que cree un clúster de HDInsight **basado en Linux**. Para más información, consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Para más información, consulte [Servicio vinculado de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- El clúster de HDInsight crea un **contenedor predeterminado** en el Almacenamiento de blobs especificado en el código JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Este comportamiento es así por diseño. Con el servicio vinculado de HDInsight a petición se crea un clúster de HDInsight cada vez se procesa un segmento, a menos que haya un clúster existente activo (**timeToLive**), que se elimina cuando finaliza el procesamiento.

	A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. Los nombres de estos contenedores siguen un patrón: "adf**nombreDeDataFactory**-**nombreDeServicioVinculado**-marcaDeFechaYHora". Use herramientas como el [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores del almacenamiento de blobs de Azure.

Para más información, consulte [Servicio vinculado a petición de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

### inputdataset.json

	{
		"name": "AzureBlobInput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"fileName": "input.log",
				"folderPath": "adfgetstarted/inputdata",
				"format": {
					"type": "TextFormat",
					"columnDelimiter": ","
				}
			},
			"availability": {
				"frequency": "Month",
				"interval": 1
			},
			"external": true,
			"policy": {}
		}
	}


Este código JSON define un conjunto de datos denominado **AzureBlobInput**, que representa los datos de entrada de una actividad en la canalización. Además, especifica que los datos de entrada se colocan en el contenedor de blobs **adfgetstarted** y en la carpeta **inputdata**.

En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

| Propiedad | Description |
| :------- | :---------- |
| type | La propiedad type se establece en AzureBlob porque los datos residen en Almacenamiento de blobs de Azure. |  
| linkedServiceName | hace referencia a StorageLinkedService que creó anteriormente. |
| fileName | Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este caso, se procesa solo el archivo input.log. |
| type | Los archivos de registro están en formato de texto, por lo que usaremos TextFormat. | 
| columnDelimiter | Las columnas de los archivos de registro están delimitadas por una coma (,). |
| frecuencia/intervalo | La frecuencia está establecida en Mes y el intervalo es 1, lo que significa que los segmentos de entrada estarán disponibles cada mes. | 
| external | Esta propiedad se establece en true si el servicio Factoría de datos no ha generado los datos de entrada. | 

### outputdataset.json

	{
		"name": "AzureBlobOutput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"folderPath": "adfgetstarted/partitioneddata",
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

Este código JSON define un conjunto de datos denominado **AzureBlobOutput**, que representa los datos de salida de una actividad en la canalización. Además, especifica que los resultados se almacenan en el contenedor de blobs **adfgetstarted** y en la carpeta **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.

### pipeline.json
> [AZURE.IMPORTANT] Reemplace **storageaccountname** por el nombre de la cuenta de Almacenamiento de Azure.


	{
		"name": "MyFirstPipeline",
		"properties": {
			"description": "My first Azure Data Factory pipeline",
			"activities": [{
				"type": "HDInsightHive",
				"typeProperties": {
					"scriptPath": "adfgetstarted/script/partitionweblogs.hql",
					"scriptLinkedService": "AzureStorageLinkedService",
					"defines": {
						"inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
						"partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
					}
				},
				"inputs": [{
					"name": "AzureBlobInput"
				}],
				"outputs": [{
					"name": "AzureBlobOutput"
				}],
				"policy": {
					"concurrency": 1,
					"retry": 3
				},
				"scheduler": {
					"frequency": "Month",
					"interval": 1
				},
				"name": "RunSampleHiveActivity",
				"linkedServiceName": "HDInsightOnDemandLinkedService"
			}],
			"start": "2016-07-10T00:00:00Z",
			"end": "2016-07-11T00:00:00Z",
			"isPaused": false
		}
	}

En este fragmento de código JSON, se crea una canalización formada por una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.

El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de Almacenamiento de Azure (especificada mediante la propiedad scriptLinkedService, llamada **StorageLinkedService**) en una carpeta denominada **script** del contenedor **adfgetstarted**.

La sección **defines** especifica la configuración de tiempo de ejecución que se pasa al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Las propiedades **start** y **end** de la canalización especifican el período activo de esta.

En el código JSON de la actividad, se especifica que el script de Hive se ejecuta en el proceso que especifica el servicio vinculado **linkedServiceName**: **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Consulte en la sección [Anatomía de una canalización](data-factory-create-pipelines.md#anatomy-of-a-pipeline) las propiedades JSON que se usan en el ejemplo anterior.

## Definición de variables globales

En Azure PowerShell, ejecute los comandos siguientes después de reemplazar los valores por los suyos propios:

> [AZURE.IMPORTANT] Consulte la sección [Requisitos previos](#prerequisites) para obtener instrucciones sobre cómo obtener el identificador de cliente, el secreto del cliente, el identificador de inquilino y el identificador de suscripción.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "FirstDataFactoryREST"



## Autenticación con AAD

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 



## Creación de Data Factory

En este paso, creará una instancia de Data Factory de Azure llamada **LogProcessingFactory**. Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada. Ejecute los siguientes comandos para crear la factoría de datos:

1. Asigne el comando a la variable denominada **cmd**.

	Confirme que el nombre de la factoría de datos que especifique aquí (ADFCopyTutorialDF) coincide con el nombre especificado en el archivo **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si la factoría de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.

		Write-Host $results

Tenga en cuenta lo siguiente:
 
- El nombre de la Factoría de datos de Azure debe ser único de forma global. Si ve el siguiente error en los resultados: **El nombre “ADFCopyTutorialDF” de factoría de datos no está disponible**, siga estos pasos:
	1. Cambie el nombre (por ejemplo, yournameFirstDataFactoryREST) en el archivo **datafactory.json**. Consulte el tema [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura para los artefactos de Data Factory.
	2. En el primer comando, donde se asigna un valor a la variable **$cmd**, reemplace FirstDataFactoryREST por el nuevo nombre y ejecute el comando.
	3. Ejecute los dos comandos siguientes para invocar la API de REST para crear la factoría de datos e imprimir los resultados de la operación.
- Para crear instancias de Data Factory, debe ser administrador o colaborador de la suscripción de Azure.
- El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, que sea visible públicamente.
- Si recibe el error: "**La suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**", realice una de las acciones siguientes e intente publicarla de nuevo:

	- En Azure PowerShell, ejecute el siguiente comando para registrar el proveedor de Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Puede ejecutar el comando siguiente para confirmar que se ha registrado el proveedor de Data Factory.
	
			Get-AzureRmResourceProvider
	- Inicie sesión mediante la suscripción de Azure en el [Portal de Azure](https://portal.azure.com) y vaya a una hoja de Data Factory (o) cree una factoría de datos en el Portal de Azure. Esta acción registra automáticamente el proveedor.

Antes de crear una canalización, debe crear algunas entidades de factoría de datos primero. En primer lugar debe crear servicios vinculados para vincular los almacenes de datos y procesos con su almacén de datos, y, después, definir los conjuntos de datos de entrada y salida para representar los datos en los almacenes de datos vinculados.

## Crear servicios vinculados 
En este paso, vinculará su cuenta de Almacenamiento de Azure y el clúster de HDInsight de Azure a petición con su factoría de datos. La cuenta de Almacenamiento de Azure contiene los datos de entrada y salida de la canalización de este ejemplo. Para ejecutar el script de Hive especificado en la actividad de la canalización en este ejemplo, se usa el servicio vinculado de HDInsight.

### Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Con este tutorial, usará la misma cuenta de Almacenamiento de Azure para almacenar los datos de entrada y salida y el archivo de script de HQL.

1. Asigne el comando a la variable denominada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el servicio vinculado se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results

### Creación de un servicio vinculado de HDInsight de Azure
En este paso, vinculará un clúster de HDInsight a petición con la factoría de datos. El clúster de HDInsight se crea automáticamente en tiempo de ejecución y se elimina después de hacer el procesamiento y de permanecer inactivo durante el período de tiempo especificado. Puede usar su propio clúster de HDInsight en lugar de usar un clúster de HDInsight a petición. Consulte [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para más información.

1. Asigne el comando a la variable denominada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el servicio vinculado se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.

		Write-Host $results

## Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representan los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio **StorageLinkedService** que ha creado anteriormente en este tutorial. El servicio vinculado apunta a una cuenta de Almacenamiento de Azure y los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo en el almacenamiento que contiene los datos de entrada y salida.

### Creación de un conjunto de datos de entrada
En este paso, creará el conjunto de datos de entrada que representa los datos de entrada almacenados en Almacenamiento de blobs de Azure.

1. Asigne el comando a la variable denominada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results
### Creación del conjunto de datos de salida
En este paso, creará el conjunto de datos de salida que representa los datos de salida almacenados en Almacenamiento de blobs de Azure.

1. Asigne el comando a la variable denominada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results 

## Creación de una canalización
En este paso, creará la primera canalización con una actividad **HDInsightHive**. El segmento de entrada está disponible mensualmente (frecuencia: mensual, intervalo: 1), el segmento de salida se genera mensualmente y la propiedad de programador de la actividad también se establece en mensual. La configuración del conjunto de datos de salida y la del programador de la actividad deben coincidir. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada.

Confirme que el archivo **input.log** aparece en la carpeta **adfgetstarted/inputdata** en el Almacenamiento de blobs de Azure y ejecute el siguiente comando para implementar la canalización. Dado que las horas de **inicio** y **fin** están establecidas en el pasado y **isPaused** está establecido en false, la canalización (actividad en la canalización) se ejecutará inmediatamente después de implementar.

1. Asigne el comando a la variable denominada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.

		Write-Host $results
5. Enhorabuena, ya creó correctamente su primera canalización con Azure PowerShell.

## Supervisión de la canalización
En este paso, utilizará la API de REST de Data Factory para supervisar los segmentos que la canalización está produciendo.

	$ds ="AzureBlobOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;

	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Ejecute Invoke-Command y el siguiente hasta que vea el segmento en estado **Listo** o **Error**. Cuando el segmento se encuentre en el estado Listo, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** de Almacenamiento de blobs. La creación de un clúster de HDInsight a petición normalmente requiere algo de tiempo.

![datos de salida](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] El archivo de entrada se elimina cuando el segmento se procesa correctamente. Por lo tanto, si desea volver a ejecutar el segmento o volver a realizar el tutorial, cargue el archivo de entrada (input.log) en la carpeta inputdata del contenedor adfgetstarted.

También puede usar el Portal de Azure para supervisar los segmentos y solucionar cualquier problema. Consulte más detalles en la sección sobre la [supervisión de canalizaciones con el Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline).

## Resumen 
En este tutorial, ha creado una instancia de Data Factory de Azure para procesar datos mediante la ejecución de un script de Hive en un clúster de Hadoop en HDInsight. Ha usado el Editor de Data Factory en el Portal de Azure para realizar los siguientes pasos:

1.	Ha creado una **factoría de datos** de Azure.
2.	Ha creado dos **servicios vinculados**:
	1.	El servicio vinculado **Almacenamiento de Azure** para vincular el almacenamiento de blobs de Azure que contiene los archivos de entrada y salida a la factoría de datos.
	2.	El servicio vinculado a petición **HDInsight de Azure** para vincular un clúster de Hadoop en HDInsight a petición a la factoría de datos. Data Factory de Azure crea un clúster de Hadoop en HDInsight justo a tiempo para procesar los datos de entrada y generar los datos de salida.
3.	Ha creado dos **conjuntos de datos**, que describen los datos de entrada y salida para la actividad de Hive de HDInsight en la canalización.
4.	Ha creado una **canalización** con una actividad de **Hive de HDInsight**.

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight de Azure a petición. Para ver cómo se usa una actividad de copia para copiar datos de un blob de Azure en SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Otras referencias
| Tema. | Description |
| :---- | :---- |
| [Referencia de API de REST de Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) | Consulte la documentación completa sobre los cmdlets de Factoría de datos |
| [Transformación y análisis con Data Factory de Azure](data-factory-data-transformation-activities.md) | En este artículo se proporciona una lista de actividades de transformación de datos (por ejemplo, la transformación de Hive para HDInsight usada en este tutorial), que se admiten en Data Factory de Azure. |
| [Programación y ejecución](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a conocer las canalizaciones y actividades de Data Factory de Azure y cómo aprovecharlas para construir flujos de trabajo controlados por datos de un extremo a otro para su escenario o negocio. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayuda a comprender los conjuntos de datos de Data Factory de Azure.
| [Supervisión y administración de canalizaciones de Data Factory de Azure](data-factory-monitor-manage-pipelines.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante las hojas del Portal de Azure. |
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. 

<!---HONumber=AcomDC_0831_2016-->