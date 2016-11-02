<properties 
    pageTitle="Traslado de datos hacia el Almacenamiento de datos SQL de Azure y desde él | Microsoft Azure" 
    description="Obtenga información acerca de cómo mover los datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure" 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="jingwang"/>


# <a name="move-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure

En este artículo se describe cómo se puede usar la actividad de copia en Azure Data Factory para mover datos entre Azure SQL Data Warehouse y otro almacén de datos. 

Puede especificar si desea utilizar PolyBase al cargar los datos en el Almacenamiento de datos SQL de Azure. Se recomienda utilizar PolyBase para obtener el mejor rendimiento a la hora de cargar datos en el Almacenamiento de datos SQL Azure. Consulte [Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información. 


## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos hacia y desde Almacenamiento de datos SQL es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos. 

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En ellos se muestra cómo copiar datos entre Almacenamiento de datos SQL y Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.


> [AZURE.NOTE] 
> Para información general sobre el servicio Data Factory de Azure, consulte [Introducción al servicio Data Factory de Azure](data-factory-introduction.md). 
> 
> En este artículo se proporcionan ejemplos de JSON, pero no se incluyen instrucciones detalladas para crear una factoría de datos. Consulte [Tutorial: Copia de datos de Almacenamiento de blobs en Base de datos SQL mediante Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver un tutorial rápido con instrucciones detalladas para usar la actividad de copia en Data Factory de Azure. 


## <a name="sample:-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Ejemplo: copia de datos del almacenamiento de datos SQL de Azure a un blob de Azure

El ejemplo define las siguientes entidades de Data Factory:

1. Un servicio vinculado de tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties). 
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos que pertenecen a una serie temporal (por horas, días, etc.) desde una tabla de una base de datos de Azure SQL Data Warehouse a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

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
 
Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
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

> [AZURE.NOTE] En el ejemplo, **sqlReaderQuery** se especifica para SqlDWSource. La actividad de copia ejecuta esta consulta en el origen de Almacenamiento de datos SQL de Azure para obtener los datos.
>  
> Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
>  
> Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (seleccione column1, column2 en mytable) y ejecutarla en Almacenamiento de datos SQL de Azure. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

## <a name="sample:-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Ejemplo: copia de datos desde un blob de Azure al almacenamiento de datos SQL de Azure

El ejemplo define las siguientes entidades de Data Factory:

1.  Un servicio vinculado de tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.  Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.  Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.  Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


El ejemplo copia los datos de la serie temporal (por horas, días, etc.) de un blob de Azure a una tabla de una base de datos de Azure SQL Data Warehouse cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos. 

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

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
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

El ejemplo copia los datos a una tabla denominada "MyTable" en el almacenamiento de datos SQL de Azure. Cree la tabla en Azure SQL Data Warehouse con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora. 

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

Consulte el artículo [Carga de datos con Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) en la documentación de Azure SQL Data Warehouse para ver un tutorial.

## <a name="azure-sql-data-warehouse-linked-service-properties"></a>Propiedades del servicio vinculado Almacenamiento de datos SQL de Azure

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado del almacenamiento de datos SQL de Azure. 

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
type | La propiedad type debe establecerse en: **AzureSqlDW** | Sí
**connectionString** | Especifique la información necesaria para conectarse a la instancia de Almacenamiento de datos SQL de Azure para la propiedad connectionString. | Sí

> [AZURE.IMPORTANT] Configure el [firewall de Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) y el servidor de bases de datos para [permitir que los servicios de Azure tengan acceso al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos a Azure SQL Data Warehouse desde fuera de Azure, incluidos orígenes de datos locales con puerta de enlace de la factoría de datos, debe configurar el intervalo de direcciones IP adecuado para el equipo que envía datos a Azure SQL Data Warehouse. 

## <a name="azure-sql-data-warehouse-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos Almacenamiento de datos SQL de Azure

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.). 

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **AzureSqlDWTable** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tableName | Nombre de la tabla en la base de datos del almacenamiento de datos SQL de Azure a la que hace referencia el servicio vinculado. | Sí |

## <a name="azure-sql-data-warehouse-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Almacenamiento de datos SQL de Azure

Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [AZURE.NOTE]
La actividad de copia toma solo una entrada y genera una única salida.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

### <a name="sqldwsource"></a>SqlDWSource

Si el origen es de tipo **SqlDWSource**, estarán disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| SqlReaderQuery | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL. Por ejemplo: select * from MyTable. | No |
| sqlReaderStoredProcedureName | Nombre del procedimiento almacenado que lee datos de la tabla de origen. | Nombre del procedimiento almacenado. | No |
| storedProcedureParameters | Parámetros del procedimiento almacenado. | Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | No |

Si se especifica **sqlReaderQuery** para SqlDWSource, la actividad de copia ejecuta la consulta en el origen de Almacenamiento de datos SQL de Azure para obtener los datos. 

Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros). 

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta y ejecutarla en Azure SQL Data Warehouse. Ejemplo: `select column1, column2 from mytable`. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

