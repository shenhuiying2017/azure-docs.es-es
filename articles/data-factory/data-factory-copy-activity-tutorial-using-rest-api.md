<properties 
	pageTitle="Tutorial: Crear una canalización con la actividad de copia mediante la API de REST | Microsoft Azure" 
	description="En este tutorial, creará una canalización de Data Factory de Azure con una actividad de copia mediante Visual Studio." 
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
	ms.topic="get-started-article" 
	ms.date="08/17/2016" 
	ms.author="spelluru"/>

# Tutorial: Crear una canalización con la actividad de copia mediante la API de REST
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Uso del Editor de Data Factory.](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Uso de PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Uso de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Uso de la API de REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Uso de la API de .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Uso del Asistente para copia](data-factory-copy-data-wizard-tutorial.md)

Este tutorial muestra cómo crear y supervisar una factoría de datos de Azure mediante la API de REST. La canalización de la factoría de datos utiliza una actividad de copia para copiar datos desde Almacenamiento de blobs de Azure a Base de datos SQL de Azure.

La actividad de copia realiza el movimiento de datos en Data Factory de Azure. La actividad funciona con un servicio disponible de forma global que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información sobre la actividad de copia.

> [AZURE.NOTE] 
Este artículo no abarca toda la API de REST de Data Factory. Consulte [Referencia de API de REST de Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) para ver la documentación completa sobre los cmdlets de Data Factory.
  

## Requisitos previos

- Lea [Información general del tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Instale [Curl](https://curl.haxx.se/dlwiz/) en su máquina. Utilice la herramienta CURL con comandos de REST para crear una factoría de datos.
- Siga las instrucciones de [este artículo](../resource-group-create-service-principal-portal.md) para:
	1. Cree una aplicación web denominada **ADFCopyTutorialApp** en Azure Active Directory.
	2. Obtenga el **Id. de cliente** y la **Clave secreta**.
	3. Obtenga el **Identificador de inquilino**.
	4. Asigne la aplicación **ADFCopyTutorialApp** al rol **Colaborador de Data Factory**.
- Instale [Azure PowerShell](../powershell-install-configure.md).
- Inicie **PowerShell** y ejecute el comando siguiente. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	1. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña que utiliza para iniciar sesión en el Portal de Azure.
	
			Login-AzureRmAccount   
	2. Ejecute el siguiente comando para ver todas las suscripciones para esta cuenta.

			Get-AzureRmSubscription 
	3. Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **&lt; NameOfAzureSubscription** &gt; por el nombre de su suscripción de Azure.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
	1. Cree un grupo de recursos de Azure con el nombre **ADFTutorialResourceGroup** ejecutando el siguiente comando en PowerShell.

			New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

		Si ya existe el grupo de recursos, especifique si desea actualizarlo (Y) o mantenerlo como está (N).

		En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado ADFTutorialResourceGroup. Si usa un otro grupo de recursos, deberá usar su nombre en lugar de ADFTutorialResourceGroup en este tutorial.
  
## Creación de definiciones de JSON
Cree los siguientes archivos JSON en la carpeta en la que se encuentra curl.exe.

### datafactory.json 
> [AZURE.IMPORTANT] El nombre debe ser único globalmente, por lo que quizás desee usar el prefijo/sufijo ADFCopyTutorialDF para que sea un nombre único.

	{  
	    "name": "ADFCopyTutorialDF",  
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

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] Reemplace **servername**, **databasename**, **username** y **password** por los nombres del servidor SQL Azure, de la Base de datos SQL, de la cuenta de usuario y de la contraseña para la cuenta.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}


### inputdataset.json

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "structure": [
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "AzureStorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "fileName": "emp.txt",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

La definición de JSON define un conjunto de datos denominado **AzureBlobInput**, que representa los datos de entrada de una actividad en la canalización. Además, especifica que los datos de entrada se encuentran en el archivo **emp.txt** que está en el contenedor de blobs **adftutorial**.

 Tenga en cuenta lo siguiente:

- **type** de conjunto de datos está establecido en **AzureBlob**.
- **linkedServiceName** se establece en **AzureStorageLinkedService**.
- **folderPath** se establece en el contenedor **adftutorial** y **fileName** se establece en **emp.txt**.
- el **tipo** de formato se establece en **TextFormat**
- Hay dos campos en el archivo de texto: **FirstName** y **LastName** separados por un carácter de coma (**columnDelimiter**)
- El elemento **availability** está establecido en **hourly** (frequency está establecido en hour e interval, en 1). Data Factory busca los datos de entrada cada hora en la carpeta raíz del contenedor de blobs (**adftutorial**) especificado.

Si no especifica **fileName** para un conjunto de datos de entrada, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada.

Si no especifica un valor **fileName** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.&lt;Guid&gt;.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se está procesando) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### outputdataset.json
	
	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "structure": [
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "emp"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


La definición de JSON define un conjunto de datos denominado **AzureBlobOutput**, que representa los datos de salida de una actividad en la canalización. Además, especifica que los resultados se almacenan en la tabla **emp** en la base de datos representada por AzureSqlLinkedService. La sección **disponibilidad** especifica que el conjunto de datos de salida se genere cada hora (frequency: hour e interval: 1).

Tenga en cuenta lo siguiente:

- **type** de conjunto de datos está establecido en **AzureSqlTable**.
- **linkedServiceName** se establece en **AzureSqlLinkedService**.
- **tablename** está establecido en **emp**.
- Hay tres columnas (**ID**, **FirstName** y **LastName**) en la tabla emp de la base de datos. ID es una columna de identidad, por lo que en ella solo se deben especificar **FirstName** y **LastName**.
- El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval** en **1**). El servicio Data Factory generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.

### pipeline.json

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from a blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-08-12T00:00:00Z",
	    "end": "2016-08-13T00:00:00Z"
	  }
	}


