<properties
	pageTitle="Compilación de la primera Data Factory (Portal de Azure) | Microsoft Azure"
	description="En este tutorial, creará una canalización de la factoría de datos de Azure de ejemplo con el Editor de la factoría de datos en el Portal de Azure."
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
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Compilación de la primera Data Factory de Azure con el Portal de Azure/Editor de Data Factory
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-build-your-first-pipeline.md)
- [Uso del Editor de Data Factory](data-factory-build-your-first-pipeline-using-editor.md).
- [Uso de PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Uso de Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Uso de la plantilla de Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)

En este artículo, aprenderá a usar el [Portal de Azure](https://portal.azure.com/) para crear su primera factoría de datos de Azure.

## Requisitos previos

1. **Debe** leer la sección [Tutorial Overview](data-factory-build-your-first-pipeline.md) del artículo y completar los pasos de los requisitos previos antes de continuar.
2. Este artículo no ofrece información general conceptual sobre el servicio Factoría de datos de Azure. Para más información del servicio, le recomendamos que consulte el artículo [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md).

## Creación de Data Factory
Una factoría de datos puede tener una o más canalizaciones. Una canalización puede tener una o más actividades. Por ejemplo, una actividad de copia para copiar datos desde un origen a un almacén de datos de destino o una actividad de Hive de HDInsight para ejecutar un script de Hive que transforme los datos de entrada para generar datos de salida. Comencemos con la creación de la factoría de datos en este paso.

1.	Después de iniciar sesión en el [Portal de Azure](https://portal.azure.com/), siga estos pasos:
	1.	Haga clic en **NUEVO** en el menú de la izquierda.
	2.	Haga clic en **Análisis de datos** en la hoja **Creación**.
	3.	Haga clic en **Factoría de datos** en la hoja **Análisis de datos**.

		![Hoja Creación](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	En la hoja **Nueva factoría de datos**, escriba **GetStartedDF** para el nombre.

	![Hoja Nueva Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "GetStartedDF" no está disponible**, cambie el nombre (por ejemplo, suNombreGetStartedDF) e intente crearla de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	> 
	> El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.
	> 
	> Para crear instancias de Data Factory, debe ser administrador o colaborador en la suscripción de Azure.


3.	Seleccione la **suscripción de Azure** donde desea crear la factoría de datos.
4.	Seleccione un **grupo de recursos** existente o cree uno nuevo. Para este tutorial, cree un grupo de recursos denominado: **ADFGetStartedRG**.
5.	Haga clic en **Crear** en la hoja **Nueva factoría de datos**.
6.	Verá que la factoría de datos se crea en el **Panel de inicio** del Portal de Azure de la manera siguiente:

	![Creación de estado de la factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. ¡Enhorabuena! Ya creó correctamente su primera factoría de datos. Tras crear correctamente la factoría de datos, verá la página Factoría de datos, que muestra el contenido de la misma.

	![Hoja de la Factoría de datos](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de crear una canalización, debe crear algunas entidades de factoría de datos primero. En primer lugar debe crear servicios vinculados para vincular los almacenes de datos y procesos con su almacén de datos, a continuación, definir los conjuntos de datos de entrada y salida para representar los datos en los almacenes de datos vinculados y, finalmente, crear la canalización con una actividad que utilice estos conjuntos de datos.

## Crear servicios vinculados
En este paso, vinculará su cuenta de Almacenamiento de Azure y el clúster de HDInsight de Azure a petición con su factoría de datos. La cuenta de Almacenamiento de Azure contendrá los datos de entrada y salida de la canalización de este ejemplo. Para ejecutar el script de Hive especificado en la actividad de la canalización en este ejemplo, se usa el servicio vinculado de HDInsight. Debe identificar qué datos de almacén o servicios de proceso se usan en el escenario y vincular dichos servicios con la factoría de datos mediante la creación de servicios vinculados.

### Creación de un servicio vinculado de Almacenamiento de Azure
En este paso, vinculará su cuenta de Almacenamiento de Azure con su factoría de datos. Para este tutorial, usará la misma cuenta de Almacenamiento de Azure para almacenar los datos de entrada y salida y el archivo de script de HQL.

1.	Haga clic en **Crear e implementar** en la hoja **FACTORÍA DE DATOS** para **GetStartedDF**. Esto inicia el Editor de la Factoría de datos.
	 
	![Icono Crear e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Haga clic en **Nuevo almacén de datos** y elija **Almacenamiento de Azure**.
	
	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Debería ver el script JSON para crear un servicio vinculado de Almacenamiento de Azure en el editor.
4. Reemplace **accountname** por el nombre de la cuenta de almacenamiento de Azure y **accountkey** por la clave de acceso de la cuenta de almacenamiento de Azure. Para aprender a obtener una clave de acceso de almacenamiento, consulte [Visualización y copia de las claves de acceso de almacenamiento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

	![Botón Implementar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Después de que el servicio vinculado se implemente correctamente, la ventana **Borrador 1** desaparecerá y verá **AzureStorageLinkedService** en la vista de árbol de la izquierda. 
   ![Servicio vinculado de Almacenamiento en el menú](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### Creación de un servicio vinculado de HDInsight de Azure
En este paso, vinculará un clúster de HDInsight a petición con la factoría de datos. El clúster de HDInsight se crea automáticamente en tiempo de ejecución y se elimina después de hacer el procesamiento y de permanecer inactivo durante el período de tiempo especificado.

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición**.

	![Nuevo proceso](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie y pegue el fragmento de código siguiente en la ventana **Borrador 1**. El fragmento de código JSON describe las propiedades que se usarán para crear el clúster de HDInsight a petición.

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
	| Versión | Con esto se especifica que la versión de HDInsight se crea para que sea 3.2. | 
	| ClusterSize | Así se crea un clúster de HDInsight de un nodo. | 
	| TimeToLive | Especifica el tiempo de inactividad del clúster de HDInsight, antes de que se elimine. |
	| linkedServiceName | Especifica la cuenta de almacenamiento que se usará para almacenar los registros que genere HDInsight. |

	Tenga en cuenta lo siguiente:
	
	- Data Factory crea automáticamente un clúster de HDInsight **basado en Windows** con el anterior código JSON. También podría hacer que creara un clúster de HDInsight **basado en Linux**. Consulte [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más detalles.
	- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Consulte [Servicio vinculado de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para más información.
	- El clúster de HDInsight crea un **contenedor predeterminado** en el almacenamiento de blobs especificado en el código JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Esto es así por diseño. Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (**timeToLive**), y se elimina cuando finaliza el procesamiento.
	
		A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. El nombre de estos contenedores siguen un patrón: "adf**nombreDeDataFactory**-**nombreDeServicioVinculado**-marcaDeFechaYHora". Use herramientas como [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/) para eliminar contenedores del almacenamiento de blobs de Azure.

	Para más información, consulte la sección [Servicio vinculado a petición de HDInsight de Azure](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
3. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.
4. Confirme que **AzureStorageLinkedService** y **HDInsightOnDemandLinkedService** aparecen en la vista de árbol de la izquierda.

	![Vista de árbol con servicios vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Creación de conjuntos de datos
En este paso, creará conjuntos de datos que representen los datos de entrada y salida para el procesamiento de Hive. Estos conjuntos de datos hacen referencia al servicio **AzureStorageLinkedService1** que creó anteriormente en este tutorial. El servicio vinculado apunta a una cuenta de Almacenamiento de Azure y los conjuntos de datos especifican el contenedor, la carpeta y el nombre de archivo en el almacenamiento que contiene los datos de entrada y salida.

### Creación de un conjunto de datos de entrada

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.

	![Nuevo conjunto de datos](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1. En el fragmento de código JSON, va a crear un conjunto de datos denominado **AzureBlobInput** que representa los datos de entrada para una actividad de la canalización. Además, va a especificar que los datos de entrada se coloquen en el contenedor de blobs llamado **adfgetstarted** y en la carpeta llamada **inputdata**.
		
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

	En la siguiente tabla se ofrecen descripciones de las propiedades JSON que se usan en el fragmento de código:

	| Propiedad | Descripción |
	| :------- | :---------- |
	| type | La propiedad type se establece en AzureBlob porque los datos residen en Almacenamiento de blobs de Azure. |  
	| linkedServiceName | Hace referencia al servicio AzureStorageLinkedService que creó anteriormente. |
	| fileName | Esta propiedad es opcional. Si omite esta propiedad, se seleccionan todos los archivos de folderPath. En este caso, se procesa solo el archivo input.log. |
	| type | Los archivos de registro están en formato de texto, por tanto, usaremos TextFormat. | 
	| columnDelimiter | Las columnas de los archivos de registro están delimitadas por , (coma). |
	| frecuencia/intervalo | La frecuencia está establecida en Mes y el intervalo es 1, lo que significa que los segmentos de entrada estarán disponibles cada mes. | 
	| external | Esta propiedad se establece en true si el servicio Factoría de datos no ha generado los datos de entrada. | 
	  
	
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos recién creado. Debería ver el conjunto de datos en la vista de árbol de la izquierda.


### Creación del conjunto de datos de salida
Ahora, va a crear el conjunto de datos de salida que representa los datos de salida almacenados en Almacenamiento de blobs de Azure.

1. En el **Editor de la Factoría de datos**, haga clic en **Nuevo conjunto de datos** en la barra de comandos y seleccione **Almacenamiento de blobs de Azure**.
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1. En el fragmento de código JSON, cree un conjunto de datos llamado **AzureBlobOutput** y especifique la estructura de los datos que generará el script de Hive. Además, especifique que los resultados se almacenen en el contenedor de blobs llamado **adfgetstarted** y en la carpeta llamada **partitioneddata**. La sección **availability** especifica que el conjunto de datos de salida se genera mensualmente.
	
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

	Consulte la sección **Creación del conjunto de datos de entrada** para obtener las descripciones de estas propiedades. No establezca la propiedad externa en un conjunto de datos ya que este es generado por el servicio Factoría de datos.
3. Haga clic en **Implementar** en la barra de comandos para implementar el conjunto de datos recién creado.
4. Compruebe que el conjunto de datos se creó correctamente.

	![Vista de árbol con servicios vinculados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Creación de una canalización
En este paso, creará la primera canalización con una actividad **HDInsightHive**. Tenga en cuenta que el segmento de entrada está disponible mensualmente (frecuencia: mes, intervalo: 1), el segmento de salida se genera mensualmente y la propiedad de programador también se establece en mensual para la actividad (consulte a continuación). La configuración del conjunto de datos de salida y la del programador de la actividad deben coincidir. En este momento, el conjunto de datos de salida es lo que impulsa la programación, por lo que debe crear un conjunto de datos de salida incluso si la actividad no genera ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. Al final de esta sección se explican las propiedades usadas en el siguiente JSON.

1. En el **Editor de Data Factory**, haga clic en el botón de puntos suspensivos **(...) Más comandos** y, después, en **Nueva canalización**.
	
	![Botón Nueva canalización](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie y pegue el fragmento de código siguiente en la ventana Borrador 1.

	> [AZURE.IMPORTANT] Reemplace **storageaccountname** por el nombre de la cuenta de almacenamiento en JSON.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
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
		            }
		        ],
		        "start": "2016-04-01T00:00:00Z",
		        "end": "2016-04-02T00:00:00Z",
		        "isPaused": false
		    }
		}
 
	En el fragmento de código JSON, se crea una canalización que consta de una sola actividad que usa Hive para procesar los datos en un clúster de HDInsight.
	
	El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de almacenamiento de Azure (especificada mediante la propiedad scriptLinkedService, llamada **AzureStorageLinkedService**) en una carpeta denominada **script** del contenedor **adfgetstarted**.

	La sección **defines** se usa para especificar la configuración de tiempo de ejecución que se pasará al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Las propiedades **start** y **end** de la canalización especifican el período activo de esta.

	En el código JSON de la actividad, se especifica que el script de Hive se ejecuta en el proceso que especifica el servicio vinculado **linkedServiceName**: **HDInsightOnDemandLinkedService**.

	> [AZURE.NOTE] Consulte en la sección [Anatomía de una canalización](data-factory-create-pipelines.md#anatomy-of-a-pipeline) las propiedades JSON que se usan en el ejemplo anterior.

3. Confirme lo siguiente:
	1. El archivo **input.log** existe en la carpeta **inputdata** del contenedor **adfgetstarted** en Almacenamiento de blobs de Azure.
	2. El archivo **partitionweblogs.hql** existe en la carpeta **script** del contenedor **adfgetstarted** en Almacenamiento de blobs de Azure. Realice los pasos de los requisitos previos en la [información general del tutorial](data-factory-build-your-first-pipeline.md) si no ve estos archivos.
	3. Confirme que ha reemplazado **storageaccountname** por el nombre de la cuenta de almacenamiento en la canalización JSON.
2. Haga clic en **Implementar** en la barra de comandos para implementar la canalización. Dado que las horas de **inicio** y **fin** están establecidas en el pasado y **isPaused** está establecido en false, la canalización (actividad en la canalización) se ejecutará inmediatamente después de implementar.
4. Confirme que la canalización aparece en la vista de árbol.

	![Vista de árbol con canalización](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Enhorabuena, ya creó correctamente su primera canalización.

## Supervisión de la canalización

6. Haga clic en **X** para cerrar las hojas del Editor de la Factoría de datos y volver a la hoja de la Factoría de datos y, a continuación, haga clic en **Diagrama**.
  
	![Icono Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. En la Vista de diagrama, verá información general de las canalizaciones y conjuntos de datos empleados en este tutorial.
	
	![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)
8. Para ver todas las actividades de la canalización, haga clic con el botón derecho en la canalización en el diagrama y haga clic en Abrir canalización.

	![Menú Abrir canalización](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Confirme que la actividad de HDInsightHive está en la canalización.
  
	![Vista Abrir canalización](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	Para volver a la vista anterior, haga clic en **Factoría de datos** en el menú de la barra de ruta de navegación en la parte superior.
10. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobInput**. Confirme que el estado del segmento es **Listo**. Puede que el segmento tarde un par de minutos en aparecer con ese estado. Si esto no sucede después de esperar un tiempo, compruebe si el archivo de entrada (input.log) está en el contenedor (adfgetstarted) y en la carpeta (inputdata) correctos.

	![Segmento de entrada con estado Listo](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Haga clic en **X** para cerrar la hoja **AzureBlobInput**.
12. En la **Vista de diagrama**, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que el segmento se está procesando.

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Cuando finalice el procesamiento, el segmento aparecerá con el estado **Listo**.
	>[AZURE.IMPORTANT] La creación de un clúster de HDInsight a petición normalmente tarda algún tiempo (20 minutos aproximadamente).

	![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. Cuando el segmento se encuentre en el estado **Listo**, busque los datos de salida en la carpeta **partitioneddata** del contenedor **adfgetstarted** de Almacenamiento de blobs.
 
	![datos de salida](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

Consulte el artículo [Supervisión y administración de canalizaciones de Data Factory de Azure](data-factory-monitor-manage-pipelines.md) para más información.

> [AZURE.IMPORTANT] El archivo de entrada se elimina cuando el segmento se procesa correctamente. Por lo tanto, si desea volver a ejecutar el segmento o volver a realizar el tutorial, cargue el archivo de entrada (input.log) en la carpeta inputdata del contenedor adfgetstarted.

## Resumen 
En este tutorial, ha creado una instancia de Data Factory de Azure para procesar datos mediante la ejecución de un script de Hive en un clúster de Hadoop en HDInsight. Ha usado el Editor de Data Factory en el Portal de Azure para llevar a cabo los siguientes pasos:

1.	Ha creado una **factoría de datos** de Azure.
2.	Ha creado dos **servicios vinculados**:
	1.	El servicio vinculado **Almacenamiento de Azure** para vincular el almacenamiento de blobs de Azure que contiene los archivos de entrada y salida a la factoría de datos.
	2.	El servicio vinculado a petición de **HDInsight de Azure** para vincular un clúster de Hadoop en HDInsight a petición a la factoría de datos. Data Factory de Azure crea un clúster de Hadoop en HDInsight justo a tiempo para procesar los datos de entrada y generar los datos de salida.
3.	Ha creado dos **conjuntos de datos** que describen los datos de entrada y salida para la actividad de Hive de HDInsight en la canalización.
4.	Ha creado una **canalización** con una actividad de **Hive de HDInsight**.

## Pasos siguientes
En este artículo, creó una canalización con una actividad de transformación (actividad de HDInsight) que ejecuta un script de Hive en un clúster de HDInsight a petición. Para ver cómo se usa una actividad de copia para copiar datos de un blob de Azure en SQL Azure, consulte [Tutorial: Copia de datos de un blob de Azure a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Otras referencias
| Tema. | Descripción |
| :---- | :---- |
| [Transformación y análisis con Data Factory de Azure](data-factory-data-transformation-activities.md) | En este artículo se proporciona una lista de actividades de transformación de datos (por ejemplo, la transformación de Hive para HDInsight usada en este tutorial), que se admiten en Data Factory de Azure. | 
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a comprender las canalizaciones y actividades en Factoría de datos de Azure y cómo aprovecharlas para construir flujos de trabajo de extremo a extremo controlados por datos para su escenario o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en Factoría de datos de Azure.
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. 

  

<!---HONumber=AcomDC_0803_2016-->