<properties 
	pageTitle="Conjuntos de datos en Data Factory de Azure | Microsoft Azure" 
	description="Descripción de los conjuntos de datos de Factoría de datos de Azure y aprenda a crearlos." 
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
	ms.date="06/08/2016" 
	ms.author="spelluru"/>

# Conjuntos de datos en Data Factory de Azure
Los conjuntos de datos de Data Factory de Azure son punteros o referencias con nombre que señalan a los datos que se quieran usar como entrada o salida de una actividad de una canalización. Los conjuntos de datos identifican datos en distintos almacenes de datos, que incluyen tablas, archivos, carpetas y documentos.

Un **servicio vinculado** define la información necesaria para que Data Factory de Azure se **conecte** a un **almacén de datos** (como una cuenta de Almacenamiento de Azure o una base de datos SQL de Azure) o un **proceso** (como Azure HDInsight y Lote de Azure). El servicio vinculado define el mecanismo (dirección, protocolo, esquema de autenticación, etc.) para tener acceso al almacén de datos o proceso.

Consulte [Almacenes de datos que se admiten](data-factory-data-movement-activities.md#supported-data-stores) para ver una lista de los servicios vinculados de almacén de datos admitidos. Haga clic en un origen de datos de la tabla para ir al tema que proporciona información detallada sobre cómo crear y configurar un servicio vinculado para ese almacén de datos.

Para obtener una lista de los servicios vinculados de procesos admitidos, consulte [Servicios vinculados de procesos](data-factory-compute-linked-services.md). Para comprender las actividades que usan estos servicios vinculados, vea [Transformación y análisis con Data Factory de Azure](data-factory-data-transformation-activities.md).

Un **conjunto de datos** de Data Factory representa las estructuras de datos dentro de un almacén de datos por medio de un **servicio vinculado al almacén de datos** (por ejemplo, un contenedor de blob en una cuenta de Almacenamiento de Azure o una tabla en una Base de datos SQL de Azure). Se puede utilizar como elemento de entrada o de salida de una actividad en una canalización. Después de crear conjuntos de datos, puede utilizarlos con actividades de la canalización. Por ejemplo, puede tener un conjunto de datos como un conjunto de datos de entrada y salida de una actividad de HDInsightHive o una actividad de copia.

> [AZURE.NOTE] Si no está familiarizado con Data Factory de Azure, consulte [Introducción al servicio Data Factory de Azure](data-factory-introduction.md) para obtener información general del servicio de la factoría de datos y [Tutorial: Creación de su primera factoría de datos (Introducción)](data-factory-build-your-first-pipeline.md) para obtener un tutorial sobre cómo crear su primera factoría de datos. Estos dos artículos le proporcionarán el contexto necesario para comprender mejor este artículo.


## Definición de conjuntos de datos
Un conjunto de datos en Data Factory de Azure se define como sigue:


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

La tabla siguiente describe las propiedades del JSON anterior:

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| -------- | ----------- | -------- | ------- |
| name | Nombre del conjunto de datos. Consulte [Data Factory de Azure: reglas de nomenclatura](data-factory-naming-rules.md) para consultar este tipo de reglas. | Sí | N/D |
| type | Tipo de conjunto de datos. Especifique uno de los tipos admitidos por Data Factory de Azure (por ejemplo: AzureBlob, AzureSqlTable). <br/><br/>Consulte [Tipo de conjunto de datos](#Type) para más información. | Sí | N/D |
| structure | Esquema del conjunto de datos<br/><br/>Consulte la sección [Estructura del conjunto de datos](#Structure) para ver más detalles. | Nº | N/D |
| typeProperties | Propiedades correspondientes al tipo seleccionado. Consulte la sección [Tipo de conjunto de datos](#Type) para más información sobre los tipos admitidos y sus propiedades. | Sí | N/D |
| external | Marca booleana para especificar si un conjunto de datos es generado explícitamente por una canalización de la factoría de datos o no. | No | false | 
| availability | Define la ventana de procesamiento o el modelo de segmentación para la producción del conjunto de datos. <br/><br/>Consulte el tema [Disponibilidad del conjunto de datos](#Availability) para más información<br/><br/>Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para más información sobre el modelo de segmentación del conjunto de datos | Sí | N/D
| policy | Define los criterios o la condición que deben cumplir los segmentos del conjunto de datos. <br/><br/>Consulte el tema [Directiva del conjunto de datos](#Policy) para más información. | No | N/D |

### Ejemplo

A continuación se muestra un ejemplo de un conjunto de datos que representa una tabla denominada**MyTable** en una **Base de datos SQL de Azure**.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

Tenga en cuenta lo siguiente:

- type está establecido en AzureSqlTable.
- La propiedad de tipo tableName (específico del tipo AzureSqlTable) se establece en MyTable.
- linkedServiceName hace referencia a un servicio vinculado de tipo AzureSqlDatabase. Consulte la definición de servicio vinculado a continuación. 
- La frecuencia de availability se establece en Day y el intervalo en 1, lo cual significa que el segmento se produce diariamente.  

AzureSqlLinkedService se define como sigue:

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

En el ejemplo JSON anterior:

- type está establecido en AzureSqlDatabase
- La propiedad de tipo connectionString especifica información para conectarse a una Base de datos SQL de Azure.  


Como puede ver, el servicio vinculado define cómo conectarse a una Base de datos SQL de Azure y el conjunto de datos define qué tabla se utiliza como entrada y salida de la factoría de datos. La sección de actividad en el JSON de [canalización](data-factory-create-pipelines.md) especifica si el conjunto de datos se utiliza como un conjunto de datos de entrada o salida.


> [AZURE.IMPORTANT] A menos que se esté produciendo un conjunto de datos mediante Data Factory de Azure, debe marcarse como **external**. Esto generalmente se aplica a las entradas de la primera actividad de una canalización.

## <a name="Type"></a> Tipo de conjunto de datos
Los orígenes de datos admitidos y los tipos de conjuntos de datos están alineados. Consulte los temas a los que se hace referencia en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) para más información sobre los tipos y la configuración de los conjuntos de datos. Por ejemplo, si está usando datos de una base de datos SQL de Azure, haga clic en la base de datos SQL de Azure en la lista de almacenes de datos admitidos para ver información detallada sobre cómo usarla como origen o como almacén de datos de receptores.

## <a name="Structure"></a>Estructura del conjunto de datos
La sección **structure** define el esquema del conjunto de datos. Contiene una colección de nombres y tipos de datos de columnas. En el ejemplo siguiente, el conjunto de datos tiene tres columnas slicetimestamp, projectname y pageviews del tipo: String, String y Decimal respectivamente.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> Disponibilidad del conjunto de datos
En la sección **availability** de un conjunto de datos se define la ventana de procesamiento (cada hora, diariamente, semanalmente, etc.) o el modelo de segmentación del conjunto de datos. Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para obtener más detalles sobre el modelo de segmentación y dependencia del conjunto de datos.

La sección availability siguiente especifica que el conjunto de datos se produce cada hora en el caso de un conjunto de datos de salida (o) que está disponible cada hora en el caso de un conjunto de datos de entrada.

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

La tabla siguiente describe las propiedades que puede utilizar en la sección de disponibilidad.

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| -------- | ----------- | -------- | ------- |
| frequency | Especifica la unidad de tiempo para la producción de segmentos del conjunto de datos.<br/><br/>**Frecuencia admitida**: Minute, Hour, Day, Week, Month. | Sí | N/D |
| interval | Especifica un multiplicador para frecuencia<br/><br/>"Frequency x interval" determina la frecuencia con la que se genera el segmento.<br/><br/>Si necesita segmentar el conjunto de datos cada hora, establezca **Frequency** en **Hour** e **interval** en **1**.<br/><br/>**Nota:** Si especifica Frequency en Minute, se recomienda establecer interval en más de 15 | Sí | N/D |
| style | Especifica si se debe generar el segmento al principio o al final del intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si se establece Frequency en Month y style se establece en EndOfInterval, se genera el segmento en el último día del mes. Si style está establecido en StartOfInterval, se genera el segmento en el primer día del mes.<br/><br/>Si se establece Frequency en Day y style se establece en EndOfInterval, el segmento se produce en la última hora del día.<br/><br/>Si se establece Frequency en Hour y style se establece en EndOfInterval, el segmento se produce al final de la hora. Por ejemplo, para un segmento para el período de 1 p.m. – 2 p.m., el segmento se producirá a las 2 p.m. | No | EndOfInterval |
| anchorDateTime | Define la posición absoluta en tiempo usada por el programador para calcular los límites de los segmentos del conjunto de datos.<br/><br/>**Nota:** si AnchorDateTime tiene partes de fecha más detalladas que frequency, se ignorarán las partes más detalladas. <br/><br/>Por ejemplo, si **interval** es **hourly** (frequency: hour e interval: 1) y **AnchorDateTime** contiene **minutes and seconds**, las partes **minutes and seconds** de AnchorDateTime se omitirán. | No | 01/01/0001 |
| Offset | Intervalo de tiempo mediante el que se desplaza el inicio y el final de todos los segmentos del conjunto de datos.<br/><br/>**Nota:** si se especifican anchorDateTime y offset, el resultado es el desplazamiento combinado. | No | N/D |

### Ejemplo de offset

Segmentos diarios que comienzan a las 6 a.m., en lugar de a medianoche, que es el valor predeterminado.

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**frequency** está establecido en **Month** e **interval** está establecido en **1** (una vez al mes): si desea que el segmento se genere el día 9 de cada mes a las 6 A.M., establezca offset en "09.06:00:00". Recuerde que esta es una hora UTC.

Para un programación de 12 meses (frecuency = month; interval = 12), offset: 60.00:00:00 significa el 1 o 2 de marzo de cada año (60 días desde el principio del año si style = StartOfInterval), en función de si el año es bisiesto o no.

### Ejemplo de anchorDateTime

**Ejemplo:** segmentos del conjunto de datos de 23 horas que comienzan en el día y a la hora siguientes: 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### Ejemplo de desplazamiento y estilo

Si necesita un conjunto de datos mensualmente en una fecha y hora específicas (supongamos que el tercer día de cada mes a las 8 de la mañana), podría usar la etiqueta **offset** para establecer la fecha y la hora en que se debería ejecutar.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>Directiva del conjunto de datos

En la sección **policy** de la definición del conjunto de datos se definen los criterios o condiciones que deben cumplir los segmentos del conjunto de datos.

### Directivas de validación

| Nombre de la directiva | Descripción | Aplicado a | Obligatorio | Valor predeterminado |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida que los datos de un **blob de Azure** cumplen los requisitos de tamaño mínimo (en megabytes). | Blob de Azure | No | N/D |
|minimumRows | Valida que los datos de una **Base de datos SQL de Azure** o una **tabla de Azure** contienen el número mínimo de filas. | <ul><li>Base de datos SQL de Azure</li><li>Tabla de Azure</li></ul> | No | N/D

#### Ejemplos

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Conjuntos de datos externos

Los conjuntos de datos externos son los que no son producidos por una canalización de ejecución en la factoría de datos. Si el conjunto de datos está marcado como **external**, la directiva **ExternalData** puede definirse para influir en el comportamiento de la disponibilidad de los segmentos del conjunto de datos.

A menos que se esté produciendo un conjunto de datos mediante Data Factory de Azure, debe marcarse como **external**. Generalmente, esto se aplicará a las entradas de la primera actividad de una canalización a menos que se desee aprovechar el encadenamiento de actividades o canalizaciones.

| Nombre | Descripción | Obligatorio | Valor predeterminado |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tiempo de retraso de la comprobación de la disponibilidad de los datos externos para el segmento especificado. Por ejemplo, si se supone que los datos estarán disponibles cada hora, la comprobación de si los datos externos están realmente disponibles y el segmento correspondiente está preparado puede retrasarse mediante dataDelay.<br/><br/>Solo se aplica a la hora actual; por ejemplo, si ahora es la 1:00 p.m. y este valor es de 10 minutos, la validación se iniciará a la 1:10 p.m.<br/><br/>Esta configuración no afecta a los segmentos del pasado (los segmentos con Slice End Time + dataDelay < Now) se procesarán sin ningún retraso.<br/><br/>Un tiempo superior a 23:59 horas se debe especificar con el formato día.horas:minutos:segundos. Por ejemplo, para especificar 24 horas, no use 24:00:00; en su lugar, use 1.00:00:00. Si usa 24:00:00, se tratará como 24 días (24.00:00:00). Para 1 día y 4 horas, especifique 1.04:00:00. | No | 0 |
| retryInterval | El tiempo de espera entre un error y el siguiente reintento. Se aplica a la hora actual; si el intento anterior falla, esperamos este tiempo después del último intento. <br/><br/>Si ahora es la 1:00 p.m., empezaremos el primer intento. Si la duración para completar la primera comprobación de validación es 1 minuto y la operación falla, el siguiente reintento será a la 1:00 + 1 minuto (duración) + 1 minuto (intervalo de reintento) = 1:02 pm. <br/><br/>En el caso de los segmentos en el pasado, no habrá ningún retraso. El reintento se realizará inmediatamente. | No | 00:01:00 (1 minuto) | 
| retryTimeout | El tiempo de espera de cada reintento.<br/><br/>Si se establece en 10 minutos, la validación se debe completar en 10 minutos. Si la validación tarda más de 10 minutos en realizarse, el reintento agotará el tiempo de espera.<br/><br/>Si se agota el tiempo de espera de todos los intentos de validación, el segmento se marcará como TimedOut. | No | 00:10:00 (10 minutos) |
| maximumRetry | Número de veces que se va a comprobar la disponibilidad de los datos externos. El valor máximo permitido es 10. | No | 3 | 

## Conjuntos de datos limitados
Puede crear conjuntos de datos que se limitan a una canalización mediante el uso de la propiedad **datasets**. Estos conjuntos de datos solo los pueden usar las actividades dentro de esta canalización, pero no las actividades de otras canalizaciones. En el ejemplo siguiente se define una canalización con dos conjuntos de datos: InputDataset-rdc y OutputDataset-rdc, que se usarán dentro de la canalización.

> [AZURE.IMPORTANT] Los conjuntos de datos con ámbito solo se admiten con las canalizaciones de un solo uso (**pipelineMode** establecido en **OneTime**). Consulte la sección [Canalización de una vez](data-factory-scheduling-and-execution.md#onetime-pipeline) para más información.

	{
	    "name": "CopyPipeline-rdc",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0608_2016-->