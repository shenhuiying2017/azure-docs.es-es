<properties 
	pageTitle="Creación y programación de canalizaciones, actividades en cadena en Data Factory| Microsoft Azure" 
	description="Aprenda a crear una canalización de datos en Data Factory de Azure para mover y transformar datos. Crear un flujo de trabajo controlado por datos para producir información lista para usar." 
    keywords="canalización de datos, flujo de trabajo controlado por datos"
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Canalizaciones y actividades en Data Factory de Azure
Este artículo ayuda a conocer las canalizaciones y actividades de Azure Data Factory y a usarlas para construir flujos de trabajo controlados por datos de un extremo para los escenarios de procesamiento de datos y movimiento de datos.

> [AZURE.NOTE] En este artículo se considera que ha repasado antes los artículos [Introducción a Azure Data Factory](data-factory-introduction.md). Si no tiene experiencia práctica con la creación de factorías de datos, repasar el tutorial [Creación de su primera factoría de datos](data-factory-build-your-first-pipeline.md) puede ayudarle a comprender mejor este artículo.

## ¿Qué es una canalización de datos?
**Canalización** es una agrupación de **actividades** relacionadas lógicamente. Se usa para agrupar actividades en una unidad que realiza una tarea. Para entender mejor las canalizaciones, deberá entender primero qué es una actividad.

## ¿Qué es una actividad?
Las actividades definen las acciones que se van a realizar en los datos. Cada actividad tiene cero o más [conjuntos de datos](data-factory-create-datasets.md) como entradas y genera uno o varios conjuntos de datos como salida.

Por ejemplo, puede utilizar una actividad de copia para organizar la copia de datos de un almacén de datos a otro. De manera similar, puede usar una actividad de Hive de HDInsight que ejecute una consulta de Hive en un clúster de HDInsight de Azure para transformar los datos. Azure Data Factory ofrece una amplia gama de actividades de [transformación de datos](data-factory-data-transformation-activities.md) y de [movimiento de datos](data-factory-data-movement-activities.md). También puede crear una actividad personalizada de .NET para ejecutar su propio código.

