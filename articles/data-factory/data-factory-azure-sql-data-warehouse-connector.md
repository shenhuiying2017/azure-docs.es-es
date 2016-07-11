<properties 
	pageTitle="Traslado de datos hacia el Almacenamiento de datos SQL de Azure y desde él | Microsoft Azure" 
	description="Obtenga información acerca de cómo mover los datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure" 
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
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia de Data Factory de Azure para mover datos desde Almacenamiento de datos SQL de Azure a otro almacén de datos y desde otro almacén de datos a Almacenamiento de datos SQL de Azure.

Puede especificar si desea utilizar PolyBase al cargar los datos en el Almacenamiento de datos SQL de Azure. Se recomienda utilizar PolyBase para obtener el mejor rendimiento a la hora de cargar datos en el Almacenamiento de datos SQL Azure. Consulte [Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.

En los siguientes ejemplos, se muestra cómo copiar datos entre Almacenamiento de datos SQL de Azure y Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar **directamente** desde cualquiera de los orígenes a uno los receptores indicados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia de Data Factory de Azure.



> [AZURE.NOTE] 
Para obtener información general sobre el servicio Data Factory de Azure, consulte [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md).
> 
> En este artículo se proporcionan ejemplos de JSON, pero no se incluyen instrucciones detalladas para crear una factoría de datos. Consulte [Tutorial: Copia de datos de un blob de Azure a Base de datos SQL de Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver un tutorial rápido con instrucciones detalladas para usar la actividad de copia de Data Factory de Azure.


## Ejemplo: copia de datos del almacenamiento de datos SQL de Azure a un blob de Azure

El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal desde una tabla de una base de datos del almacenamiento de datos SQL de Azure a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado del almacenamiento de datos SQL de Azure:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servicio vinculado de almacenamiento de blobs de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada del almacenamiento de datos SQL de Azure:**

El ejemplo supone que ha creado una tabla "MyTable" en el almacenamiento de datos SQL de Azure y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.
 
Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos que la tabla es externa a la factoría de datos y que no se producen por ninguna actividad de la factoría de datos

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **SqlDWSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

> [AZURE.NOTE] En el ejemplo anterior, **sqlReaderQuery** se especifica para SqlDWSource. La actividad de copia ejecuta esta consulta en el origen de Almacenamiento de datos SQL de Azure para obtener los datos.
>  
> Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
>  
> Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en Almacenamiento de datos SQL de Azure. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

## Ejemplo: copia de datos desde un blob de Azure al almacenamiento de datos SQL de Azure

El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Un conjunto de datos [dataset](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un conjunto de datos [dataset](data-factory-create-datasets.md) de tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


El ejemplo copia los datos que pertenecen a una serie temporal desde un blob de Azure a una tabla de una base de datos del almacenamiento de datos SQL de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado del almacenamiento de datos SQL de Azure:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Servicio vinculado de almacenamiento de blobs de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Factoría de datos que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de datos de salida del almacenamiento de datos SQL de Azure:**

El ejemplo copia los datos a una tabla denominada "MyTable" en el almacenamiento de datos SQL de Azure. Debe crear la tabla en el almacenamiento de datos SQL de Azure con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **SqlDWSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

Consulte el artículo [Carga de datos con Factoría de datos de Azure](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) en la documentación de almacenamiento de datos SQL de Azure para ver un tutorial.

## Propiedades del servicio vinculado Almacenamiento de datos SQL de Azure

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado del almacenamiento de datos SQL de Azure.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type debe establecerse en: **AzureSqlDW** | Sí
**connectionString** | Especifique la información necesaria para conectarse a la instancia de Almacenamiento de datos SQL de Azure para la propiedad connectionString. | Sí

Nota: tendrá que configurar [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Tendrá que configurar el servidor de base de datos para [permitir que los servicios de Azure tengan acceso al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos al almacenamiento de datos SQL de Azure desde fuera de Azure, incluidos orígenes de datos locales con puerta de enlace de la factoría de datos, debe configurar el intervalo de direcciones IP adecuado para el equipo que envía datos al almacenamiento de datos SQL de Azure.

## Propiedades de tipo de conjunto de datos Almacenamiento de datos SQL de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **AzureSqlDWTable** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla en la base de datos del almacenamiento de datos SQL de Azure a la que hace referencia el servicio vinculado. | Sí |

## Propiedades de tipo de actividad de copia de Almacenamiento de datos SQL de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

**Nota:** la actividad de copia toma solo una entrada y genera una única salida.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

### SqlDWSource

En caso de la actividad de copia si el origen es de tipo **SqlDWSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No |
| sqlReaderStoredProcedureName | Nombre del procedimiento almacenado que lee datos de la tabla de origen. | Nombre del procedimiento almacenado. | No |
| storedProcedureParameters | Parámetros del procedimiento almacenado. | Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | No |

Si se especifica **sqlReaderQuery** para SqlDWSource, la actividad de copia ejecuta la consulta en el origen de Almacenamiento de datos SQL de Azure para obtener los datos.

Como alternativa, puede especificar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en Almacenamiento de datos SQL de Azure. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

#### Ejemplo de SqlDWSource

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Definición del procedimiento almacenado:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
**SqlDWSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Inserta datos en la tabla SQL cuando el tamaño del búfer alcance writeBatchSize | Entero | No (valor predeterminado = 10000) |
| writeBatchTimeout | Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. | timespan<br/><br/> Ejemplo: "00:30:00" (30 minutos). | No | 
| sqlWriterCleanupScript | Consulta especificada por el usuario para que la actividad de copia se ejecute de tal forma que se limpien los datos de un segmento específico. Consulte la sección sobre repetibilidad a continuación para obtener más detalles. | Una instrucción de consulta. | No |
| allowPolyBase | Indica si se usa PolyBase (si procede) en lugar de mecanismo BULKINSERT para cargar datos en Almacenamiento de datos SQL de Azure. <br/><br/>Tenga en cuenta que solo se admite el conjunto de datos **Blob de Azure** con **formato** establecido en **TextFormat** como conjunto de datos de origen en este momento y que en breve otros tipos de orígenes serán compatibles. <br/><br/>Consulte [Uso de PolyBase para cargar datos en el Almacenamiento de datos SQL](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver las restricciones y obtener más información. | True <br/>False (valor predeterminado) | No |  
| polyBaseSettings | Un grupo de propiedades que se pueden especificar si el valor de la propiedad **allowPolybase** está establecido en **True**. | &nbsp; | No |  
| rejectValue | Especifica el número o porcentaje de filas que se pueden rechazar antes de que se produzca un error en la consulta. <br/><br/>Más información sobre las opciones de rechazo de PolyBase en la sección **Argumentos** del tema [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (predeterminado), 1, 2, … | No |  
| rejectType | Especifica si se indica la opción rejectValue como un valor literal o un porcentaje. | Valor (predeterminado), Porcentaje | No |   
| rejectSampleValue | Determina el número de filas que se van a recuperar antes de que PolyBase vuelva a calcular el porcentaje de filas rechazadas. | 1, 2, … | Sí, si el valor de **rejectType** es **porcentaje**. |  
| useTypeDefault | Especifica cómo administrar los valores que faltan en archivos de texto delimitados cuando PolyBase recupera datos del archivo de texto.<br/><br/>Más información sobre esta propiedad en la sección de argumentos de [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (predeterminada) | No | 


#### Ejemplo de SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Uso de PolyBase para cargar datos en el Almacenamiento de datos SQL
Usar **PolyBase** es una manera eficaz de cargar grandes cantidades de datos en Almacenamiento de datos SQL de Azure con un alto rendimiento. Puede ver una gran mejora en el rendimiento mediante el uso de PolyBase en lugar del mecanismo BULKINSERT predeterminado.

Establezca el valor de la propiedad **allowPolyBase** en **True**, tal y como se muestra en el ejemplo siguiente de Data Factory de Azure para usar PolyBase con el fin de copiar datos de Almacenamiento de blobs de Azure a Almacenamiento de datos SQL de Azure. Cuando se establece allowPolyBase en true, puede especificar las propiedades específicas de PolyBase mediante el grupo de propiedades **polyBaseSettings**. Consulte la sección [SqlDWSink](#SqlDWSink) anterior para más información acerca de las propiedades que puede utilizar con polyBaseSettings.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

### Copias directas con PolyBase
Si el origen de datos cumple los criterios siguientes, puede realizar las copias directamente desde el almacén de datos de origen a Almacenamiento de datos SQL de Azure mediante PolyBase con referencia a la configuración de ejemplo anterior. De lo contrario, puede aprovechar [Copias almacenadas provisionalmente con PolyBase](#staged-copy-using-polybase).

Tenga en cuenta que, si no se cumplen los requisitos, Data Factory de Azure comprobará la configuración y volverá automáticamente al mecanismo BULKINSERT para realizar el movimiento de datos.

1.	**Servicio vinculado de origen** es de tipo **Almacenamiento de Azure** y no está configurado para utilizar la autenticación de SAS (firma de acceso compartido). Consulte [Servicio vinculado de Almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para más información.
2. El **conjunto de datos de entrada ** es de tipo **Blob de Azure** y el tipo de formato en las propiedades de tipo es **OrcFormat** o **TextFormat** con las configuraciones siguientes:
	1. **rowDelimiter** debe ser **\\n**.
	2. **nullValue** está establecido en **cadena vacía** ("").
	3. **encodingName** está establecido en **utf-8**, el valor **predeterminado**, por lo que no se establece en un valor diferente.
	4. **escapeChar** y **quoteChar** no se han especificado.
	5. **Compression** no es **BZIP2**.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	No hay ninguna configuración de **skipHeaderLineCount** en **BlobSource** para la actividad de copia de la canalización.
4.	No hay ninguna configuración de **sliceIdentifierColumnName** en **SqlDWSink** para la actividad de copia de la canalización. (PolyBase garantiza que todos los datos se actualizan o que no se actualiza ninguno en una única ejecución). Para lograr la **repetibilidad**, podría usar **sqlWriterCleanupScript**.
5.	No se usa ningún receptor **columnMapping** en la actividad de copia asociada.

### Copias almacenadas provisionalmente con PolyBase
Cuando los datos de origen no cumplan los criterios de la sección anterior, puede habilitar la copia de datos a través de un almacenamiento de blobs de Azure provisional, en cuyo caso Data Factory de Azure realizará las transformaciones necesarias en los datos para que se cumplan los requisitos de PolyBase. Después, utilizará PolyBase para cargarlos en Almacenamiento de datos SQL. Consulte [Copias almacenadas provisionalmente](data-factory-copy-activity-performance.md#staged-copy) para más información sobre cómo funciona en general la copia de datos por medio de un blob de Azure de almacenamiento provisional.

> [AZURE.IMPORTANT] Si va a copiar datos de un almacén de datos local a Almacenamiento de datos SQL de Azure mediante PolyBase y almacenamiento provisional, debe instalar JRE 8 (Java Runtime Environment) en la máquina de puerta de enlace que se usará para transformar los datos de origen en un formato correcto. Tenga en cuenta que una puerta de enlace de 64 bits requiere JRE de 64 bits, y una de 32 bits JRE de 32 bits. Descargue la versión adecuada de [ubicación de descargas de Java](http://go.microsoft.com/fwlink/?LinkId=808605).

Para usar esta característica, cree un [servicio vinculado de Almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que haga referencia a la cuenta de Almacenamiento de Azure que tenga el Almacenamiento de blobs provisional. Después, especifique las propiedades **enableStaging** y **stagingSettings** de la actividad de copia tal y como se muestra a continuación:

	"activities":[  
	{
		"name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDWOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlDwSink",
				"allowPolyBase": true
			},
    		"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob"
			}
		}
	}
	]


### Procedimientos recomendados al usar PolyBase

#### Limitación de tamaño de fila
Polybase no admite filas de tamaño superior a 32 KB. Si se intenta cargar una tabla con filas de más de 32 KB, daría como resultado el siguiente error:

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Si tiene datos de origen con filas mayores de 32 KB, puede dividir las tablas de origen verticalmente en varias más pequeñas, donde el tamaño de fila mayor de cada una de ellas no supere el límite. Las tablas más pequeñas se pueden cargadas con PolyBase y se combinan en el Almacenamiento de datos SQL de Azure.

#### tableName en Almacenamiento de datos SQL de Azure
En la tabla siguiente se proporcionan ejemplos sobre cómo especificar la propiedad **tableName** en el conjunto de datos JSON para diversas combinaciones de nombre de tabla y esquema.

| Esquema de base de datos | Nombre de tabla | Propiedad JSON tableName |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable o [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] o [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Si ve un error, como se muestra a continuación, podría deberse a un problema con el valor especificado para la propiedad tableName. Consulte en la tabla anterior la forma correcta de especificar los valores para la propiedad tableName de JSON.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Columnas con valores predeterminados
Actualmente, la característica PolyBase en Data Factory solo acepta el mismo número de columnas que la tabla de destino. Supongamos que tiene una tabla con cuatro columnas y una de ellas se define con un valor predeterminado, los datos de entrada deben contener todavía cuatro columnas. Si se proporciona un conjunto de datos de entrada de tres columnas, se podría producir un error, tal como se muestra a continuación:

	All columns of the table must be specified in the INSERT BULK statement.

El valor NULL es una forma especial de valor predeterminado. Si la columna admite valores NULL, los datos de entrada (en el blob) para esa columna pueden estar vacíos (no pueden faltar en el conjunto de datos de entrada). PolyBase insertará valores NULL para ellos en Almacenamiento de datos SQL de Azure.


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para Almacenamiento de datos SQL de Azure

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a y desde SQL de Azure, SQL Server y Sybase, se usarán las siguientes asignaciones del tipo SQL al tipo .NET, y viceversa.

La asignación es igual que la asignación de [tipo de datos de SQL Server para ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de datos SQL Server | Tipo .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Booleano |
| char | String, Char |
| fecha | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte |
| Float | Doble |
| imagen | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Object * |
| text | String, Char |
| Twitter en tiempo | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

<!---HONumber=AcomDC_0629_2016-->