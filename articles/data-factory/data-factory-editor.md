<properties 
	pageTitle="Editor de la Factoría de datos de Azure" 
	description="Describe el Editor de la Factoría de datos de Azure, que permite crear, editar e implementar las definiciones de JSON de servicios vinculados, tablas y canalizaciones mediante una interfaz de usuario ligera basada en web ligera." 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Editor de la Factoría de datos de Azure
El Editor de la Factoría de datos de Azure es un editor web ligero que forma parte del Portal de vista previa de Azure y que permite crear, editar e implementar archivos JSON de todas las entidades de la Factoría de datos de Azure. Esto le permite crear servicios vinculados, conjuntos de datos y canalizaciones mediante las plantillas de JSON que se distribuyen con el servicio Factoría de datos. Si es nuevo en PowerShell, esto elimina la necesidad de instalar y refuerza Azure PowerShell para crear generadores de datos de Azure.

## Inicio del Editor de la Factoría de Datos
Para iniciar el Editor de la Factoría de datos, haga clic en el mosaico **Crear e implementar** de la hoja **Factoría de datos** para su factoría de datos de Azure.

![Mosaico Crear e implementar][author-and-deploy-tile]

Aparecerá el Editor de la Factoría de datos, tal como se muestra en la siguiente imagen:
 
![Editor de la Factoría de datos][data=factory-editor]
 
## Creación de nuevos servicios vinculados, conjuntos de datos y canalizaciones
Hay cuatro botones en la barra de herramientas que puede usar para crear entidades de la Factoría de datos de Azure.
 
- **Nuevo almacén de datos** para crear un servicio vinculado al almacén de datos. Al hacer clic en este botón, aparecerá un menú con las siguientes opciones: almacenamiento de Azure, base de datos SQL de Azure y base de datos local de SQL server.
- **Nuevo proceso** para crear un servicio vinculado a un proceso. Al hacer clic en este botón, aparecerá un menú con las siguientes opciones: clúster de HDInsight a petición, clúster de HDInsight, el servicio vinculado del Aprendizaje automático de Azure.      
- **Nuevo conjunto de datos** para crear un conjunto de datos. Al hacer clic en este botón, verá las siguientes opciones: tabla de blobs, tabla de SQL de Azure y tabla local.  
- **Nueva canalización** para crear una canalización. Haga clic en **... (puntos suspensivos)** en la barra de herramientas si no ve el botón.
 
### Para crear un servicio de almacenamiento vinculado
1. Haga clic en **Nuevo almacén de datos** y elija una de las opciones del menú.
 
	![Menú Nuevo almacén de datos][new-data-store-menu] 
2. Verá la plantilla JSON para crear un servicio de almacenamiento almacenado en el **lienzo del Editor** a la derecha. También verá que aparece un nodo de borrador en **Borradores**. Haga lo siguiente:
	1. En **Almacenamiento de Azure**: reemplace **<accountname>** y **<accountkey >** por el nombre y la clave de su cuenta de almacenamiento de Azure.
	2. En **Base de datos SQL de Azure**: reemplace **<servername>** por el nombre del servidor SQL de Azure, **<databasename >** por el nombre de la base de datos, **<username>@<servername>** por el nombre del usuario y **<password>** por la contraseña de la cuenta de usuario. 
	3. En **Base de datos local de SQL Server**: reemplace **<servername>** por el nombre del servidor SQL local, **<databasename >** por el nombre de la base de datos, **<username>** por el nombre del usuario y **<password>** por la contraseña de la cuenta de usuario.
4. Haga clic en **Implementar** en la barra de herramientas para implementar el servicio vinculado. Puede hacer clic en **Descartar** para descartar el borrador JSON que ha creado.
 
	![Botón Implementar][deploy-button]

1. Debería ver el estado de la operación de implementación en la barra de título.

	![Mensaje de implementación correcta][deploy-success-message]
