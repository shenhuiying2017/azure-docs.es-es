<properties
	pageTitle="Compilación de la primera canalización mediante el Editor de la Factoría de datos"
	description="En este tutorial, creará una canalización de Factoría de datos de Azure de ejemplo con el Editor de la Factoría de datos en el Portal de Azure clásico."
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
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Compilación de la primera canalización de Factoría de datos de Azure mediante el Editor de la Factoría de datos (Portal de Azure clásico)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


En este artículo, aprenderá a usar el [Portal de Azure](https://portal.azure.com/) para crear la primera canalización. Este tutorial consta de los siguientes pasos:

1.	Creación de la factoría de datos
2.	Creación de los conjuntos de datos y servicios vinculados (almacenes de datos, procesos)
3.	Creación de la canalización

Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Revise el artículo [Información general del tutorial](data-factory-build-your-first-pipeline.md) y realice los pasos de requisitos previos antes de completar este tutorial.

## Paso 1: Creación de la factoría de datos

1.	Tras iniciar sesión en el [Portal de Azure](http://portal.azure.com/), haga lo siguiente:
	1.	Haga clic en **NUEVO** en el menú de la izquierda. 
	2.	Haga clic en **Análisis de datos** en la hoja **Creación**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

		![Hoja Creación](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	En la hoja **Nueva factoría de datos**, escriba **DataFactoryMyFirstPipeline** como nombre.

	![Hoja Nueva Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "DataFactoryMyFirstPipeline" no está disponible**, cambie el nombre de la factoría de datos (por ejemplo, yournameDataFactoryMyFirstPipeline) e intente crearlo de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	>  
	> El nombre de la factoría de datos se puede registrar como nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

3.	Si no creó ningún grupo de recursos, tendrá que crearlo. Para ello, siga estos pasos:
	1.	Haga clic en **NOMBRE DEL GRUPO DE RECURSOS**.
	2.	Seleccione **Crear un nuevo grupo de recursos** en la hoja **Grupo de recursos**.
	3.	Escriba **ADF** como **Nombre** en la hoja **Crear grupo de recursos**.
	4.	Haga clic en **Aceptar**.
	
		![Creación de un grupo de recursos](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Una vez seleccionado el grupo de recursos, compruebe que usa la suscripción correcta en la que quiere crear la factoría de datos.
5.	Haga clic en **Crear** en la hoja **Nueva factoría de datos**.
6.	Verá que la factoría de datos se crea en el **Panel de inicio** del Portal de Azure de la manera siguiente:   

	![Creación de estado de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. ¡Enhorabuena! Ya creó correctamente su primera factoría de datos. Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma. 	

	![Hoja de la Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

En los pasos siguientes, aprenderá a crear los servicios vinculados, los conjuntos de datos y la canalización que va a usar en este tutorial.

## Paso 2: Creación de conjuntos de datos y servicios vinculados
En este paso, vinculará su cuenta de almacenamiento de Azure y un clúster de HDInsight de Azure a petición a su factoría de datos y luego creará un conjunto de datos que represente los datos de salida del procesamiento de Hive.

### Creación de un servicio vinculado de Almacenamiento de Azure
1.	Haga clic en **Crear e implementar** en la hoja **FACTORÍA DE DATOS** para **DataFactoryFirstPipeline**. Esto inicia el Editor de la Factoría de datos. 
	 
	![Icono Crear e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Haga clic en **Nuevo almacén de datos** y elija **Almacenamiento de Azure**
	
	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Debería ver el script JSON para crear un servicio vinculado de Almacenamiento de Azure en el editor. 
4. Reemplace **account name** por el nombre de la cuenta de almacenamiento de Azure y **account key** por la clave de acceso de la cuenta de almacenamiento de Azure. Para aprender cómo obtener una clave de acceso de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
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
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:
	
	Propiedad | Descripción
	-------- | -----------
	Versión | Con esto se especifica que la versión de HDInsight se crea para que sea 3.2. 
	ClusterSize | Así se crea un clúster de HDInsight de un nodo. 
	TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine.
	linkedServiceName | Especifica la cuenta de almacenamiento que se usará para almacenar los registros que genere HDInsight.
3. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado. 
4. Confirme que StorageLinkedService y HDInsightOnDemandLinkedService aparecen en la vista de árbol de la izquierda.

	![Vista de árbol con servicios vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos almacenados en el almacenamiento de blobs de Azure.

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.  

	![Nuevo conjunto de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1. En el fragmento de código JSON, cree un conjunto de datos llamado **AzureBlobOutput** y especifique la estructura de los datos que generará el script de Hive. Además, especifique que los resultados se almacenan en el contenedor de blobs llamado **data** y en la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
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

1. En el **Editor de la Factoría de datos**, haga clic en el botón de puntos suspensivos **(...)** y, a continuación, en **Nueva canalización**.
	
	![Botón Nueva canalización](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1.

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
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
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
	
	El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de almacenamiento de Azure (especificada mediante scriptLinkedService, denominada **StorageLinkedService**) y en un contenedor llamado **script**.

	La sección **defines** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:PartitionedData}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de esta.

	En el JSON de actividad, se especifica que el script de Hive se ejecuta en el proceso que especifica el servicio vinculado (**HDInsightOnDemandLinkedService**).
3. Haga clic en **Implementar** en la barra de comandos para implementar la canalización.
4. Confirme que la canalización aparece en la vista de árbol.

	![Vista de árbol con canalización](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Enhorabuena, ya creó correctamente su primera canalización.
6. Haga clic en **X** para cerrar las hojas del Editor de la Factoría de datos y volver a la hoja de la Factoría de datos y, a continuación, haga clic en **Diagrama**.
  
	![Icono Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. En la Vista de diagrama, verá información general de las canalizaciones y conjuntos de datos empleados en este tutorial.
	
	![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. En la Vista de diagrama, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que el segmento se está procesando.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Cuando finalice el procesamiento, verá el segmento con el estado **Listo**. Tenga en cuenta que la creación de un clúster de HDInsight a petición normalmente tarda algún tiempo. 

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Cuando el segmento se encuentre en el estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **data** del almacenamiento de blobs.  
 

 

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Para ver cómo se usa una actividad de copia para copiar datos de un blob de Azure en SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure en SQL Azure](./data-factory-get-started.md).
  

<!---HONumber=AcomDC_1203_2015-->