<properties
	pageTitle="Compilación de la primera canalización mediante Visual Studio"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo mediante Visual Studio."
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

# Compilación de la primera canalización mediante Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


En este artículo, aprenderá a usar Visual Studio para crear su primera canalización. Este tutorial consta de los siguientes pasos:

2.	Creación de los servicios vinculados (almacenes de datos, procesos).
3.	Creación de un conjunto de datos.
3.	Creación de una canalización
4.	Cree una factoría de datos e implementar servicios vinculados, el conjunto de datos y la canalización. 

Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Revise el artículo [Información general del tutorial](data-factory-build-your-first-pipeline.md) y realice los pasos de requisitos previos antes de completar este tutorial.

## Tutorial: Creación e implementación de las entidades de Factoría de datos con Visual Studio 

### Requisitos previos

Debe tener instalado en el equipo lo siguiente: - Visual Studio 2013 - Descargue el SDK de Azure para Visual Studio 2013. Vaya a la [página de descargas de Azure](http://azure.microsoft.com/downloads/) y haga clic en el **vínculo de descarga de VS 2013** en la sección **.NET** - Actualice las "Herramientas de Visual Studio para Factoría de datos de Azure" a la versión más reciente; para ello, vaya a Visual Studio 2013--> Herramientas--> Actualizaciones--> Galería de Visual Studio y haga clic en "Actualizar" en la entrada del complemento "Herramientas de Visual Studio para Factoría de datos de Azure".


### Creación del proyecto de Visual Studio 
1. Inicie **Visual Studio 2013**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debe aparecer el cuadro de diálogo **Nuevo proyecto**.  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de factoría de datos**. Si no ve la plantilla DataFactory, cierre Visual Studio, instale el SDK de Azure para Visual Studio 2013 y vuelva a abrir Visual Studio.  

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; a continuación, haga clic en **Aceptar**.

	![Explorador de soluciones](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Crear servicios vinculados
En este paso, vinculará su cuenta de Almacenamiento de Azure y un clúster de HDInsight de Azure a petición a su factoría de datos y luego creará un conjunto de datos que represente los datos de salida del procesamiento de Hive.


#### Creación de un servicio vinculado de Almacenamiento de Azure


4. Haga clic con el botón derecho en **Servicios vinculados** en el Explorador de soluciones, seleccione **Agregar** y haga clic en **Nuevo elemento**.      
5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Servicios vinculados de Almacenamiento de Azure** en la lista y haga clic en **Agregar**. 

	![Nuevo servicio vinculado](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Reemplace **accountname** y **accountkey** por el nombre de la cuenta de almacenamiento de Azure y su clave.

	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Guarde el archivo **AzureStorageLinkedService1.json**.

#### Creación de un servicio vinculado de HDInsight de Azure
Ahora, va a crear un servicio vinculado para un clúster de HDInsight a petición que se usará para ejecutar el script de Hive.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Servicios vinculados**, seleccione **Agregar** y haga clic en **Nuevo elemento**.
2. Seleccione **Servicio vinculado de HDInsight a petición** y haga clic en **Agregar**. 
3. Reemplace el código **JSON** por el siguiente:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
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

4. Guarde el archivo **HDInsightOnDemandLinkedService1.json**.
 
### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos almacenados en el almacenamiento de blobs de Azure.

1. En el **Explorador de soluciones**, haga clic con el botón derecho y seleccione **Agregar**, y haga clic en **Nuevo elemento**. 
2. Seleccione **Blob de Azure** en la lista y haga clic en **Agregar**. 
3. Reemplace el código **JSON** en el editor por el siguiente: en el fragmento de código JSON, se crea un conjunto de datos llamado **AzureBlobOutput** y se especifica la estructura de los datos que generará el script de Hive. Además, se especifica que los resultados se almacenan en el contenedor de blobs llamado **data** y la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Guarde el archivo **AzureBlobLocation1.json**.


### Creación de la primera canalización
En este paso, creará la primera canalización:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en **Canalizaciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**. 
2. Seleccione **Canalización de transformación de Hive** en la lista y haga clic en **Agregar**. 
3. Reemplace el código **JSON** por el siguiente fragmento.

	> [AZURE.IMPORTANT]Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
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
	
	El archivo de script de Hive, **partitionweblogs.hql** se almacena en la cuenta de almacenamiento de Azure (especificada mediante scriptLinkedService, denominada **AzureStorageLinkedService1**) y en un contenedor denominado **script**.

	La sección **extendedProperties** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:PartitionedData}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de la canalización.

	En el JSON de actividad, se especifica que el script de Hive se ejecuta en el proceso especificado por el servicio vinculado: **HDInsightOnDemandLinkedService**.
3. Guarde el archivo **HiveActivity1.json**.

### Agregar partitionweblogs.hql como una dependencia 

1. Haga clic con el botón derecho en **Dependencias** de la ventana del **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Elemento existente**.  
2. Navegue hasta **C:\\ADFGettingStarted**, seleccione el archivo **partitionweblogs.hql** y haga clic en **Agregar**. 

Al publicar la solución en el paso siguiente, se carga el archivo HQL en el contenedor de scripts del almacenamiento de blobs.

### Publicación e implementación de las entidades de Factoría de datos

18. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
20. Debería ver el siguiente cuadro de diálogo:

	![Cuadro de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. En la página Configurar la factoría de datos, haga lo siguiente:
	1. Seleccione la opción **Crear nueva factoría de datos**.
	2. Escriba **FirstPipelineUsingVS** para **Nombre**.
	3. Seleccione la suscripción correcta para el campo **Suscripción**. 
	4. Seleccione el **grupo de recursos** para la factoría de datos que se va a crear. 
	5. Seleccione la **región** para la factoría de datos. 
	6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**. (Presione el **tabulador** para salir del campo Nombre si el botón **Siguiente** está deshabilitado). 
23. En la página **Publicar elementos**, asegúrese de que todas las factorías de datos están seleccionadas y haga clic en **Siguiente** para pasar a la página **Resumen**.     
24. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página **Estado de implementación**, debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar. 
 

## Uso del Explorador de servidores para revisar las entidades de Factoría de datos

1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y expanda **Factoría de datos**. Si ve **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure en su suscripción. Verá el estado de esta operación en la ventana **Lista de tareas de Factoría de datos**.

	![Explorador de servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar **Exportar Factoría de datos al nuevo proyecto** para crear un proyecto de Visual Studio basado en una factoría de datos existente.

	![Exportación de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Actualización de herramientas de Factoría de datos para Visual Studio

Para actualizar las herramientas de Factoría de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en **Herramientas** en el menú y seleccione **Extensiones y actualizaciones**.
2. Seleccione **Actualizaciones** en el panel izquierdo y, luego, seleccione **Galería de Visual Studio**.
3. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Consulte [Supervisión de los conjuntos de datos y canalización](data-factory-monitor-manage-pipelines.md) para obtener instrucciones sobre cómo usar el Portal de vista previa de Azure para supervisar la canalización y los conjuntos de datos que creó en este tutorial.
 

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Si quiere ver cómo se usa una actividad de copia para copiar datos de un blob de Azure a SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-get-started.md).
  
## Enviar comentarios
Agradecemos sus comentarios sobre este artículo. Dedique unos minutos a enviar sus comentarios por [correo electrónico](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=Oct15_HO2-->