2. Si la operación de implementación se realiza correctamente, debería ver el servicio vinculado que ha creado en la vista de árbol a la izquierda.
  
	![StorageLinkedService en la vista de árbol][storagelinkedservice-in-treview]

### Para crear un servicio vinculado a un proceso
1. Haga clic en **Nuevo proceso** y elija una de las opciones del menú.
 
	![Menú Nuevo proceso][new-compute-menu] 
2. Verá la plantilla JSON para crear un servicio vinculado a un proceso en el lienzo del Editor a la derecha. Haga lo siguiente:
	1. En **Clúster de HDInsight a petición**, especifique los valores para las propiedades siguientes: 
		1. En la propiedad **clusterSize**, especifique el tamaño del clúster de HDInsight que desea que el servicio Factoría de datos cree en tiempo de ejecución. 
		2. En la propiedad **jobsContainer**, especifique el nombre del contenedor de blobs predeterminado donde desea que se almacenen los registros de clúster.
		3. En la propiedad **timeToLive**, especifique el tiempo de inactividad permitido antes de que se elimine el clúster de HDInsight. Por ejemplo: 00:05:00 indica que el clúster debería eliminarse después de 5 minutos de tiempo de inactividad.
		4. En la propiedad **version**, especifique la versión de HDInsight para el clúster (valor predeterminado: versión 3.1).
		5. En la propiedad **linkedServiceName**, especifique el servicio de almacenamiento vinculado de Azure que se asociará con el clúster de HDInsight. 
	6. En **Clúster de HDInsight** (traiga su propio clúster), especifique los valores para las propiedades siguientes:
		1. En la propiedad **clusterUri**, especifique la dirección URL para su propio clúster de HDInsight. 
		2. En la propiedad **userName**, especifique la cuenta de usuario que el servicio Factoría de datos debe usar para conectarse a su clúster de HDInsight. 
		3. En la propiedad **password**, especifique la contraseña de la cuenta de usuario. 
		4. En la propiedad **linkedServiceName**, especifique el servicio de almacenamiento vinculado que está asociado a su clúster de HDInsight.
	5. En **el servicio vinculado de Aprendizaje automático de Azure**, especifique las siguientes propiedades:
		1. En la propiedad **mlEndPoint**, especifique la dirección URL de puntuación de lotes de Aprendizaje automático de Azure.
		2. En la propiedad **apiKey** propiedad, especifique la clave de API para el modelo del área de trabajo publicado.
3. Haga clic en **Implementar** en la barra de herramientas para implementar el servicio vinculado.

> [AZURE.NOTE]Consulte el tema [Servicios vinculados][msdn-linkedservices-reference] de la Biblioteca de MSDN para obtener descripciones de los elementos JSON que se usan para definir un servicio vinculado de la Factoría de datos de Azure.

### Para crear un nuevo conjunto de datos
1. Haga clic en **Nuevo conjunto de datos** y elija una de las opciones del menú.
2. Verá la plantilla JSON para crear un conjunto de datos en el lienzo del Editor a la derecha. Haga lo siguiente: 
	1. En **Tabla Blob**, especifique los valores para las propiedades siguientes:
	2. En **Tabla SQL de Azure** o **Tabla local**, especifique los valores para las propiedades siguientes: 
		1. En la sección **ubicación**: 
			2. En la propiedad **linkedServiceName**, especifique el nombre del servicio vinculado que hace referencia a la base de datos SQL de Azure SQL o la base de datos local de SQL Server.
			2. En la propiedad **tableName**, especifique el nombre de la tabla en la instancia de la Base de datos SQL de Azure o el SQL Server local a que hace referencia el servicio vinculado.
		3. En la sección **disponibilidad**:
			1. En la propiedad **frequency**, especifique la unidad de tiempo para la producción de segmentos de datos. Valores admitidos para frequency: Minute, Hour, Day, Week, Month.
			2. En la propiedad **interval**, especifique el intervalo de la frecuencia definida. **frequency** establecido en **Hour** e **interval** establecido en **1** indica que se deben generar nuevos segmentos de datos cada hora. 
		3. En la sección **estructura**: 
			1. especifique nombres y tipos de columnas, tal como se muestra en el ejemplo siguiente:
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Consulte el tema [Tablas][msdn-tables-reference] de la Biblioteca de MSDN para obtener descripciones de los elementos JSON que se usan para definir una tabla de la Factoría de datos de Azure.
 		           
