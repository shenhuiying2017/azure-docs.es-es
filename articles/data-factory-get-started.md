<properties 
	pageTitle="Introducción al uso de la Factoría de datos de Azure" 
	description="En este tutorial se muestra cómo crear una canalización de datos de ejemplo que copia datos de un blob a una instancia de base de datos de SQL de Azure." 
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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Introducción a la Factoría de datos de Azure
Este artículo le introduce en el uso de la Factoría de datos de Azure. El tutorial de este artículo le muestra cómo crear una factoría de datos de Azure y una canalización en ella con el fin de copiar datos de ejemplo de un almacenamiento de blobs de Azure en una base de datos SQL de Azure.

La lista siguiente proporciona los pasos habituales que los desarrolladores deben realizar: 

1.	Crear una **factoría de datos de Azure**.
2.	Crear **servicios vinculados** para vincular los almacenes de datos y los servicios de proceso con la factoría de datos.  Por ejemplo, un servicio vinculado puede vincular una base de datos SQL de Azure o un clúster de HDInsight con la factoría de datos.
3.	Crear **tablas** que describen los datos de entrada y los datos de salida para las canalizaciones. Las tablas también especifican la ubicación real de los datos dentro de los almacenes de datos vinculados con una factoría de datos. Por ejemplo, una tabla puede especificar el nombre de la tabla SQL de una base de datos SQL de Azure (o) un contenedor de blobs de un blob de Azure. 
4.	Crear **canalizaciones**. Una canalización consiste en una o más actividades que consumen datos de entrada y generan datos de salida. En una actividad de copia se copian los datos de un almacén de datos de origen en un almacén de datos de destino y una actividad de HDInsight procesa los datos de entrada mediante scripts de Hive y Pig.  
5.	Especifique el **período activo** para las canalizaciones (para el procesamiento de datos). El período activo define la duración de tiempo en que se producirán segmentos de datos. Puede especificar la fecha y hora de inicio y final de una canalización (o) puede hacer que se ejecute todo el tiempo.

En este tutorial, aprenderá lo siguiente: 

1.	Usar el **Portal de vista previa de Azure** para crear una factoría de datos de Azure y servicios vinculados para los almacenes de datos.
2.	Usar **Azure PowerShell** para crear tablas y una canalización. El portal no admite la creación de tablas y canalizaciones en esta versión


##Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- Una suscripción de Azure. Para obtener más información acerca de la obtención de una suscripción, vea [Opciones de compra][azure-purchase-options][, Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].
- Descargue e instale [Azure PowerShell][download-azure-powershell] en su equipo.
- Lea el tema [Introducción a la Factoría de datos de Azure][data-factory-introduction].
- Cuenta de almacenamiento de Azure. En este tutorial usará el almacenamiento de blobs como almacén de datos de origen. Vea [Acerca de las cuentas de almacenamiento][data-factory-create-storage] para conocer los pasos para crear un almacenamiento de Azure.
- Base de datos SQL de Azure. En este tutorial creará una base de datos de ejemplo y la usará como almacén de datos de destino. Vea [Cómo crear y configurar una base de datos de SQL Azure][data-factory-create-sql-database] para conocer los pasos para crear una base de datos de SQL Azure.

##Apartados de este tutorial

