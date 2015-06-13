<properties 
	pageTitle="Tutorial: crear y supervisar un generador de datos de Azure con Azure PowerShell" 
	description="Aprenda a usar Azure PowerShell para crear y supervisar los generadores de datos de Azure." 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Tutorial: Crear y supervisar un generador de datos con Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

El [empezar a trabajar con el generador de datos de Azure][adf-get-started] tutorial muestra cómo crear y supervisar un generador de datos de Azure mediante el [Portal de vista previa de Azure][azure-preview-portal]. En este tutorial, creará y supervisar un generador de datos de Azure mediante cmdlets de PowerShell de Azure. La canalización en el generador de datos que se crea en este tutorial copia los datos de un Azure blob en una base de datos de SQL Azure.

> [AZURE.NOTE]Este artículo no abarca todos los cmdlets de generador de datos. Consulte [referencia de cmdlets de generador de datos][cmdlet-reference] para obtener documentación completa sobre los cmdlets de generador de datos.

##Requisitos previos
Además de requisitos previos que se enumeran en el tema de información general del Tutorial, debe tener instalado en el equipo de Azure PowerShell. Si no tiene ya, descargue e instale [Azure PowerShell][download-azure-powershell] en el equipo.

##Apartados de este tutorial
En la tabla siguiente se enumera los pasos que se llevará a cabo como parte de este tutorial y sus descripciones.