Tenga en cuenta lo siguiente:

- En la sección de actividades, solo hay una actividad cuyo **tipo** está establecido en **CopyActivity**.
- La entrada de la actividad está establecida en **AzureBlobInput** y la salida de la actividad está establecida en **AzureSqlOutput**.
- En la sección **transformación**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Puede especificar solo la parte de fecha y omitir la parte de hora de la fecha y hora. Por ejemplo, "03-02-2015", que es equivalente a "03-02-2015T00:00:00Z"

Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de finalización (**end**) es opcional, pero se utilizará en este tutorial.

Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**.

En el ejemplo, hay 24 segmentos de datos, ya que se produce un segmento de datos a la hora.
	
> [AZURE.NOTE] Consulte en la sección [Anatomía de una canalización](data-factory-create-pipelines.md#anatomy-of-a-pipeline) las propiedades JSON que se usan en el ejemplo anterior.

## Definición de variables globales

En Azure PowerShell, ejecute los comandos siguientes después de reemplazar los valores por los suyos propios:

> [AZURE.IMPORTANT] Consulte la sección [Requisitos previos](#prerequisites) para obtener instrucciones sobre cómo obtener el identificador de cliente, el secreto del cliente, el identificador de inquilino y el identificador de suscripción.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "ADFCopyTutorialDF"

## Autenticación con AAD 
Ejecute el siguiente comando para autenticarse con Azure Active Directory (AAD).

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 

## Creación de Data Factory

En este paso, creará una instancia de Data Factory de Azure llamada **ADFCopyTutorialDF**. Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos de un almacén de datos de origen a uno de destino. Una actividad de Hive de HDInsight para ejecutar un script de Hive y convertir los datos de entrada en datos de salida del producto. Ejecute los siguientes comandos para crear la factoría de datos:

1. Asigne el comando a la variable denominada **cmd**.

	Confirme que el nombre de la factoría de datos que especifique aquí (ADFCopyTutorialDF) coincide con el nombre especificado en el archivo **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si la factoría de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.

		Write-Host $results

Tenga en cuenta lo siguiente:
 
- El nombre de la Factoría de datos de Azure debe ser único de forma global. Si ve el error en los resultados: **El nombre “ADFCopyTutorialDF” de factoría de datos no está disponible**, realice lo siguiente:
	1. Cambie el nombre (por ejemplo, suNombreADFCopyTutorialDF) en el archivo **datafactory.json**.
	2. En el primer comando donde se asigna un valor a la variable **$cmd**, reemplace ADFCopyTutorialDF por el nuevo nombre y ejecute el comando.
	3. Ejecute los dos comandos siguientes para invocar la API de REST para crear la factoría de datos e imprimir los resultados de la operación.
	
	Consulte el tema [Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura para los artefactos de Data Factory.
- Para crear instancias de Data Factory, debe ser administrador o colaborador de la suscripción de Azure.
- El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, que sea visible públicamente.
- Si recibe el error: "**La suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**", realice una de las acciones siguientes e intente publicarla de nuevo:

	- En Azure PowerShell, ejecute el siguiente comando para registrar el proveedor de Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Puede ejecutar el comando siguiente para confirmar que se ha registrado el proveedor de Data Factory.
	
			Get-AzureRmResourceProvider
	- Inicie sesión mediante la suscripción de Azure en el [Portal de Azure](https://portal.azure.com) y vaya a una hoja de Data Factory (o) cree una factoría de datos en el Portal de Azure. Esta acción registra automáticamente el proveedor.

Antes de crear una canalización, debe crear algunas entidades de factoría de datos primero. Cree unos servicios vinculados para vincular almacenes de datos de origen y destino a su almacén de datos. A continuación, defina los conjuntos de datos de entrada y salida para representar datos en almacenes de datos vinculados. Por último, cree la canalización con una actividad que utilice estos conjuntos de datos.

## Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser Almacenamiento de Azure, Base de datos SQL de Azure o Base de datos SQL Server local que contenga los datos de entrada o almacene los datos de salida de una canalización de Factoría de datos. Un servicio de proceso es el servicio que procesa datos de entrada y genera datos de salida.

En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **AzureSqlLinkedService**. El servicio vinculado StorageLinkedService vincula una cuenta de Almacenamiento de Azure, y AzureSqlLinkedService vincula una instancia de Base de datos SQL de Azure a la factoría de datos **ADFTutorialDataFactoryPSH**. Más adelante en este tutorial, creará una canalización que copia datos de un contenedor de blobs de AzuretStorageLinkedService en una tabla SQL de AzureSqlLinkedService.

### Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Con este tutorial, usará la cuenta de almacenamiento de Azure para almacenar los datos de entrada.

1. Asigne el comando a la variable denominada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el servicio vinculado se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results

### Creación de un servicio vinculado SQL de Azure
En este paso, vinculará su cuenta de Base de datos SQL de Azure con su factoría de datos. Con este tutorial, usará la misma Base de datos SQL de Azure para almacenar los datos de salida.

1. Asigne el comando a la variable denominada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el servicio vinculado se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results

## Creación de conjuntos de datos

En el paso anterior, creó los servicios vinculados **AzureStorageLinkedService** y **AzureSqlLinkedService** para vincular una cuenta de Almacenamiento de Azure y Base de datos SQL de Azure a la factoría de datos **ADFTutorialDataFactory**. En este paso, creará conjuntos de datos que representan los datos de entrada y salida de la actividad de copia en la canalización que va a crear en el paso siguiente.

El conjunto de datos de entrada de este tutorial hace referencia a un contenedor de blobs del Almacenamiento de Azure al que apunta StorageLinkedService. El conjunto de datos de salida hace referencia a una tabla SQL de la base de datos SQL de Azure al que apunta AzureSqlLinkedService.

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
Realice los siguientes pasos para preparar Almacenamiento de blobs de Azure y Base de datos SQL de Azure para este tutorial.
 
* Cree un contenedor de blobs con el nombre **adftutorial** en la instancia de Almacenamiento de blobs de Azure a la que apunta **StorageLinkedService**.
* Cree y cargue un archivo de texto, **emp.txt**, como un blob para el contenedor **adftutorial**.
* Cree una tabla llamada **emp** en la Base de datos SQL de Azure a la que apunta **AzureSqlLinkedService**.


1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\\ADFGetStartedPSH** de su disco duro.

        John, Doe
		Jane, Doe
				
2. Use herramientas como el [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Explorador de almacenamiento de Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Use el siguiente script de SQL para crear la tabla **emp** en su Base de datos SQL de Azure.


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Si tiene SQL Server 2014 instalado en el equipo: siga las instrucciones del artículo [Paso 2: Conexión con la base de datos SQL de la base de datos de administración de SQL de Azure con SQL Server Management Studio][sql-management-studio] para conectarse al servidor SQL de Azure y ejecutar el script de SQL.

	Si el cliente no tiene permiso para acceder al servidor SQL de Azure, tendrá que configurar el firewall de su servidor SQL de Azure para permitir el acceso desde su máquina (dirección IP). Consulte [este artículo](../sql-database/sql-database-configure-firewall-settings.md) para conocer los pasos que deben darse para configurar el firewall de un servidor SQL de Azure.
		
### Creación de un conjunto de datos de entrada 
En este paso, creará un conjunto de datos denominado **AzureBlobInput** que apunta a un contenedor de blobs en Almacenamiento de Azure que está representado por el servicio vinculado **AzureStorageLinkedService**. Este contenedor de blobs (**adftutorial**) contiene los datos de entrada en el archivo: **emp.txt**.

1. Asigne el comando a la variable denominada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results

### Creación del conjunto de datos de salida
En este paso, creará una tabla de salida denominada **AzureSqlOutput**. Este conjunto de datos apunta a una tabla SQL (**emp**) de la Base de datos SQL de Azure representada por **AzureSqlLinkedService**. La canalización copia los datos del blob de entrada a la tabla **emp**.

1. Asigne el comando a la variable denominada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.
  
		Write-Host $results 

## Creación de una canalización
En este paso, creará una canalización con una **actividad de copia** que utiliza **AzureBlobInput** como entrada y **AzureSqlOutput** como salida.

1. Asigne el comando a la variable denominada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Ejecute el comando con **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Vea los resultados. Si el conjunto de datos se ha creado correctamente, verá su JSON en los **resultados**; de lo contrario, verá un mensaje de error.

		Write-Host $results

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, con una canalización que copia datos desde Almacenamiento de blobs de Azure a Base de datos SQL de Azure.

## Supervisión de la canalización
En este paso, utilizará la API de REST de Data Factory para supervisar los segmentos que la canalización está produciendo.

	$ds ="AzureSqlOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;


	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Ejecute estos comandos hasta que vea un segmento en estado **Listo** o **Error**. Cuando el segmento está en estado Listo, compruebe la tabla **emp** en Base de datos SQL de Azure para los datos de salida.

Para cada segmento, se copian dos filas de datos del archivo de origen en la tabla emp de Base de datos SQL de Azure. Por tanto, podrá ver 24 nuevos registros en la tabla emp cuando todos los segmentos se procesan correctamente (en estado Listo).


## Resumen
En este tutorial, ha usado una API de REST para crear una factoría de datos de Azure para copiar datos de un blob de Azure en una Base de datos SQL de Azure. Estos son los pasos de alto nivel que realizó en este tutorial:

1.	Ha creado una **factoría de datos** de Azure.
2.	Ha creado **servicios vinculados**:
	1. Un servicio vinculado Almacenamiento de Azure para vincular la cuenta de Almacenamiento de Azure que contiene datos de entrada.
	2. Un servicio vinculado SQL Azure para vincular la base de datos SQL de Azure que contiene los datos de salida.
3.	Ha creado **conjuntos de datos** que describen los datos de entrada y salida para las canalizaciones.
4.	Ha creado una **canalización** con una actividad de copia con un origen BlobSource y un receptor SqlSink.

## Otras referencias
| Tema. | Description |
| :---- | :---- |
| [Actividades de movimiento de datos](data-factory-data-movement-activities.md) | En este artículo se proporciona información detallada sobre la actividad de copia que se usa en el tutorial. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a conocer las canalizaciones y actividades de Data Factory de Azure y cómo aprovecharlas para construir flujos de trabajo controlados por datos de un extremo a otro para su escenario o negocio. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayuda a comprender los conjuntos de datos de Data Factory de Azure.
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0831_2016-->