#### <a name="sqldwsource-example"></a>Ejemplo de SqlDWSource

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
 

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Inserta datos en la tabla SQL cuando el tamaño del búfer alcance writeBatchSize | Entero (número de filas) | No (valor predeterminado = 10000) |
| writeBatchTimeout | Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. | timespan<br/><br/>  Ejemplo: "00:30:00" (30 minutos). | No | 
| sqlWriterCleanupScript | Especifique una consulta para que se ejecute la actividad de copia de tal forma que se limpien los datos de un segmento específico. Consulte la [sección sobre repetibilidad](#repeatability-during-copy)para más información. | Una instrucción de consulta.  | No |
| allowPolyBase | Indica si se usa PolyBase (si procede) en lugar de mecanismo BULKINSERT para cargar datos en Almacenamiento de datos SQL de Azure. <br/><br/>Actualmente, solo se admiten conjuntos de datos de **blobs de Azure** con **formato** establecido en **TextFormat** como conjunto de datos de origen. <br/><br/>Consulte [Uso de PolyBase para cargar datos en Almacenamiento de datos SQL](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver restricciones y más información. | True  <br/>False (valor predeterminado) | No |  
| polyBaseSettings | Un grupo de propiedades que se pueden especificar si el valor de la propiedad **allowPolybase** está establecido en **true**. | &nbsp; | No |  
| rejectValue | Especifica el número o porcentaje de filas que se pueden rechazar antes de que se produzca un error en la consulta. <br/><br/>Más información sobre las opciones de rechazo de PolyBase en la sección **Argumentos** del tema [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) [CREAR UNA TABLA EXTERNA (Transact-SQL)]. | 0 (predeterminado), 1, 2, … | No |  
| rejectType | Especifica si se indica la opción rejectValue como un valor literal o un porcentaje. | Valor (predeterminado), Porcentaje | No |   
| rejectSampleValue | Determina el número de filas que se van a recuperar antes de que PolyBase vuelva a calcular el porcentaje de filas rechazadas. | 1, 2, … | Sí, si el valor de **rejectType** es **percentage** |  
| useTypeDefault | Especifica cómo administrar los valores que faltan en archivos de texto delimitados cuando PolyBase recupera datos del archivo de texto.<br/><br/>Más información sobre esta propiedad en la sección de argumentos de [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (predeterminada) | No | 


#### <a name="sqldwsink-example"></a>Ejemplo de SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Movimiento de datos hacia y desde Almacenamiento de datos SQL de Azure mediante Factoría de datos de Azure
Usar **PolyBase** es una manera eficaz de cargar grandes cantidades de datos en Almacenamiento de datos SQL de Azure con un alto rendimiento.  Puede ver una gran mejora en el rendimiento mediante el uso de PolyBase en lugar del mecanismo BULKINSERT predeterminado.

Establezca el valor de la propiedad **allowPolyBase** en **true**, tal y como se muestra en el ejemplo siguiente de Azure Data Factory para usar PolyBase con el fin de copiar datos en Azure SQL Data Warehouse. Cuando se establece allowPolyBase en true, puede especificar las propiedades específicas de PolyBase mediante el grupo de propiedades **polyBaseSettings** . Consulte la sección [SqlDWSink](#SqlDWSink) para más información acerca de las propiedades que puede utilizar con polyBaseSettings.   


    "sink": {
        "type": "SqlDWSink",
        "allowPolyBase": true,
        "polyBaseSettings":
        {
            "rejectType": "percentage",
            "rejectValue": 10.0,
            "rejectSampleValue": 100,
            "useTypeDefault": true 
        }

    }

### <a name="direct-copy-using-polybase"></a>Copias directas con PolyBase
Si el origen de datos cumple los criterios descritos en esta sección, puede realizar las copias directamente desde el almacén de datos de origen a Azure SQL Data Warehouse mediante PolyBase. De lo contrario, puede usar [Copias almacenadas provisionalmente con PolyBase](#staged-copy-using-polybase).

Si no se cumplen los requisitos, Azure Data Factory comprobará la configuración y volverá automáticamente al mecanismo BULKINSERT para realizar el movimiento de datos.

1.  **Servicio vinculado de origen** es de tipo **Azure Storage** y no está configurado para utilizar la autenticación de SAS (firma de acceso compartido). Consulte [Servicio vinculado de Almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para más información.  
2. El **conjunto de datos de entrada** es de tipo **Blob de Azure** y el tipo de formato en las propiedades de tipo es **OrcFormat** o **TextFormat** con las configuraciones siguientes:
    1. **rowDelimiter** tiene que ser **\n**. 
    2. **nullValue** está establecido en **cadena vacía** (""). 
    3. **encodingName** está establecido en **utf-8**, el valor **predeterminado**, por lo que no debe establecerse en un valor diferente. 
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
3.  No hay ninguna configuración de **skipHeaderLineCount** en **BlobSource** para la actividad de copia en la canalización. 
4.  No hay ninguna configuración de **sliceIdentifierColumnName** en **SqlDWSink** para la actividad de copia en la canalización. (PolyBase garantiza que todos los datos se actualizan o que no se actualiza ninguno en una única ejecución). Para lograr la **capacidad de repetición**, podría usar **sqlWriterCleanupScript**.
5.  No se usa **columnMapping** en la actividad de copia asociada. 

### <a name="staged-copy-using-polybase"></a>Copias almacenadas provisionalmente con PolyBase
Si los datos de origen no cumplen los criterios que se introdujeron en la sección anterior, puede habilitar la copia de datos a través de un almacenamiento de blobs de Azure provisional. En este caso, Azure Data Factory realiza transformaciones en los datos para que cumplan con los requisitos de formato de datos de PolyBase y, a continuación, usa este para cargar datos en SQL Data Warehouse. Consulte [Copias almacenadas provisionalmente](data-factory-copy-activity-performance.md#staged-copy) para más información sobre cómo funciona en general la copia de datos por medio de un blob de Azure de almacenamiento provisional.

> [AZURE.IMPORTANT] Si va a copiar datos de un almacén de datos local a Azure SQL Data Warehouse mediante PolyBase y almacenamiento provisional, instale JRE 8 (Java Runtime Environment) en la máquina de puerta de enlace que se usa para transformar los datos de origen en un formato correcto. Una puerta de enlace de 64 bits requiere JRE de 64 bits y una de 32 bits, JRE de 32 bits. Descargue la versión adecuada de [la ubicación de descargas de Java](http://go.microsoft.com/fwlink/?LinkId=808605).

Para usar esta característica, cree un [servicio vinculado de Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) que haga referencia a la cuenta de Azure Storage que tenga el almacenamiento de blobs provisional. Después, especifique las propiedades **enableStaging** y **stagingSettings** de la actividad de copia tal y como se muestra en el siguiente código:

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


### <a name="best-practices-when-using-polybase"></a>Procedimientos recomendados al usar PolyBase

#### <a name="row-size-limitation"></a>Limitación de tamaño de fila
Polybase no admite filas de tamaño superior a 32 KB. Si se intenta cargar una tabla con filas de más de 32 KB, daría como resultado el siguiente error: 

    Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Si tiene datos de origen con filas mayores de 32 KB, puede dividir las tablas de origen verticalmente en varias más pequeñas, donde el tamaño de fila mayor de cada una de ellas no supere el límite. Las tablas más pequeñas se pueden cargadas con PolyBase y se combinan en el Almacenamiento de datos SQL de Azure.

#### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName en Almacenamiento de datos SQL de Azure
En la tabla siguiente se proporcionan ejemplos sobre cómo especificar la propiedad **tableName** en el conjunto de datos JSON para diversas combinaciones de nombre de tabla y esquema.

| Esquema de base de datos | Nombre de tabla | Propiedad JSON tableName |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable o [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] o [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Si ve el siguiente error, podría deberse a un problema con el valor especificado para la propiedad tableName. Consulte en la tabla la forma correcta de especificar los valores para la propiedad tableName de JSON.  

    Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### <a name="columns-with-default-values"></a>Columnas con valores predeterminados
Actualmente, la característica PolyBase en Data Factory solo acepta el mismo número de columnas que la tabla de destino. Supongamos que tiene una tabla con cuatro columnas y una de ellas está definida con un valor predeterminado. Los datos de entrada deberían seguir conteniendo cuatro columnas. Si se proporciona un conjunto de datos de entrada de tres columnas, se producirá un error parecido al siguiente mensaje:

    All columns of the table must be specified in the INSERT BULK statement.

El valor NULL es una forma especial de valor predeterminado. Si la columna admite valores NULL, los datos de entrada (en el blob) para esa columna pueden estar vacíos (no pueden faltar en el conjunto de datos de entrada). PolyBase insertará valores NULL para ellos en Azure SQL Data Warehouse.  


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-sql-data-warehouse"></a>Asignación de tipos para Almacenamiento de datos SQL de Azure

Como se mencionó en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de tipos, de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a y desde SQL de Azure, SQL Server y Sybase, se usarán las siguientes asignaciones del tipo SQL al tipo .NET, y viceversa.

La asignación es igual que la asignación de [tipo de datos de SQL Server para ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de datos SQL Server | Tipo .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte[] |
| bit | Booleano |
| char | String, Char[] |
| fecha | DateTime |
| DateTime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | Datetimeoffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte[] |
| Float | Doble |
| imagen | Byte[] | 
| int | Int32 | 
| money | Decimal |
| nchar | String, Char[] |
| ntext | String, Char[] |
| numeric | Decimal |
| nvarchar | String, Char[] |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql_variant | Object * |
| text | String, Char[] |
| Twitter en tiempo | timespan |
| timestamp | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary |  Byte[] |
| varchar | String, Char[] |
| xml | xml |



[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Oct16_HO2-->