Paso | Descripción
-----| -----------
[Paso 1: Crear un generador de datos de Azure](#CreateDataFactory) | En este paso, creará un generador de datos de Azure denominado **ADFTutorialDataFactoryPSH**. 
[Paso 2: Crear servicios vinculados](#CreateLinkedServices) | En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. El StorageLinkedService vincula un almacenamiento de Azure y AzureSqlLinkedService vincula el ADFTutorialDataFactoryPSH una base de datos de SQL Azure.
[Paso 3: Crear la entrada y salida de los conjuntos de datos](#CreateInputAndOutputDataSets) | En este paso, definirá dos conjuntos de datos (** EmpTableFromBlob ** y **EmpSQLTable**) que se utilizan como tablas de entrada y salidas para el **copia actividad** en el ADFTutorialPipeline que creará en el paso siguiente.
[Paso 4: Crear y ejecutar una canalización](#CreateAndRunAPipeline) | En este paso, va a crear una canalización con nombre **ADFTutorialPipeline** en el generador de datos: **ADFTutorialDataFactoryPSH**... La canalización tendrá un **actividad de copia** que copia datos de un Azure blob a una tabla de base de datos de Azure de salida.
[Paso 5: Supervisar los conjuntos de datos y canalización](#MonitorDataSetsAndPipeline) | En este paso, va a supervisar los conjuntos de datos y la canalización con Azure PowerShell en este paso.

## <a name="CreateDataFactory"></a>Paso 1: Crear un generador de datos de Azure
En este paso, uso de Azure PowerShell para crear un generador de datos de Azure denominado **ADFTutorialDataFactoryPSH**.

1. Iniciar **Azure PowerShell** y ejecute los comandos siguientes. Mantenga el Azure PowerShell abierta hasta el final de este tutorial. Si cierra y vuelve a abrir, deberá volver a ejecutar estos comandos.
	- Ejecutar **Add-AzureAccount** y escriba el nombre de usuario y la contraseña que utilizas para iniciar sesión en el Portal de vista previa de Azure.  
	- Ejecutar **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecutar **Select-AzureSubscription** para seleccionar la suscripción que desea trabajar. Esta suscripción debe ser la misma que la usada en el Portal de vista previa de Azure. 
2. Cambiar a **AzureResourceManager** están disponibles en este modo de modo que los cmdlets del generador de datos de Azure.

		Switch-AzureMode AzureResourceManager 
3. Crear un grupo de recursos de Azure denominado: **ADFTutorialResourceGroup** ejecute el comando siguiente.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algunos de los pasos en este tutorial se supone que usa el grupo de recursos denominado **ADFTutorialResourceGroup**. Si utiliza un grupo de recursos distinto, debe usar en lugar de ADFTutorialResourceGroup en este tutorial. 
4. Ejecute el **nueva AzureDataFactory** cmdlet para crear un generador de datos con nombre: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **nombre del generador de datos "ADFTutorialDataFactoryPSH" no está disponible**, cambie el nombre (por ejemplo, yournameADFTutorialDataFactoryPSH). Use este nombre en lugar de ADFTutorialFactoryPSH mientras realiza los pasos de este tutorial.

## <a name="CreateLinkedServices"></a>Paso 2: Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, base de datos de SQL Azure o una base de datos de SQL Server local que contiene los datos de entrada o almacena los datos de salida de una canalización del generador de datos. Un servicio de proceso es el servicio que procesa datos de entrada y genera datos de salida.

En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. StorageLinkedService vinculados a vínculos de servicio una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos de SQL Azure para el generador de datos: **ADFTutorialDataFactoryPSH**. Más adelante en este tutorial que copia datos de un contenedor de blob en StorageLinkedService a una tabla SQL en AzureSqlLinkedService creará una canalización.

### Creación de un servicio vinculado para una cuenta de almacenamiento de Azure
1.	Cree un archivo JSON denominado **StorageLinkedService.json** en la **C:\ADFGetStartedPSH** con el siguiente contenido. Si todavía no existe, cree la carpeta ADFGetStartedPSH.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	En el **Azure PowerShell**, cambie a la **ADFGetStartedPSH** carpeta. 
3.	Puede utilizar el **nueva AzureDataFactoryLinkedService** cmdlet para crear un servicio vinculado. Este cmdlet y otros cmdlets de factoría de datos que se utiliza en este tutorial requieren que se pasen valores la **ResourceGroupName** y **DataFactoryName** parámetros. Como alternativa, puede usar **Get AzureDataFactory** para obtener un objeto DataFactory y pasar el objeto sin necesidad de escribir ResourceGroupName y DataFactoryName cada vez que ejecuta un cmdlet. Ejecute el comando siguiente para asignar el resultado de la **AzureDataFactory Get** cmdlet a una variable: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	Ahora, ejecute el **nueva AzureDataFactoryLinkedService** cmdlet para crear el servicio vinculado: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si no había ejecutado la **AzureDataFactory Get** cmdlet y asigna el resultado a **$df** variable, tendría que especificar los valores de los parámetros ResourceGroupName y DataFactoryName como sigue.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si cierra Azure PowerShell en el centro del tutorial, habrá ejecuta el cmdlet Get-AzureDataFactory próxima vez que ejecute Azure PowerShell para completar el tutorial.

### Creación de un servicio vinculado para una base de datos SQL de Azure
1.	Cree un archivo JSON denominado AzureSqlLinkedService.json con el siguiente contenido.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Ejecute el siguiente comando para crear un servicio vinculado. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Confirme que el **Permitir el acceso a los servicios de Azure** esté activada para el servidor de SQL Azure. Para comprobar y encenderlo, haga lo siguiente:
	>
	> <ol>
<li>Haga clic en <b>Examinar</b> concentrador a la izquierda y haga clic en <b>servidores SQL</b>.</li>
<li>Seleccione el servidor y haga clic en <b>configuración</b> en el <b>SQL SERVER</b> blade.</li>
<li>En el <b>configuración</b> blade, haga clic en <b>Firewall</b>.</li>
<li>En el <b>Firewalll configuración</b> blade, haga clic en <b>ON</b> para <b>Permitir el acceso a los servicios de Azure</b>.</li>
<li>Haga clic en <b>ACTIVE</b> concentrador a la izquierda para cambiar a la <b>factoría de datos</b> estuviera en el módulo.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Paso 3: Crear la entrada y salida de las tablas

En el paso anterior, creó servicios vinculados **StorageLinkedService** y **AzureSqlLinkedService** para vincular una cuenta de almacenamiento de Azure y una base de datos de SQL Azure para el generador de datos: **ADFTutorialDataFactoryPSH**. En este paso, creará las tablas que representan la entrada y salida de datos para la actividad de copia en la canalización que se va a crear en el paso siguiente.

Una tabla es un conjunto de datos rectangular y es el único tipo de conjunto de datos que se admite en este momento. La tabla de entrada en este tutorial refiere a un contenedor de blob en el almacenamiento de Azure que StorageLinkedService apunta a y la tabla de resultados en una tabla SQL en la base de datos de SQL Azure que señala AzureSqlLinkedService.

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
Omita este paso si ha realizado el tutorial de [empezar a trabajar con el generador de datos de Azure][adf-get-started] artículo.

Tendrá que realizar los pasos siguientes para preparar el almacenamiento de blobs de Azure y la base de datos de SQL Azure para este tutorial.
 
* Crear un contenedor de blob denominado **adftutorial** almacenamiento de blobs de Azure que **StorageLinkedService** señala a. 
* Crear y cargar un archivo de texto **emp.txt**, como un blob en el **adftutorial** contenedor. 
* Crear una tabla denominada **emp** en la base de datos de SQL Azure en SQL Azure de base de datos que **AzureSqlLinkedService** señala a.


1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** a **C:\ADFGetStartedPSH** carpeta en el disco duro. 

        John, Doe
		Jane, Doe
				
2. Usar herramientas como [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) para crear la **adftutorial** contenedor y cargar la **emp.txt** archivo al contenedor.

    ![Explorador de almacenamiento de Azure][image-data-factory-get-started-storage-explorer]
3. Use el siguiente script SQL para crear el **emp** tabla en la base de datos de SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Si tiene instalado en el equipo de SQL Server 2014: siga las instrucciones de [paso 2: conectar con base de datos SQL de la administración de SQL de Azure con SQL Server Management Studio][sql-management-studio] artículo para conectarse al servidor SQL Azure y ejecutar la secuencia de comandos SQL.

	Si tiene instalado en el equipo de Visual Studio 2013: en el Portal de vista previa de Azure ([http://portal.azure.com](http://portal.sazure.com)), haga clic en **Examinar** concentrador izquierdo, haga clic en la **servidores SQL Server**, seleccione la base de datos y haga clic en **abierta en Visual Studio** en la barra de herramientas para conectarse al servidor SQL Azure y ejecutar la secuencia de comandos. Si el cliente no tiene permiso para tener acceso al servidor de SQL Azure, necesitará configurar el firewall de su servidor de SQL Azure para permitir el acceso desde su equipo (dirección IP). Consulte el artículo anterior para conocer los pasos configurar el firewall para el servidor de SQL Azure.
		
### Creación de la tabla de entrada 
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla denominada **EmpBlobTable** que señala a un contenedor de blob en el almacenamiento de Azure representado por la **StorageLinkedService** vinculados de servicio. Este contenedor de blobs (** adftutorial **) contiene los datos de entrada en el archivo: **emp.txt**.

1.	Cree un archivo JSON denominado **EmpBlobTable.json** en la **C:\ADFGetStartedPSH** carpeta con el siguiente contenido:

		{
	    	"name": "EmpTableFromBlob",
	        "properties":
	        {
	            "structure":  
	                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "StorageLinkedService"
	            },
	            "availability": 
	            {
	                "frequency": "hour",
	                "interval": 1,
	                "waitonexternal": {}
	                }
	        }
		}
	
	Tenga en cuenta lo siguiente:
	
	- ubicación **tipo** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **StorageLinkedService**. 
	- **folderPath** está establecido en el **adftutorial** contenedor. También puede especificar el nombre de un blob en la carpeta. Puesto que no se especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
	- formato **tipo** está establecido en **TextFormat**
	- Hay dos campos en el archivo de texto **FirstName** y **LastName** separados por un carácter de coma (** columnDelimiter **)	
	- El **disponibilidad** está establecido en **cada hora** (** frecuencia ** se establece en **hora** y **intervalo** está establecido en **1** ), por lo que el servicio de generador de datos buscará los datos de entrada cada hora en la carpeta raíz en el contenedor de blob (** adftutorial **) especificado.

	Si no especifica un **nombre de archivo** para un **entrada** **tabla**, todos los archivos o blobs desde la carpeta de entrada (** folderPath **) se consideran como entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Ver los archivos de ejemplo en el [tutorial][adf-tutorial] para obtener ejemplos.
 
	Si no se especifica un **nombre de archivo** para un **tabla de salida**, el genera los archivos de la **folderPath** se denominan con el siguiente formato: datos. < Guid > .txt (ejemplo: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.

2.	Ejecute el siguiente comando para crear la tabla de generador de datos.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Creación de la tabla de salida
En esta parte del paso, va a crear una tabla de resultados denominada **EmpSQLTable** que señala a una tabla SQL (** emp **) en la base de datos de SQL Azure está representado por la **AzureSqlLinkedService** vinculados de servicio. La canalización copia los datos de blob de entrada a la **emp** tabla.

1.	Cree un archivo JSON denominado **EmpSQLTable.json** en la **C:\ADFGetStartedPSH** carpeta con el contenido siguiente.
		
		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSQLTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     Tenga en cuenta lo siguiente:
	
	* ubicación **tipo** está establecido en **AzureSQLTableLocation**.
	* **linkedServiceName** está establecido en **AzureSqlLinkedService**.
	* **tablename** está establecido en **emp**.
	* Hay tres columnas: **ID de**, **FirstName**, y **LastName** : en la tabla emp en la base de datos, pero el identificador es una columna de identidad, por lo que necesitará especificar sólo **FirstName** y **LastName** aquí.
	* El **disponibilidad** está establecido en **cada hora** (** frecuencia ** establecido en **hora** y **intervalo** establecido en **1**). El servicio de generador de datos generará un segmento de datos de salida cada hora en el **emp** tabla en la base de datos de SQL Azure.

2.	Ejecute el siguiente comando para crear la tabla de generador de datos.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Paso 4: Crear y ejecutar una canalización
En este paso, se crea una canalización con un **copia actividad** que utiliza **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1.	Cree un archivo JSON denominado **ADFTutorialPipeline.json** en la **C:\ADFGetStartedPSH** carpeta con el siguiente contenido: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Tenga en cuenta lo siguiente:

	- En la sección actividades, hay sólo una actividad cuya **tipo** está establecido en **CopyActivity**.
	- Entrada de la actividad se establece en **EmpTableFromBlob** y de salida de la actividad se establece en **EmpSQLTable**.
	- En el **transformación** sección, **BlobSource** se especifica como el tipo de origen y **SqlSink** se especifica como el tipo de receptor.

	> [AZURE.NOTE]Sustituya el valor de la **iniciar** propiedad con la fecha actual y **end** valor con el día siguiente. Ambos iniciar y fechas y horas de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. El **end** tiempo es opcional, pero se utilizará en este tutorial. Si no especifica el valor para el **end** propiedad, se calcula como "** inicio + 48 horas **". Para ejecutar la canalización de forma indefinida, especifique **9/9/9999** como el valor de la **end** propiedad. En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
	> [Referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Ejecute el siguiente comando para crear la tabla de generador de datos. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

## <a name="MonitorDataSetsAndPipeline"></a>Paso 5: Supervisar la canalización y los conjuntos de datos
En este paso, usará Azure PowerShell para supervisar lo que está ocurriendo en un generador de datos de Azure.

1.	Ejecutar **Get AzureDataFactory** y asignar el resultado a una variable $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Ejecutar **Get AzureDataFactorySlice** para obtener detalles acerca de todos los sectores de la **EmpSQLTable**, que es la tabla de salida de la canalización.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Reemplazar parte de fecha, mes y año el **StartDateTime** parámetro con el año, mes y fecha. Debe coincidir con el **iniciar** valor en la canalización de JSON.

	Debe ver 24 sectores, uno para cada hora de 12 A.M. del día actual a las 12 A.M. del día siguiente.
	
	**Primer sector:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Último segmento:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Ejecutar **Get AzureDataFactoryRun** para obtener los detalles de actividad se ejecuta durante un **específico** sector. Cambie el valor de la **StartDateTime** parámetro para que coincida con el **iniciar** tiempo del sector de la salida anterior. El valor de la **StartDateTime** deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Debería ver una salida similar a la siguiente:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]Consulte [referencia de cmdlets de generador de datos][cmdlet-reference] para obtener documentación completa sobre los cmdlets de generador de datos.

## Pasos siguientes

Artículo | Descripción
------ | ---------------
[Copiar los datos con el generador de datos de Azure - actividad de copia][copy-activity] | Este artículo proporciona una descripción detallada de la **copia actividad** utilizados en este tutorial. 
[Habilitar las canalizaciones trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial que muestra cómo copiar datos desde una **base de datos de SQL Server local** a un blob de Azure. 
[Uso de Pig y Hive con el generador de datos][use-pig-and-hive-with-data-factory] | Este artículo tiene un tutorial que muestra cómo usar **HDInsight actividad** para ejecutar una **hive y pig** secuencia de comandos para procesar datos de entrada para generar datos de salida.
[Tutorial: Mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial] | Este artículo proporciona un **-to-end tutorial** que muestra cómo implementar un **escenario real** mediante el generador de datos de Azure para transformar los datos de archivos de registro en perspectivas.
[Utilizar actividades personalizadas en un generador de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear un **actividad personalizada** y su uso en una canalización. 
[Solucionar problemas del generador de datos][troubleshoot] | Este artículo se describe cómo **solucionar** problemas del generador de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminando la tabla de la Base de datos SQL de Azure). 
[Referencia de cmdlets de generador de datos de Azure][cmdlet-reference] | Este contenido de referencia con información detallada sobre todos los **cmdlets del generador de datos**.
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de cmdlets, script JSON, funciones, etc.... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir--> 