## Canalización de copia de ejemplo
En la canalización de ejemplo siguiente, hay una actividad del tipo **Copia** en la sección de **actividades**. En este ejemplo, [Copiar actividad](data-factory-data-movement-activities.md) copia datos desde un Azure Blob Storage en una Azure SQL Database.

	{
	  "name": "CopyPipeline",
	  "properties": {
	    "description": "Copy data from a blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputDataset"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputDataset"
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
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	} 

Tenga en cuenta los siguientes puntos:

- En la sección de actividades, solo hay una actividad con **type** establecido en **Copy**.
- La entrada de la actividad está establecida en **InputDataset**, mientras que la salida está establecida en **OutputDataset**.
- En la sección **typeProperties**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

Para obtener un tutorial completo de creación de esta canalización, vea [Tutorial: Copiar datos desde Blob Storage a SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Canalización de transformación de ejemplo
En la canalización de ejemplo siguiente, hay una actividad del tipo **HDInsightHive** en la sección de **actividades**. En este ejemplo, el [actividad de HDInsight Hive](data-factory-hive-activity.md) transforma los datos de Azure Blob Storage mediante la ejecución de un archivo de script de Hive en un clúster de Azure HDInsight Hadoop.

	{
	    "name": "TransformPipeline",
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

Tenga en cuenta los siguientes puntos:

- En la sección de actividades, solo hay una actividad con **type** establecido en **HDInsightHive**.
- El archivo de script de Hive, **partitionweblogs.hql**, se almacena en la cuenta de Almacenamiento de Azure (especificada por la propiedad scriptLinkedService, llamada **AzureStorageLinkedService**) en una carpeta denominada **script** del contenedor **adfgetstarted**.
- La sección **defines** se usa para especificar la configuración de tiempo de ejecución que se pasa al script de Hive como valores de configuración de Hive (por ejemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Para obtener un tutorial completo de creación de esta canalización, consulte [Tutorial: Compilar su primera canalización para procesar datos mediante el clúster de Hadoop](data-factory-build-your-first-pipeline.md).

## Encadenamiento de actividades
Si tiene varias actividades en una canalización y la salida de una actividad no es la entrada de otra actividad, las actividades se pueden ejecutar en paralelo si los segmentos de datos de entrada para las actividades están listos.

Puede encadenar dos actividades haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Las actividades pueden estar en la misma canalización o en canalizaciones diferentes. La segunda actividad se ejecuta solo cuando la primera de ellas se completa correctamente.

Por ejemplo, considere el siguiente caso:
 
1.	La canalización P1 incluye la actividad A1 que requiere el conjunto de datos de entrada externo D1 y genera el conjunto de datos de **salida** **D2**.
2.	La canalización P2 incluye la actividad A2 que requiere una **entrada** del conjunto de datos **D2** y genera el conjunto de datos de salida D3.
 
En este escenario, la actividad A1 se ejecuta cuando los datos externos están disponibles y se alcanza la frecuencia de disponibilidad programada. La actividad A2 se ejecuta cuando están disponibles los segmentos programados de D2 y se alcanza la frecuencia de disponibilidad programada. Si se produce un error en uno de los segmentos del conjunto de datos D2, A2 no se ejecuta para ese segmento hasta que está disponible.

Vista de diagrama:

![Encadenamiento de las actividades de dos canalizaciones](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vista de diagrama con ambas actividades en la misma canalización:

![Encadenamiento de las actividades de la misma canalización](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Para obtener más información, vea [Programación y ejecución](#chaining-activities).

## Programación y ejecución
Hasta ahora se ha descrito lo que son las canalizaciones y las actividades. También ha visto cómo se definen estas y se vio con detalle las actividades en Azure Data Factory. Ahora veremos cómo se ejecutan.

Una canalización solo está activa entre su hora de inicio y de finalización. No se ejecuta antes de la hora de inicio ni después de la hora de finalización. Si la canalización está en pausa, no se ejecutará, independientemente de su hora de inicio y de finalización. Para que se ejecute una canalización, no debe estar en pausa. De hecho, no se ejecuta la canalización. Se ejecutan las actividades de la canalización. No obstante, lo hacen en el contexto general de la canalización.

Consulte [Programación y ejecución](data-factory-scheduling-and-execution.md) para comprender cómo funciona la programación y la ejecución en Factoría de datos de Azure.

## Creación de canalizaciones
Factoría de datos de Azure proporciona varios mecanismos para crear e implementar canalizaciones (que a su vez contienen una o varias actividades).

### Uso de Azure Portal
Puede utilizar el editor de Data Factory en Azure Portal para crear una canalización. Consulte [Introducción a Azure Data Factory (Editor de Data Factory)](data-factory-build-your-first-pipeline-using-editor.md), un tutorial completo.

### Uso de Visual Studio 
Puede usar Visual Studio para crear e implementar canalizaciones en Factoría de datos de Azure. Consulte [Introducción a Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md), un tutorial completo.

### Uso de Azure PowerShell
Puede usar Azure PowerShell para crear canalizaciones en Factoría de datos de Azure. Digamos que ha definido la canalización JSON en un archivo c:\\DPWikisample.json. Puede cargarlo en su instancia de Factoría de datos de Azure, tal como se muestra en el ejemplo siguiente:

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Consulte [Introducción a Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md), un tutorial completo para crear una factoría de datos con una canalización.

### Uso del SDK de .NET
También puede crear e implementar la canalización mediante el SDK de .NET. Este mecanismo se puede usar para crear canalizaciones mediante programación. Para más información, vea [Creación, administración y supervisión de factorías de datos mediante programación](data-factory-create-data-factories-programmatically.md).


### Uso de la plantilla de Azure Resource Manager
Puede crear e implementar una canalización mediante una plantilla de Azure Resource Manager. Para más información, vea [Introducción a Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md).

### Uso de la API de REST
También puede crear e implementar la canalización mediante las API de REST. Este mecanismo se puede usar para crear canalizaciones mediante programación. Para más información, vea [Crear o actualizar una canalización](https://msdn.microsoft.com/library/azure/dn906741.aspx).


## Supervisar y administrar canalizaciones  
Una vez implementada una canalización, puede administrar y supervisar las canalizaciones, segmentos y ejecuciones. Obtenga más información sobre este tema aquí: [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md).


## JSON de canalización   
Vamos a fijarnos un poco más en cómo se define una canalización en formato JSON. La estructura genérica de una canalización tiene el aspecto siguiente:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

La sección **activities** puede contener una o más actividades definidas. Cada actividad tiene la siguiente estructura de nivel superior:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

En la tabla siguiente se describen las propiedades dentro de las definiciones de actividad y la canalización JSON:

Etiqueta | Description | Obligatorio
--- | ----------- | --------
name | Nombre de la actividad o la canalización. Especifique un nombre que represente la acción que la actividad o la canalización está configurada para realizar<br/><ul><li>Número máximo de caracteres: 260</li><li>Tiene que comenzar con una letra, un número o un carácter de subrayado (\_)</li><li>No se permiten los caracteres siguientes: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> | Sí
description | Texto que describe para qué se usa la actividad o la canalización | Sí
type | Especifica el tipo de actividad. Consulte los artículos [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y [Actividades de transformación de datos](data-factory-data-transformation-activities.md) para ver los diferentes tipos de actividades. | Sí
inputs | Tablas de entrada utilizadas por la actividad <br/><br/>/ / una tabla de entrada<br/>"inputs": [{"name": "inputtable1"}],<br/><br/>/ / dos tablas de entrada <br/>"inputs": [{"name": "inputtable1"}, {"name": "inputtable2"}], | Sí
outputs | Tablas de salida utilizadas por la actividad.// una tabla de salida<br/>"outputs": [ { "name": “outputtable1” } ],<br/><br/>//dos tablas de salida<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ], | Sí
linkedServiceName | Nombre del servicio vinculado utilizado por la actividad. <br/><br/>Una actividad puede requerir que especifique el servicio vinculado que enlaza con el entorno de procesos necesario. | Sí para actividad de HDInsight y actividad de puntuación por lotes de Aprendizaje automático de Azure <br/><br/>No para todos los demás
typeProperties | Propiedades en la sección typeProperties dependen del tipo de actividad. | No
policy | Directivas que afectan al comportamiento en tiempo de ejecución de la actividad. Si no se especifica, se usan las directivas predeterminadas. | No
start | Fecha y hora de inicio para la canalización. Debe estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. <br/><br/>Se puede especificar una hora local, por ejemplo, una hora EST. Aquí tiene un ejemplo: "2016-02-27T06:00:00**-05:00**", que son las 6 a.m. EST.<br/><br/>Las propiedades start y end juntas especifican un período activo para la canalización. Los segmentos de salida solo se producen en este período activo. | No<br/><br/>Si especifica un valor para la propiedad end, tiene que especificar el valor de la propiedad start.<br/><br/>Las horas de inicio y finalización pueden estar vacías para crear una canalización. Debe especificar ambos valores para establecer un período activo para que se ejecute la canalización. Si no especifica horas de inicio y finalización al crear una canalización, puede establecerlas más adelante mediante el cmdlet Set-AzureDataFactoryPipelineActivePeriod.
end | Fecha y hora de finalización para la canalización. Si se especifica, debe estar en formato ISO. Por ejemplo: 2014-10-14T17:32:41Z <br/><br/>Es posible especificar una hora local, por ejemplo, una hora EST. Aquí tiene un ejemplo: "2016-02-27T06:00:00**-05:00**", que son las 6 a.m. EST.<br/><br/>Para ejecutar la canalización indefinidamente especifique 9999-09-09 como valor para la propiedad end. | No <br/><br/>Si se especifica un valor para la propiedad start, hay que especificar un valor para la propiedad end.<br/><br/>Consulte las notas para la propiedad **start**.
isPaused | Si se establece en true, la canalización no se ejecuta. Valor predeterminado = false. Puede utilizar esta propiedad para habilitar o deshabilitar. | No 
scheduler | La propiedad "scheduler" se utiliza para definir la programación deseada para la actividad. Sus subpropiedades son las mismas que las de la [propiedad availability de un conjunto de datos](data-factory-create-datasets.md#Availability). | No |   
| pipelineMode | El método para la programación de ejecuciones para la canalización. Los valores permitidos son: scheduled (predeterminado).<br/><br/>‘Scheduled’ indica que la canalización se ejecutará en el intervalo de tiempo especificado de acuerdo con su período activo (hora de inicio y finalización). 'Onetime' indica que la canalización se ejecutará solo una vez. Las canalizaciones creadas una sola vez no se pueden modificar ni actualizar actualmente. Consulte la sección [Canalización de una vez](data-factory-scheduling-and-execution.md#onetime-pipeline) para más información acerca de la configuración onetime. | No | 
| expirationTime | Tiempo de duración después de la creación durante el que la canalización es válida y debe permanecer aprovisionada. La canalización se elimina automáticamente una vez que alcanza la hora de caducidad si no tiene ninguna ejecución activa, con error o pendiente. | No | 
| datasets | Lista de conjuntos de datos que usarán las actividades definidas en la canalización. Esta propiedad puede utilizarse para definir conjuntos de datos que son específicos de esta canalización y que no se definen dentro de la factoría de datos. Los conjuntos de datos definidos dentro de esta canalización solo pueden utilizarse en ella y no se pueden compartir. Consulte la sección [Conjuntos de datos limitados](data-factory-create-datasets.md#scoped-datasets) para más información.| No |  
 

### Directivas
Las directivas afectan al comportamiento en tiempo de ejecución de una actividad, concretamente cuando se procesa el segmento de una tabla. En la tabla siguiente se proporciona los detalles.

Propiedad | Valores permitidos | Valor predeterminado | Description
-------- | ----------- | -------------- | ---------------
simultaneidad | Entero <br/><br/>Valor máximo: 10 | 1 | Número de ejecuciones simultáneas de la actividad.<br/><br/>Determina el número de ejecuciones paralelas de la actividad que pueden tener lugar en distintos segmentos. Por ejemplo, si una actividad tiene que recorrer un gran conjunto de datos disponibles, tener un valor mayor de simultaneidad acelera el procesamiento de datos. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina el orden de los segmentos de datos que se están procesando.<br/><br/>Por ejemplo, si tiene 2 segmentos (que tienen lugar uno a las 4 p.m. y el otro a las 5 p.m.) y ambos están pendientes de ejecución. Si establece que executionPriorityOrder sea NewestFirst, se procesará primero el segmento de las 5 p.m. De forma similar, si establece que executionPriorityORder sea OldestFIrst, se procesará el segmento de las 4 p.m. 
retry | Entero<br/><br/>El valor máximo permitido es 10 | 3 | Número de reintentos antes de que el procesamiento de datos del segmento se marque como error. La ejecución de la actividad de un segmento de datos se vuelve a intentar hasta el número de reintentos especificado. El reintento se realiza tan pronto como sea posible después del error.
timeout | TimeSpan | 00:00:00 | Tiempo de espera para la actividad. Ejemplo: 00:10:00 (implica un tiempo de espera de 10 minutos)<br/><br/>Si no se especifica ningún valor o si es 0, el tiempo de espera es infinito.<br/><br/>Si el tiempo de procesamiento de los datos en un segmento supera el valor de tiempo de espera, se cancela y el sistema vuelve a intentar el procesamiento. El número de reintentos depende de la propiedad retry. Si se excede el tiempo de espera, el estado será TimedOut.
delay | TimeSpan | 00:00:00 | Especifica el retraso antes de iniciar el procesamiento de los datos del segmento.<br/><br/>La ejecución de la actividad de un segmento de datos se inicia una vez que transcurra el retraso más allá del tiempo de ejecución esperado.<br/><br/>Ejemplo: 00:10:00 (implica un retraso de 10 minutos)
longRetry | Entero<br/><br/>Valor máximo: 10 | 1 | Número de reintentos largos antes de que la ejecución de los segmentos produzca error.<br/><br/>Los intentos de longRetry se espacian de acuerdo a longRetryInterval. Por tanto, si necesita especificar un tiempo entre reintentos, utilice longRetry. Si se especifican Retry y longRetry, cada intento de longRetry incluirá el número de intentos de Retry y el número máximo de intentos será Retry * longRetry.<br/><br/>Por ejemplo, si tenemos la siguiente configuración en la directiva de la actividad:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Se supone que existe un solo segmento para ejecutar (el estado es En espera) y la ejecución de la actividad no se puede realizar nunca. Inicialmente habría tres intentos consecutivos de ejecución. Después de cada intento, el estado del segmento sería Retry. Después de los 3 primeros intentos, el estado del segmento sería LongRetry.<br/><br/>Después de una hora (es decir, el valor de longRetryInteval), se produciría otro conjunto de 3 intentos consecutivos de ejecución. Después de eso, el estado del segmento sería Failed y ya no se realizarían más intentos. Por tanto, en total se realizaron 6 intentos.<br/><br/>Nota: Si una ejecución se realiza correctamente, el estado del segmento sería Ready y no se realizaría ningún otro reintento.<br/><br/>longRetry puede usarse en situaciones donde llegan datos dependientes a horas no deterministas o el entorno general en el que se produce el procesamiento de datos es poco confiable. En esos casos es posible que realizar reintentos uno tras otro no ayude, mientras que hacerlo después de un intervalo de tiempo puede generar el resultado deseado.<br/><br/>Advertencia: No establezca valores altos para longRetry o longRetryInterval. Normalmente, los valores más altos implican otros problemas sistémicos. 
longRetryInterval | TimeSpan | 00:00:00 | El retraso entre reintentos largos 


## Pasos siguientes

- Conozca [Programación y ejecución en Factoría de datos de Azure](data-factory-scheduling-and-execution.md).
- Obtenga más información sobre las [capacidades de movimiento de datos](data-factory-data-movement-activities.md) y [transformación de datos](data-factory-data-transformation-activities.md) en Factoría de datos de Azure
- Conozca [Administración y supervisión Factoría de datos de Azure](data-factory-monitor-manage-pipelines.md).
- [Creación e implementación de su primera canalización](data-factory-build-your-first-pipeline.md).

<!---HONumber=AcomDC_0928_2016-->