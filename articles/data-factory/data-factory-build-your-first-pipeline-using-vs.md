<properties
	pageTitle="Compilación de la primera canalización mediante la Factoría de datos de Azure"
	description="En este tutorial se muestra cómo crear una canalización de datos de ejemplo que transforma los datos con HDInsight de Azure Visual Studio"
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


En este artículo, aprenderá a usar Visual Studio para crear su primera canalización. Este tutorial consta de los siguientes pasos:

1.	Creación de la factoría de datos
2.	Creación de los conjuntos de datos y servicios vinculados (almacenes de datos, procesos)
3.	Creación de la canalización

Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para obtener información general detallada del servicio, vea el artículo [Introducción a la Factoría de datos de Azure](data-factory-introduction.md).

## Paso 1: Creación de la factoría de datos

1.	Tras iniciar sesión en el [Portal de vista previa de Azure](http://portal.azure.com/), haga lo siguiente:
	1.	Haga clic en **NUEVO** en la esquina inferior izquierda. 
	2.	Haga clic en **Análisis de datos** en la hoja **Creación**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

		![Hoja Creación](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	En la hoja **Nueva factoría de datos**, escriba **DataFactoryMyFirstPipeline** como nombre.

	![Hoja Nueva Factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] 
3.	Si no creó ningún grupo de recursos, tendrá que crearlo. Para ello, siga estos pasos:
	1.	Haga clic en **NOMBRE DEL GRUPO DE RECURSOS**.
	2.	Seleccione **Crear un nuevo grupo de recursos** en la hoja **Grupo de recursos**.
	3.	Escriba **ADF** como **Nombre** en la hoja **Crear grupo de recursos**.
	4.	Haga clic en **Aceptar**.
	
		![Creación de un grupo de recursos](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	Una vez seleccionado el grupo de recursos, compruebe que usa la suscripción correcta en la que quiere crear la factoría de datos.
5.	Haga clic en **Crear** en la hoja **Nueva factoría de datos**.
6.	Verá que la factoría de datos se crea en el **Panel de inicio** del Portal de vista previa de Azure, de la manera siguiente:   

	![Creación de estado de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. ¡Enhorabuena! Ya creó correctamente su primera factoría de datos. Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma. 	

	![Hoja de la Factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

En los pasos siguientes, aprenderá a crear los servicios vinculados, los conjuntos de datos y la canalización que va a usar en este tutorial.

## Tutorial: Creación e implementación de las entidades de Factoría de datos con Visual Studio 

### Requisitos previos

Debe tener instalado en el equipo lo siguiente: - Visual Studio 2013 - Descargue el SDK de Azure para Visual Studio 2013. Vaya a la [Página de descargas de Azure](http://azure.microsoft.com/downloads/) y haga clic en **Instalación de VS 2013**en la sección **.NET**.


### Creación del proyecto de Visual Studio 
1. Inicie **Visual Studio 2013**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debería ver el cuadro de diálogo **Nuevo proyecto**.  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de factoría de datos**. Si no ve la plantilla DataFactory, cierre Visual Studio, instale el SDK de Azure para Visual Studio 2013 y vuelva a abrir Visual Studio.  

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; luego haga clic en **Aceptar**.

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
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
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
2. Seleccione **Blob de Azure** en la lista y haga clic en**Agregar**. 
3. Reemplace el código **JSON** en el editor por el siguiente: en el fragmento de código JSON, se crea un conjunto de datos llamado **AzureBlobOutput** y se especifica la estructura de los datos que generará el script de Hive. Además, se especifica que los resultados se almacenan en el contenedor de blobs llamado **data** y la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
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
3. Reemplace el código **JSON** por el siguiente fragmento de código y reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento.

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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
3. Guarde el archivo **HiveActivity1.json**. 

### Publicación e implementación de las entidades de Factoría de datos
  
1. En el área de la barra de herramientas, haga clic con el botón derecho y seleccione **Factoría de datos** para habilitar la barra de herramientas de Factoría de datos si aún no está habilitada. 
19. En la **barra de herramientas Factoría de datos**, haga clic en el **cuadro desplegable** para ver todos las factorías de datos de su suscripción de Azure. Si ve el cuadro de diálogo **Iniciar sesión en Visual Studio**: 
	20. Escriba la **cuenta de correo electrónico** asociada a la suscripción de Azure en la que desea crear la factoría de datos, escriba la **Contraseña** y haga clic en **Iniciar sesión**.
	21. Una vez iniciada la sesión correctamente, verá todas las factorías de datos de la suscripción de Azure. En este tutorial, creará una nueva factoría de datos.       
22. En la lista desplegable, seleccione **DataFactoryMyFirstPipeline** y haga clic en el botón **Publicar** para implementar o publicar los servicios vinculados, los conjuntos de datos y la canalización.    

	![Botón Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. Debería ver el estado de la publicación en la ventana **Lista de tareas de Factoría de datos** que se muestra en la ilustración anterior. Confirme que la publicación se ha realizado correctamente.


## Uso del Explorador de servidores para revisar las entidades de Factoría de datos

1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y expanda **Factoría de datos**. Si ve **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure en su suscripción. Verá el estado de esta operación en la ventana **Lista de tareas de Factoría de datos**.

	![Explorador de servidores](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar ** Exportar Factoría de datos al nuevo proyecto** para crear un proyecto de Visual Studio basado en una factoría de datos existente.

	![Exportación de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Actualización de herramientas de Factoría de datos para Visual Studio

Para actualizar las herramientas de Factoría de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en **Herramientas** en el menú y seleccione **Extensiones y actualizaciones**.
2. Seleccione **Actualizaciones** en el panel izquierdo y luego elija **Galería de Visual Studio**.
3. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Vea [Supervisión de los conjuntos de datos y la canalización](data-factory-monitor-manage-pipelines.md) para obtener instrucciones sobre el uso del Portal de vista previa de Azure para supervisar la canalización y los conjuntos de datos que creó en este tutorial.
 

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Si desea ver cómo se usa una actividad de copia para copiar datos de un Blob de Azure en SQL Azure, vea [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-get-started.md).
  

<!---HONumber=July15_HO5-->