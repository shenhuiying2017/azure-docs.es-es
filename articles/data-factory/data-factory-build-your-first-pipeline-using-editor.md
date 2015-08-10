<properties
	pageTitle="Compilación de la primera canalización mediante la Factoría de datos de Azure"
	description="En este tutorial se muestra cómo crear una canalización de datos de ejemplo que transforma los datos con HDInsight de Azure y el Editor de la Factoría de datos."
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
	ms.topic="hero-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Compilación de la primera canalización mediante la Factoría de datos de Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


En este artículo, aprenderá a usar el [Portal de vista previa de Azure](https://portal.azure.com/) para crear la primera canalización. Este tutorial consta de los siguientes pasos:

1.	Creación de la factoría de datos
2.	Creación de los conjuntos de datos y servicios vinculados (almacenes de datos, procesos)
3.	Creación de la canalización

Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

## Paso 1: Creación de la factoría de datos

1.	Tras iniciar sesión en el [Portal de vista previa de Azure](http://portal.azure.com/), haga lo siguiente:
	1.	Haga clic en **NUEVO** en el menú de la izquierda. 
	2.	Haga clic en **Análisis de datos** en la hoja **Creación**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

		![Hoja Creación](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	En la hoja **Nueva factoría de datos**, escriba **DataFactoryMyFirstPipeline** como nombre.

	![Hoja Nueva Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] 
3.	Si no creó ningún grupo de recursos, tendrá que crearlo. Para ello, siga estos pasos:
	1.	Haga clic en **NOMBRE DEL GRUPO DE RECURSOS**.
	2.	Seleccione **Crear un nuevo grupo de recursos** en la hoja **Grupo de recursos**.
	3.	Escriba **ADF** como **Nombre** en la hoja **Crear grupo de recursos**.
	4.	Haga clic en **Aceptar**.
	
		![Creación de un grupo de recursos](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Una vez seleccionado el grupo de recursos, compruebe que usa la suscripción correcta en la que quiere crear la factoría de datos.
5.	Haga clic en **Crear** en la hoja **Nueva factoría de datos**.
6.	Verá que la factoría de datos se crea en el **Panel de inicio** del Portal de vista previa de Azure, de la manera siguiente:   

	![Creación de estado de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. ¡Enhorabuena! Ya creó correctamente su primera factoría de datos. Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma. 	

	![Hoja de la Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

En los pasos siguientes, aprenderá a crear los servicios vinculados, los conjuntos de datos y la canalización que va a usar en este tutorial.

## Paso 2: Creación de conjuntos de datos y servicios vinculados
En este paso, vinculará su cuenta de Almacenamiento de Azure y un clúster de HDInsight de Azure a petición a su factoría de datos y luego creará un conjunto de datos que represente los datos de salida del procesamiento de Hive.

### Creación de un servicio vinculado de Almacenamiento de Azure
1.	Haga clic en **Crear e implementar** en la hoja **FACTORÍA DE DATOS** para **DataFactoryFirstPipeline**. Esto inicia el Editor de la Factoría de datos. 
	 
	![Icono Crear e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Haga clic en **Nuevo almacén de datos** y elija **Almacenamiento de Azure**
	
	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Debería ver el script JSON para crear un servicio vinculado de Almacenamiento de Azure en el editor. 
4. Reemplace **accountname** por el nombre de la cuenta de almacenamiento de Azure y **accountkey** por la clave de acceso de la cuenta de almacenamiento de Azure. Para obtener información sobre cómo obtener la clave de acceso de almacenamiento, vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys)
5. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

	![Botón Implementar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Creación de un servicio vinculado de HDInsight de Azure
Ahora, va a crear un servicio vinculado para un clúster de HDInsight a petición que se usará para ejecutar el script de Hive.

1. En el **Editor de Factoría de datos**, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición**.

	![Nuevo proceso](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1. El fragmento de código JSON describe las propiedades que se usarán para crear el clúster de HDInsight a petición. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:05:00",
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
3. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado. 
4. Confirme que StorageLinkedService y HDInsightOnDemandLinkedService aparecen en la vista de árbol de la izquierda.

	![Vista de árbol con servicios vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos almacenados en el almacenamiento de blobs de Azure.

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.  

	![Nuevo conjunto de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1. En el fragmento de código JSON, se crea un conjunto de datos llamado **AzureBlobOutput** y se especifica la estructura de los datos que generará el script de Hive. Además, se especifica que los resultados se almacenan en el contenedor de blobs llamado **data** y la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
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

3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos recién creado.
4. Compruebe que el conjunto de datos se creó correctamente.

	![Vista de árbol con servicios vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Paso 3: Creación de la primera canalización
En este paso, creará la primera canalización:

1. En el **Editor de la Factoría de datos**, haga clic en el botón de puntos suspensivos **(...)** y luego en **Nueva canalización**.
	
	![Botón Nueva canalización](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1.

	> [AZURE.IMPORTANT]Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento en el código JSON.

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
 
	En el fragmento de código JSON, se crea una canalización que consta de una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.
	
	El archivo de script de Hive, **partitionweblogs.hql** se almacena en la cuenta de almacenamiento de Azure (especificada mediante scriptLinkedService, denominada ** StorageLinkedService**) y en un contenedor denominado **script**.

	La sección **extendedProperties** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:PartitionedData}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de la canalización.

	En el JSON de actividad, se especifica que el script de Hive se ejecuta en el proceso especificado por el servicio vinculado: **HDInsightOnDemandLinkedService**.
3. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.
4. Confirme que la canalización aparece en la vista de árbol.

	![Vista de árbol con canalización](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Enhorabuena, ya creó correctamente su primera canalización.
6. Haga clic en **X** para cerrar las hojas del Editor de la Factoría de datos y volver a la hoja Factoría de datos, y haga clic en **Diagrama**.
  
	![Icono Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. En la Vista de diagrama, verá información general de las canalizaciones y conjuntos de datos empleados en este tutorial.
	
	![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. En la Vista de diagrama, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que el segmento se está procesando.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Cuando finalice el procesamiento, verá el segmento con el estado **Listo**. Tenga en cuenta que la creación de un clúster de HDInsight a petición normalmente tarda algún tiempo. 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Cuando el segmento se encuentre en el estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **data** del almacenamiento de blobs.  
 

 

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Si desea ver cómo se usa una actividad de copia para copiar datos de un Blob de Azure en SQL Azure, vea [Tutorial: Copia de datos de un blob de Azure a SQL Azure](./data-factory-get-started.md).
  

<!---HONumber=July15_HO5-->