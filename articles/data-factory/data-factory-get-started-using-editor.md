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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Tutorial: Crear y supervisar un generador de datos mediante el Editor del generador de datos
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Apartados de este tutorial
Este tutorial contiene los siguientes pasos:

Paso | Descripción
-----| -----------
[Paso 1: Crear un generador de datos de Azure](#CreateDataFactory) | En este paso, creará un generador de datos de Azure denominado **ADFTutorialDataFactory**.  
[Paso 2: Crear servicios vinculados](#CreateLinkedServices) | En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. El StorageLinkedService vincula el almacenamiento de Azure y AzureSqlLinkedService vincula la base de datos de SQL Azure para el ADFTutorialDataFactory. Los datos de entrada para la canalización residen en un contenedor de blob en el almacenamiento de blobs de Azure y datos de salida se almacenará en una tabla en la base de datos de SQL Azure. Por lo tanto, agregue estos almacenes de datos de dos como servicios vinculados en el generador de datos.      
[Paso 3: Crear la entrada y salida de las tablas](#CreateInputAndOutputDataSets) | En el paso anterior, creó servicios vinculados que hacen referencia a los almacenes de datos que contienen datos de entrada y salida. En este paso, definirá dos tablas de generador de datos-- **EmpTableFromBlob** y **EmpSQLTable** --que representan los datos de entrada y salida se almacenan en los almacenes de datos. Para el EmpTableFromBlob, debe especificar el contenedor de blob que contiene un blob con el origen de datos y para el EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida. También debe especificar otras propiedades como la estructura de los datos, la disponibilidad de los datos, etc.... 
[Paso 4: Crear y ejecutar una canalización](#CreateAndRunAPipeline) | En este paso, va a crear una canalización con nombre **ADFTutorialPipeline** en la ADFTutorialDataFactory. La canalización tendrá un **copia actividad** que copia de entrada de datos de Azure blob en la tabla SQL Azure de salida.
[Paso 5: Supervisar intervalos y canalizaciones](#MonitorDataSetsAndPipeline) | En este paso, va a supervisar los sectores de las tablas de entrada y salidas con Portal de vista previa de Azure.
 

## <a name="CreateDataFactory"></a>Paso 1: Crear un generador de datos de Azure
En este paso, se utilizará el Portal de vista previa de Azure para crear un generador de datos de Azure denominado **ADFTutorialDataFactory**.

1.	Tras iniciar sesión en la [Portal de vista previa de Azure][azure-preview-portal], haga clic en **nuevo** en la esquina inferior izquierda, seleccione **análisis de datos** en el **crear** blade y haga clic en **factoría de datos** en el **análisis de datos** blade. 

	![Nuevo -> DataFactory][image-data-factory-new-datafactory-menu]

6. En el **Nuevo generador de datos** módulo:
	1. Escriba **ADFTutorialDataFactory** para el **nombre**. 
	
  		![Nuevo módulo de generador de datos][image-data-factory-getstarted-new-data-factory-blade]
	2. Haga clic en **nombre del grupo de recursos** y realice lo siguiente:
		1. Haga clic en **crear un nuevo grupo de recursos**.
		2. En el **Crear grupo de recursos** blade, escriba **ADFTutorialResourceGroup** para el **nombre** del grupo de recursos y haga clic en **Aceptar**. 

			![Crear grupo de recursos][image-data-factory-create-resource-group]

		Algunos de los pasos en este tutorial se supone que usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener información acerca de los grupos de recursos, consulte [uso de grupos de recursos para administrar los recursos de Azure](resource-group-overview.md).  
7. En el **Nuevo generador de datos** blade, tenga en cuenta que **Agregar al panel de inicio** está seleccionada.
8. Haga clic en **crear** en el **Nuevo generador de datos** blade.

	> [AZURE.NOTE]El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **nombre del generador de datos "ADFTutorialDataFactory" no está disponible**, cambie el nombre del generador de datos (por ejemplo, yournameADFTutorialDataFactory) y que intente crear de nuevo. Use este nombre en lugar de ADFTutorialFactory mientras realiza los pasos restantes de este tutorial. Consulte [factoría de datos - reglas de nomenclatura][data-factory-naming-rules] tema de las reglas de nomenclatura para los artefactos de generador de datos.
	 
	![Nombre del generador de datos no está disponible][image-data-factory-name-not-available]

9. Haga clic en **notificaciones** concentrador a la izquierda y busque las notificaciones desde el proceso de creación. Haga clic en **X** para cerrar la **notificaciones** blade si está abierto.
10. Una vez completada la creación, verá el **factoría de datos** blade tal como se muestra a continuación.

    ![Página principal de generador de datos][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Paso 2: Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, una base de datos SQL de Azure o una base de datos SQL Server local.

En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. StorageLinkedService vinculados a vínculos de servicio una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos de SQL Azure a la **ADFTutorialDataFactory**. Más adelante en este tutorial que copia datos de un contenedor de blob en StorageLinkedService a una tabla SQL en AzureSqlLinkedService creará una canalización.

### Crear un servicio vinculado para la cuenta de almacenamiento de Azure
1.	En el **factoría de datos** blade, haga clic en **autor e implementar** mosaico para iniciar la **Editor** para el generador de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

	> [AZURE.NOTE]Consulte [Editor del generador de datos][data-factory-editor] tema de información general detallada del editor de generador de datos.
	 
5. En el **Editor**, haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **almacenamiento de Azure** desde el menú desplegable. Debería ver la plantilla JSON para crear un servicio de almacenamiento de Azure vinculado en el panel derecho.

	![Botón de almacén de datos nuevo editor][image-editor-newdatastore-button]
    
6. Reemplace **accountname** y **accountkey** con el nombre de cuenta y valores de clave de cuenta para la cuenta de almacenamiento de Azure.

	![Almacenamiento de blobs de editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.

6. Haga clic en **implementar** en la barra de herramientas para implementar el StorageLinkedService. Confirme que aparece el mensaje **vinculado el servicio creado correctamente** en la barra de título.

	![Implementar el almacenamiento de blobs de editor][image-editor-blob-storage-deploy]

### Crear un servicio vinculado para la base de datos de SQL Azure
1. En el **Editor del generador de datos** , haga clic en **nuevo almacén de datos** botón en la barra de herramientas y seleccione **base de datos de SQL Azure** desde el menú desplegable. Debería ver la plantilla JSON para crear el servicio SQL Azure vinculado en el panel derecho.

	![Configuración de SQL Azure Editr][image-editor-azure-sql-settings]

2. Reemplace **nombreDeServidor**, **databasename**, **username@servername**, y **contraseña** con los nombres de servidor, base de datos, cuenta de usuario y contraseña de SQL Azure. 
3. Haga clic en **implementar** en la barra de herramientas para crear e implementar el AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Paso 3: Crear la entrada y salida de las tablas
En el paso anterior, creó servicios vinculados **StorageLinkedService** y **AzureSqlLinkedService** para vincular una cuenta de almacenamiento de Azure y una base de datos de SQL Azure para el generador de datos: **ADFTutorialDataFactory**. En este paso, definirá dos tablas de generador de datos-- **EmpTableFromBlob** y **EmpSQLTable** --que representan los datos de entrada y salida se almacenan en los almacenes de datos al que hace referencia StorageLinkedService y AzureSqlLinkedService respectivamente. Para EmpTableFromBlob, debe especificar el contenedor de blob que contiene un blob con el origen de datos y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida.

### Creación de la tabla de entrada 
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla denominada **EmpBlobTable** que señala a un contenedor de blob en el almacenamiento de Azure representado por la **StorageLinkedService** vinculados de servicio.

1. En el **Editor** para el generador de datos, haga clic en **nuevo conjunto de datos** en la barra de herramientas y haga clic en botón **tabla Blob** desde el menú desplegable. 
2. Reemplace JSON en el panel derecho con el siguiente fragmento JSON: 

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
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Tenga en cuenta lo siguiente:
	
	- ubicación **tipo** está establecido en **AzureBlobLocation**.
	- **linkedServiceName** está establecido en **StorageLinkedService**. Este servicio vinculado se hubiera creado en el paso 2.
	- **folderPath** está establecido en el **adftutorial** contenedor. También puede especificar el nombre de un blob en la carpeta. Puesto que no se especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
	- formato **tipo** está establecido en **TextFormat**
	- Hay dos campos en el archivo de texto **FirstName** y **LastName** separados por un carácter de coma (** columnDelimiter **)	
	- El **disponibilidad** está establecido en **cada hora** (** frecuencia ** se establece en **hora** y **intervalo** está establecido en **1** ), por lo que el servicio de generador de datos buscará los datos de entrada cada hora en la carpeta raíz en el contenedor de blob (** adftutorial **) especificado. 
	

	Si no especifica un **nombre de archivo** para un **entrada** **tabla**, todos los archivos o blobs desde la carpeta de entrada (** folderPath **) se consideran como entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Ver los archivos de ejemplo en el [tutorial][adf-tutorial] para obtener ejemplos.
 
	Si no se especifica un **nombre de archivo** para un **tabla de salida**, el genera los archivos de la **folderPath** se denominan con el siguiente formato: datos. & lt; GUID & gt;. txt (ejemplo: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt.).

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

2. Haga clic en **implementar** en la barra de herramientas para crear e implementar la **EmpTableFromBlob** tabla. Confirme que aparece la **tabla creado correctamente** mensaje en la barra de título del Editor.

### Creación de la tabla de salida
En esta parte del paso, va a crear una tabla de resultados denominada **EmpSQLTable** que señala a una tabla SQL en SQL Azure base de datos está representado por la **AzureSqlLinkedService** vinculados de servicio.

1. En el **Editor** para el generador de datos, haga clic en **nuevo conjunto de datos** en la barra de herramientas y haga clic en botón **tabla SQL Azure** desde el menú desplegable. 
2. Reemplace JSON en el panel derecho con el siguiente fragmento JSON:

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
            		"type": "AzureSqlTableLocation",
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
	* **linkedServiceName** está establecido en **AzureSqlLinkedService** (este servicio vinculado hubiera creado en el paso 2).
	* **tablename** está establecido en **emp**.
	* Hay tres columnas: **ID de**, **FirstName**, y **LastName** : en la tabla emp en la base de datos, pero el identificador es una columna de identidad, por lo que necesitará especificar sólo **FirstName** y **LastName** aquí.
	* El **disponibilidad** está establecido en **cada hora** (** frecuencia ** establecido en **hora** y **intervalo** establecido en **1**). El servicio de generador de datos generará un segmento de datos de salida cada hora en el **emp** tabla en la base de datos de SQL Azure.


3. Haga clic en **implementar** en la barra de herramientas para crear e implementar la **EmpSQLTable** tabla.


## <a name="CreateAndRunAPipeline"></a>Paso 4: Crear y ejecutar una canalización
En este paso, se crea una canalización con un **copia actividad** que utiliza **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1. En el **Editor** para el generador de datos, haga clic en **nueva canalización** en la barra de herramientas. Haga clic en **... (puntos suspensivos)** en la barra de herramientas si no ve el botón. O bien, haga clic con el botón secundario **Canalizaciones** en la vista de árbol y elija **Nueva canalización**.

	![Botón de canalización nuevo editor][image-editor-newpipeline-button]
 
2. Reemplace JSON en el panel derecho con el siguiente fragmento JSON:

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Tenga en cuenta lo siguiente:

	- En la sección actividades, hay sólo una actividad cuya **tipo** está establecido en **CopyActivity**.
	- Entrada de la actividad se establece en **EmpTableFromBlob** y de salida de la actividad se establece en **EmpSQLTable**.
	- En el **transformación** sección, **BlobSource** se especifica como el tipo de origen y **SqlSink** se especifica como el tipo de receptor.

	> [AZURE.NOTE]Sustituya el valor de la **iniciar** propiedad con la fecha actual y **end** valor con el día siguiente. Puede especificar sólo la parte de fecha y omitir la parte de hora de la fecha y hora. Por ejemplo, "2015-02-03", que es equivalente a "2015-02-03T00:00:00Z" ambos iniciar y fechas y horas de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. El **end** tiempo es opcional, pero se utilizará en este tutorial. Si no especifica el valor para el **end** propiedad, se calcula como "** inicio + 48 horas **". Para ejecutar la canalización de forma indefinida, especifique **9999-09-09** como el valor de la **end** propiedad. En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
	> [AZURE.NOTE]Consulte [referencia de secuencias de comandos de JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obtener más información acerca de las propiedades JSON.

4. Haga clic en **implementar** en la barra de herramientas para crear e implementar la **ADFTutorialPipeline**. Confirme que aparece la **canalización creado correctamente** mensaje.
5. Ahora, cierre el **Editor** blade haciendo clic en **X**. Haga clic en **X** para cerrar el cuadro de ADFTutorialDataFactory con la vista de árbol y de la barra de herramientas. Si ve **se descartarán los cambios no guardados** de mensajes, haga clic en **Aceptar**.
6. Debe volver a la **factoría de datos** módulo para la **ADFTutorialDataFactory**.

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.
 
### Vista del generador de datos en una vista de diagrama 
1. En el **factoría de datos** blade, haga clic en **diagrama**.

	![Módulo de generador de datos - icono diagrama][image-datafactoryblade-diagramtile]

2. Debería ver un diagrama similar al siguiente:

	![Vista de diagrama][image-data-factory-get-started-diagram-blade]

	Puede acercar, alejar, zoom al 100%, zoom ajustar automáticamente colocar las canalizaciones y tablas y mostrar información de linaje (resalta elementos ascendente y descendentes de los elementos seleccionados). Puede blick de doble en un objeto (tabla de entrada y salida o canalización) para ver las propiedades para ella. 
3. Haga doble clic en **ADFTutorialPipeline** en la vista de diagrama y haga clic en **abrir canalización**. Debería ver las actividades en la canalización, junto con los conjuntos de datos de entrada y salida para las actividades. En este tutorial, tener sólo una actividad en la canalización (actividad copia) con EmpTableBlob como conjunto de datos de entrada y EmpSQLTable como conjunto de datos de salida.   

	![Canalización abierto](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Haga clic en **factoría de datos** en la ruta de exploración en la esquina superior izquierda para volver a la vista de diagrama. La vista de diagrama muestra todas las canalizaciones. En este ejemplo, solo ha creado una canalización.
 

## <a name="MonitorDataSetsAndPipeline"></a>Paso 5: Supervisar la canalización y los conjuntos de datos
En este paso, usará el Portal de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure. También puede usar los cmdlets de PowerShell para supervisar los conjuntos de datos y las canalizaciones. Para obtener más información acerca del uso de cmdlets para la supervisión, consulte [Monitor y administrar datos de fábrica con Cmdlets de PowerShell][monitor-manage-using-powershell].

1. Vaya a [Portal de Azure (vista previa)][azure-preview-portal] si no tiene abierta. 
2. Si el módulo para **ADFTutorialDataFactory** no está abierto, ábralo haciendo clic en **ADFTutorialDataFactory** en el **panel de inicio**. 
3. Deben mostrarse el recuento y los nombres de las tablas y la canalización que creó en esta hoja.

	![página principal con nombres][image-data-factory-get-started-home-page-pipeline-tables]

4. Ahora, haga clic en **conjuntos de datos** de mosaico.
5. En el **conjuntos de datos** blade, haga clic en **EmpTableFromBlob**. Se trata de la tabla de entrada para la **ADFTutorialPipeline**.

	![Conjuntos de datos con EmpTableFromBlob seleccionado][image-data-factory-get-started-datasets-emptable-selected]   
5. Observe que ya se han producido las rebanadas de datos hasta la hora actual y son **Listo** porque la **emp.txt** archivo existe todo el tiempo en el contenedor de blobs: **adftutorial\input**. Confirme que los sectores no aparecen en la **error recientemente sectores** sección en la parte inferior.

	Ambos **actualizado recientemente sectores** y **error recientemente sectores** listas se ordenan por el **hora de última actualización**. En las situaciones siguientes, se cambia la hora de un intervalo de actualización.
    

	-  Actualiza el estado del sector manualmente, por ejemplo, utilizando la **conjunto AzureDataFactorySliceStatus** (o), haga clic en **ejecutar** en el **sector** módulo para el sector.
	-  El segmento cambia estado debido a una ejecución (por ejemplo, una ejecución iniciado, una ejecución finalizado y no se pudo, una ejecución finaliza y se realizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** Para ver la lista más amplia de sectores. Haga clic en **filtro** en la barra de herramientas para filtrar los sectores.
	
	Para ver los intervalos de datos ordenados por los tiempos de inicio y fin del sector en su lugar, haga clic en **rebanadas de datos (por hora de sector)** en mosaico.

	![Segmentos de datos por intervalo de tiempo][DataSlicesBySliceTime]

6. Ahora, en la **conjuntos de datos** blade, haga clic en el **EmpSQLTable**. Se trata de la tabla de salida para la **ADFTutorialPipeline**.

	![módulo de conjuntos de datos][image-data-factory-get-started-datasets-blade]



	 
6. Debería ver el **EmpSQLTable** blade tal como se muestra a continuación:

	![cuadro de tabla][image-data-factory-get-started-table-blade]
 
7. Observe que ya se han producido las rebanadas de datos hasta la hora actual y son **Listo**. Los sectores no aparecen en la **sectores problema** sección en la parte inferior.
8. Haga clic en **... (Puntos suspensivos)** Para ver todos los sectores.

	![módulo de sectores de datos][image-data-factory-get-started-dataslices-blade]

9. Haga clic en cualquier segmento de datos de la lista y debería ver el **segmento de datos** blade.

	![módulo de segmento de datos][image-data-factory-get-started-dataslice-blade]
  
	Si no se encuentra en el sector del **Listo** estado, puede ver los intervalos de nivel superior que no están listos y están bloqueando el intervalo actual de la ejecución en el **intervalos de nivel superior que no están listos** lista.

11. En el **segmento de datos** blade, debería ver toda la actividad se ejecuta en la lista en la parte inferior. Haga clic en un **actividad se ejecute** para ver el **Detalles de ejecución de la actividad** blade.

	![Detalles de ejecución de actividad][image-data-factory-get-started-activity-run-details]

	
12. Haga clic en **X** para cerrar todos los módulos hasta que regrese al blade principal para la **ADFTutorialDataFactory**.
14. (opcional) Haga clic en **canalizaciones** en la página principal de **ADFTutorialDataFactory**, haga clic en **ADFTutorialPipeline** en el **canalizaciones** blade y obtención de detalles de las tablas de entrada (** consumidos **) o tablas de salida (** producido **).
15. Iniciar **SQL Server Management Studio**, conectarse a la base de datos de SQL Azure y compruebe que las filas se insertan en la **emp** tabla en la base de datos.

	![resultados de la consulta SQL][image-data-factory-get-started-sql-query-results]


## Resumen 
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Usar el Portal de vista previa de Azure para crear el generador de datos, servicios vinculados, tablas y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:

1.	Crear un Azure **factoría de datos**.
2.	Crear **vinculado servicios** que vinculan los almacenes de datos y calcula (denominadas **servicios vinculados**) para el generador de datos.
3.	Crear **tablas** que describen de entrada de datos y los datos de salida de las canalizaciones.
4.	Crear **canalizaciones**. Una canalización consta de una o varias actividades y procesa las entradas y produce salidas. Establecer el período activo para la canalización especificando **iniciar** tiempo y **End** el momento en que la canalización. El período activo define la duración de tiempo en que se producirán segmentos de datos. 


> [AZURE.NOTE]Para obtener una lista de actividades admitidas, vea [canalizaciones y las actividades][msdn-activities] tema y para obtener una lista de servicios vinculados admitidos, consulte [servicios vinculados][msdn-linkedservices] tema en MSDN Library.
> 
> Para realizar este tutorial con Azure PowerShell, consulte [monitor un generador de datos con Azure PowerShell y crear][monitor-manage-using-powershell].

## Pasos siguientes

Artículo | Descripción
------ | ---------------
[Copiar los datos con el generador de datos de Azure - actividad de copia][copy-activity] | Este artículo proporciona una descripción detallada de la **copia actividad** utilizados en este tutorial. 
[Habilitar las canalizaciones trabajar con datos locales][use-onpremises-datasources] | Este artículo tiene un tutorial que muestra cómo copiar datos desde una **base de datos de SQL Server local** a un blob de Azure. 
[Tutorial: Mover y procesar los archivos de registro mediante el generador de datos][adf-tutorial] | Este artículo proporciona un **-to-end tutorial** que muestra cómo implementar un **escenario real** mediante el generador de datos de Azure para transformar los datos de archivos de registro en perspectivas.
[Solucionar problemas del generador de datos][troubleshoot] | Este artículo se describe cómo **solucionar** problemas del generador de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminando la tabla de la Base de datos SQL de Azure). 
[Referencia del programador de generador de datos de Azure][developer-reference] | La referencia del programador tiene el contenido de referencia completa de cmdlets, script JSON, funciones, etc.... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 