Paso | Descripción
-----| -----------
[Paso 1: Crear una factoría de datos de Azure](#CreateDataFactory) | En este paso creará una factoría de datos de Azure denominada **ADFTutorialDataFactory**. 
[Paso 2: Crear servicios vinculados](#CreateLinkedServices) | En este paso, creará dos servicios vinculados: **MyBlobStore** y **MyAzureSQLStore**. MyBlobStore vincula un almacenamiento de Azure y MyAzureSQLStore vincula una base de datos SQL de Azure con ADFTutorialDataFactory.
[Paso 3: Crear conjuntos de datos de entrada y salida](#CreateInputAndOutputDataSets) | En este paso, definirá dos conjuntos de datos (**EmpTableFromBlob** y **EmpSQLTable**) que se usan para entrada y salida de la **actividad de copia** en la ADFTutorialPipeline que creará en el siguiente paso.
[Paso 4: Creación y ejecución de una canalización](#CreateAndRunAPipeline) | En este paso, creará una canalización denominada **ADFTutorialPipeline**. La canalización dispondrá de una **actividad de copia** que copia datos de un blob de Azure en una tabla de base de datos de Azure de salida.
[Paso 5: Supervisar conjuntos de datos y canalización](#MonitorDataSetsAndPipeline) | En este paso, supervisará los conjuntos de datos y la canalización con Azure Management Studio.
 


## <a name="CreateDataFactory"></a>Paso 1: Crear una factoría de datos de Azure
En este paso, utilice el Portal de vista previa de Azure para crear una factoría de datos de Azure llamada **ADFTutorialDataFactory**.

1.	Tras iniciar sesión en el [Portal de vista previa de Azure][azure-preview-portal], haga clic en **NUEVO** en la esquina inferior izquierda y haga clic en **Factoría de datos** en la hoja**Nuevo**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Si no aparece **Factoría de datos** en la hoja **Nuevo**, desplácese hacia abajo.  


6. En la hoja **Nueva factoría de datos**:
	1. Escriba **ADFTutorialDataFactory** como **nombre.** 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Haga clic en **NOMBRE DE GRUPO DE RECURSOS** y haga lo siguiente:
		1. Haga clic en **Crear un nuevo grupo de recursos**.
		2. En la hoja **Crear grupo de recursos**, escriba **ADFTutorialResourceGroup** para el **nombre** del grupo de recursos y haga clic en **Aceptar**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado **ADFTutorialResourceGroup**. Si utiliza un grupo de recursos diferentes, deberá usar el grupo de recursos que seleccione aquí en lugar de ADFTutorialResourceGroup.  
7. En la hoja **Nueva factoría de datos**, observe que está seleccionada la opción **Agregar al Panel de inicio**.
8. Haga clic en **Crear** en la hoja **Nueva factoría de datos**.

	> [WACOM.NOTE] El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre del generador de datos "ADFTutorialDataFactory" no está disponible**, cambie el nombre (por ejemplo, yournameADFTutorialDataFactory). Use este nombre en lugar de ADFTutorialFactory mientras lleva a cabo los pasos de este tutorial.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Haga clic en el **Centro de notificaciones** de la izquierda y busque las notificaciones del proceso de creación.
10. Una vez finalizada la creación, verá la hoja Factoría de datos como se muestra a continuación.
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. También puede abrirla desde el **Panel de inicio** como se muestra a continuación haciendo clic en **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Paso 2: Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, una base de datos SQL de Azure o una base de datos SQL Server local.

En este paso, creará dos servicios vinculados: **MyBlobStore** y **MyAzureSQLStore**. El servicio vinculado MyBlobStore vincula una cuenta de almacenamiento de Azure y MyAzureSQLStore vincula una base de datos SQL de Azure con **ADFTutorialDataFactory**. Más adelante en este tutorial creará una canalización que copia datos de un contenedor de blobs de MyBlobStore en una tabla SQL de MyAzureSQLStore.

### Creación de un servicio vinculado para una cuenta de almacenamiento de Azure
1.	En la hoja **FACTORÍA DE DATOS**, haga clic en el cuadro **Servicios vinculados** para iniciar la hoja **Servicios vinculados**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. En la hoja **Servicios vinculados**, haga clic en **Agregar almacén de datos**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. En la hoja **Nuevo almacén de datos**:  
	1. Escriba un **nombre** para el almacén de datos. Para este tutorial, escriba **MyBlobStore** para el **nombre**.
	2. Escriba una **descripción** (opcional) para el almacén de datos.
	3. Haga clic en **Tipo**.
	4. Seleccione **Cuenta de almacenamiento de Azure** y haga clic en **Aceptar**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Volverá a la hoja **Nuevo almacén de datos**, que tiene el siguiente aspecto:

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Escriba el **Nombre de cuenta** y la **Clave de cuenta** para la cuenta de almacenamiento de Azure y haga clic en **Aceptar.**   

6. Cuando haga clic en **Aceptar** en la hoja **Nuevo almacén de datos**, debería ver **myblobstore** en la lista de **ALMACENES DE DATOS** en la hoja **Servicios vinculados**. Compruebe si hay algún mensaje en el centro de **NOTIFICACIONES** (a la izquierda).

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Creación de un servicio vinculado para una base de datos SQL de Azure
1. En la hoja **Servicios vinculados**, haga clic en **Agregar almacén de datos** en la barra de herramientas para agregar otro origen de datos (Base de datos SQL de Azure).
2. En la hoja Nuevo almacén de datos:
	1. Escriba un **nombre** para el almacén de datos. Para este tutorial, escriba **MyAzureSQLStore** como **NOMBRE.** 
	2. Escriba una**DESCRIPCIÓN (opcional)** para el almacén.
	3. Haga clic en **Tipo** y seleccione **Base de datos SQL de Azure**.
3. Escriba **Servidor**, **Base de datos**, **Nombre de usuario** y **contraseña** para la Base de datos SQL de Azure y haga clic en **Aceptar**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. Después de hacer clic en **Aceptar** en la hoja **Nuevo almacén de datos**, verá ambos almacenes en la hoja **Servicios vinculados**.

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Paso 3: Crear tablas de entrada y salida

En el paso anterior, creó los servicios vinculados **MyBlobStore** y **MyAzureSQLStore** para vincular una cuenta de almacenamiento de Azure y una base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactory**. En este paso, creará tablas que representan los datos de entrada y salida para la actividad de copia en la canalización que va a crear en el paso siguiente. 

Una tabla es un conjunto de datos rectangular y es el único tipo de conjunto de datos que se admite en este momento. La tabla de entrada hace referencia a un contenedor de blobs en el almacenamiento de Azure al que apunta MyBlobStore y la tabla de salida hace referencia a una tabla SQL en la base de datos de SQL Azure a la que apunta MyAzureSQLStore.  
 
En este momento, la creación de conjuntos de datos y canalizaciones no se admite mediante el Portal de vista previa de Azure, así que para ello usará cmdlets de PowerShell de Azure. Antes de crear las tablas, primero debe hacer lo siguiente (los pasos detallados se indican a continuación).

* Crear un contenedor de blobs llamado **adftutorial** en el almacenamiento de blobs de Azure al que apunta MyBlobStore. 
* Crear y cargar un archivo de texto **emp.txt**, como un blob al contenedor **adftutorial**. 
* Crear una tabla llamada **emp** en la base de datos SQL de Azure a la que apunta MyAzureSQLStore.
* Crear una carpeta llamada **ADFGetStarted** en el disco duro.  

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\ADFGetStarted** de su disco duro. 

        John, Doe
		Jane, Doe
				
2. Use herramientas como el [explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Use el siguiente script de SQL para crear la tabla **emp** en la base de datos SQL de Azure. Puede utilizar la consola de administración de SQL de Azure para conectarse a una base de datos de SQL de Azure y ejecutar el script de SQL. También puede usar SQL Server Management Studio para realizar esta tarea. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Para iniciar la consola de administración de SQL Azure, haga clic en **ADMINISTRAR** como se muestra en la siguiente imagen:
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Creación de la tabla de entrada 
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla denominada **EmpBlobTable** que apunta a un contenedor de blobs en el almacenamiento de Azure representado por el servicio vinculado **MyBlobStore**.


1. Cree un archivo JSON para una tabla de factoría de datos que representa los datos de emp.txt en el Bloc de notas blob.Launch, copie el siguiente script JSON y guárdelo como EmpBlobTable.json en la carpeta C:\ADFGetStarted.


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
            		"linkedServiceName": "MyBlobStore"
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
	
	- el **tipo** de ubicación se establece en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **MyBlobStore**. Este servicio vinculado lo había creado en el paso 2).
	- **folderPath** se establece en el contenedor **adftutorial**. También puede especificar el nombre de un blob en la carpeta. Puesto que no se especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
	- el **tipo** de formato se establece en **TextFormat**
	- - Hay dos campos en el archivo de texto, **FirstName** y **ColumnName**, separados por un carácter de coma (columDelimiter)	
	- La **availability** está establecida en **hourly** (la **frequency** se establece en **hour** e **interval** en **1**). El servicio de la factoría de datos buscará los datos de entrada cada hora en la carpeta raíz del contenedor de blobs (**adftutorial**) especificado.

	Si no especifica un **nombre de archivo** para una **tabla de entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Consulte los archivos de muestra del [tutorial][adf-tutorial] para ver algunos ejemplos.
 
	Si no especifica un **nombre de archivo** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el folderName se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Para obtener más información acerca de las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de la factoría de datos de Azure están disponibles en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y contraseña que usó para iniciar sesión en el Portal de vista previa de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que desea trabajar. Esta suscripción debe ser la misma que la usada en el Portal de vista previa de Azure.

3. Use el cmdlet **New-AzureDataFactoryTable** para crear la tabla de entrada con el archivo **EmpBlobTable.json**.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Para obtener más información acerca de este cmdlet y otros cmdlets de factoría de datos, vea [Referencia de cmdlets de factoría de datos][cmdlet-reference].
 
### Creación de la tabla de salida
En esta parte del paso, creará una tabla de salida denominada **EmpSQLTable** que apunta a una tabla SQL de la base de datos SQL de Azure que está representada por el servicio vinculado **MyAzureSQLStore**. 

1. Cree un archivo JSON para una tabla de factoría de datos que representa los datos de la base de datos SQL de Azure. Inicie el Bloc de notas, copie el siguiente script JSON y guárdelo como **EmpSQLTable.json** en la carpeta **C:\ADFGetStarted**.



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
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Tenga en cuenta lo siguiente: 
	
	* el **tipo** de ubicación está establecido en **AzureSQLTableLocation**.
	* **linkedServiceName** está establecido en **MyAzureSQLStore** (este servicio vinculado lo había creado en el paso 2).
	* **tablename** está establecido en **emp**.
	* Hay tres columnas: **ID**, **FirstName** y **LastName**: en la tabla emp de la base de datos, pero el id. es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.
	* La **availability** está establecida en **hourly** (frequency está establecido en hour e interval en 1).  El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.


2. En **Azure PowerShell**, ejecute el siguiente comando para crear otra tabla de factoría de datos que represente la tabla **emp** de la base de datos SQL de Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Paso 4: Creación y ejecución de una canalización
En este paso, creará una canalización con una **actividad de copia** que usa **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1. Cree un archivo JSON para la canalización. Inicie el Bloc de notas, copie el siguiente script JSON y guárdelo como **ADFTutorialPipeline.json** en la carpeta **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
        		]
      		}
		} 

	Tenga en cuenta lo siguiente:

	- En la sección de actividades, solo hay una actividad cuyo **tipo** está establecido en **CopyActivity**.
	- La entrada de la actividad está establecida en **EmpTableFromBlob** y la salida en **EmpSQLTable**.
	- En la sección **transformation**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

2. Use el cmdlet **New-AzureDataFactoryPipeline** para crear una canalización mediante el archivo **ADFTutorialPipeline.json** que ha creado.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. Una vez creados los procesos, puede especificar la duración en la que se producirá el procesamiento de datos. Al especificar el período activo de una canalización, se está definiendo la duración de tiempo en el que se procesarán los segmentos de datos basándose en las propiedades de disponibilidad que se han definido para cada tabla de la factoría de datos de Azure.  Ejecute el siguiente comando de PowerShell para establecer el período activo en la canalización y escriba Y para confirmar. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Reemplace el valor de **StartDateTime** por el día actual y el valor de **EndDateTime**por el día siguiente. Tanto StartDateTime como EndDateTime son horas UTC y deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. **EndDateTime** es un valor opcional, pero se utilizará en este tutorial. 
	> Si no especifica **EndDateTime**, se calcula como "**StartDateTime + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de **EndDateTime**.  
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.

4. En el **Portal de Azure**, en la hoja **FACTORÍA DE DATOS** de **ADFTutorialDataFactory**, haga clic en **Diagrama**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Debería ver un diagrama similar al siguiente: (Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja).

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Enhorabuena.** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

## <a name="MonitorDataSetsAndPipeline"></a>Paso 5: Supervisión de los conjuntos de datos y de la canalización
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información acerca del uso de cmdlets para la supervisión, vea [Supervisión y administración de la factoría de datos mediante cmdlets de PowerShell][monitor-manage-using-powershell].

1. Vaya al [Portal de Azure (vista previa)][azure-preview-portal] si no lo ha abierto. 
2. Si la hoja de **ADFTutorialDataFactory** no está abierta, ábrala haciendo clic en **ADFTutorialDataFactory** en el **Panel de inicio.** 
3. Debería ver el número y los nombres de las tablas y de la canalización creada en esta hoja.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Ahora haga clic en **Conjuntos de datos**.
5. En la hoja **Conjuntos de datos**, haga clic en **EmpTableFromBlob**. Esta es la tabla de entrada para **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Observe que ya se han producido los segmentos de datos hasta la hora actual y que están **listos** porque el archivo **emp.txt** existe todo el tiempo en el contenedor de blobs: **adftutorial\input**. Confirme que no aparecen segmentos en la sección de **segmentos problemáticos** de la parte inferior.
6. Ahora, en la hoja **Conjuntos de datos**, haga clic en **EmpSQLTable**. Esta es la tabla de salida para **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Debería ver la hoja **EmpSQLTable** como se muestra a continuación:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Tenga en cuenta que ya se han producido los segmentos de datos hasta el momento actual y están **listos**. No se muestra ningún segmento en la sección de **segmentos con problemas** de la parte inferior.
8. Haga clic en **... (puntos suspensivos)** para ver todos los segmentos.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Haga clic en cualquier segmento de datos de la lista. Debe aparecer la hoja **SEGMENTO DE DATOS**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. En la hoja **SEGMENTO DE DATOS**, verá todas las ejecuciones de actividades de la lista en la parte inferior. Haga clic en una **ejecución de actividad** para ver la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Haga clic en **X** para cerrar todas las hojas hasta que regrese a la hoja inicial de **ADFTutorialDataFactory**.
14. (opcional) Haga clic en **canalizaciones** en la página principal de **ADFTutorialDataFactory**, en **ADFTutorialPipeline** en la hoja **Canalizaciones** y obtenga detalles de las tablas de entrada (**Consumido**) o tablas de salida (**Producido**).
15. Inicie **SQL Server Management Studio**, conéctese a la base de datos SQL de Azure y compruebe que las filas se han insertado en la tabla **emp** de la base de datos.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Resumen 
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado el Portal de vista previa de Azure para crear la factoría de datos y los servicios vinculados. Ha usado cmdlets de PowerShell de Azure para crear tablas y una canalización y, a continuación, ha programado la canalización. Estos son los pasos de alto nivel que realizó en este tutorial:  

1.	Crear una **factoría de datos** de Azure.
2.	Crear **servicios vinculados** que vinculen almacenes de datos y procesos (lo que se conoce como **Servicios vinculados**) con la factoría de datos.
3.	Crear **tablas** que describen los datos de entrada y salida para las canalizaciones.
4.	Crear **canalizaciones**. Una canalización consta de una o varias actividades y procesa las entradas y produce salidas. 
5.	Especifique el **período activo** para las canalizaciones (para el procesamiento de los datos). El período activo define la duración de tiempo en que se producirán segmentos de datos.

## Pasos siguientes

Artículo | Descripción
------ | ---------------
[Tutorial: Mover y procesar los archivos de registro mediante la factoría de datos de Azure][adf-tutorial] | En este artículo se proporciona un **tutorial completo** en el que se muestra cómo implementar un **escenario en tiempo real** mediante el uso de la factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Copia de datos con la Factoría de datos de Azure: actividad de copia][copy-activity] | Este artículo proporciona una descripción detallada de la **actividad de copia** que ha usado en este tutorial.
[Habilitar las canalizaciones para trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial en el que se muestra cómo copiar datos de **una base de datos de SQL Server local** a un blob de Azure.
[Usar Pig y Hive con la factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo tiene un tutorial que muestra cómo utilizar **HDInsight Activity** para ejecutar un script de **hive/pig** para procesar los datos de entrada a fin de producir datos de salida. 
[Usar actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones detalladas para crear una **actividad personalizada** y usarla en una canalización. 
[Supervisión y administración de la Factoría de datos de Azure mediante PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo **supervisar y administrar** una factoría de datos de Azure mediante **cmdlets de Azure PowerShell**. Puede probar los ejemplos del artículo en ADFTutorialDataFactory.
[Solución de problemas de la factoría de datos][troubleshoot] | En este artículo se describe cómo **solucionar** problemas de la factoría de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminación de la tabla en la Base de datos SQL de Azure). 
[Referencia de cmdlets de la factoría de datos de Azure][cmdlet-reference] | Este contenido de referencia tiene detalles acerca de todos los **cmdlets de la factoría de datos**.
[Referencia del desarrollador de la factoría de datos de Azure][developer-reference] | La Referencia del desarrollador tiene el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