### Para crear y activar una canalización 
1. Haga clic en **Nueva canalización** en la barra de herramientas. Si no ve el botón **Nueva canalización**, haga clic en **... (puntos suspensivos)** para verlo.   
2. Verá la plantilla JSON para crear una canalización en el lienzo del Editor a la derecha. Haga lo siguiente: 
	1. En la propiedad **description**, especifique la descripción de la canalización.
	2. En la sección **activities**, agregue actividades a la canalización. Ejemplo:
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
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
	3. En la propiedad **start**, especifique cuándo se inicia el procesamiento de datos o cuándo se procesan los segmentos de datos. Ejemplo: 2014-05-01T00:00:00Z.
	4. En la propiedad **end**, especifique cuándo finaliza el procesamiento de datos. Ejemplo: 2014-05-01T00:00:00Z.       

> [AZURE.NOTE]Consulte el tema [Canalizaciones y actividades][msdn-pipelines-reference] de la Biblioteca de MSDN para obtener descripciones de los elementos JSON que se usan para definir una canalización de la Factoría de datos de Azure.

## Para agregar una definición de actividad a una canalización JSON
Puede agregar una definición de actividad a una canalización JSON haciendo clic en **Agregar actividad** en la barra de herramientas. Al hacer clic en este botón, elija el tipo de actividad que desea agregar a la canalización.

![Agregar opciones de actividad][add-activity-options]

Si desea copiar datos de una base de datos SQL de Azure en el almacenamiento de blobs de Azure y procesar los datos en el almacenamiento de blobs mediante script de Pig en un clúster de HDInsight, primero agregue una **actividad de copia** y, a continuación, agregue una **actividad de Pig** a la canalización. Esto crea dos secciones en la sección de actividades de la canalización JSON. La actividad de Pig es la actividad de HDInsight con transformación de Pig.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## Inicio de una canalización
Puede especificar la fecha y hora de inicio y la fecha y hora de finalización de una canalización especificando valores para las propiedades start y end en el JSON.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## Borradores en el editor
Los borradores permiten guardar temporalmente el trabajo cuando cambia de contexto o navega por otra entidad de la Factoría de datos. El ciclo de vida de los borradores está asociada con la sesión del explorador. Si cierra el explorador o usa otro equipo, los borradores no estarán disponibles.

## Para descartar un borrador JSON de una entidad de la Factoría de datos
Puede descartar la definición de JSON de una entidad de la Factoría de datos de Azure haciendo clic en el botón **Descartar** en la barra de herramientas.

## Para clonar una entidad de la Factoría de Datos
Puede clonar una entidad existente de la Factoría de datos de Azure (servicio vinculado, tabla o canalización) seleccionando la entidad en la vista de árbol y haciendo clic en el botón **Clonar** de la barra de herramientas.

![Clonar una entidad de la Factoría de datos][clone-datafactory-entity]

Aparecerá un nuevo borrador en el nodo **Borradores** de la vista de árbol.

## Para eliminar una entidad de la Factoría de Datos
Para eliminar una entidad de la Factoría de datos de Azure (servicio vinculado, tabla o canalización), seleccione la entidad en la vista de árbol y haga clic en **Eliminar** en la barra de herramientas o haga clic con el botón secundario en la entidad y seleccione **Eliminar**.

![Eliminar una entidad de la Factoría de datos][delete-datafactory-entity]

## Otras referencias
Consulte el tema [Introducción a la Factoría de datos de Azure][data-factory-get-started] para obtener instrucciones paso a paso para crear una factoría de datos de Azure mediante el Editor de la Factoría de datos de Azure.

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=Oct15_HO1-->