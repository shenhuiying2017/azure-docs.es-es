---
title: Copia de datos en y desde el Almacenamiento de blobs de Azure | Microsoft Docs
description: 'Aprenda a copiar datos de blob en Data Factory de Azure. Use nuestro ejemplo: Copia de datos entre Almacenamiento de blobs de Azure y Base de datos SQL de Azure.'
keywords: datos de blob, copia de blobs de azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: jingwang

---
# <a name="move-data-to-and-from-azure-blob-using-azure-data-factory"></a>Movimiento de datos hacia y desde Blob de Azure mediante Factoría de datos de Azure
En este artículo se explica cómo usar la actividad de copia en Data Factory de Azure para mover datos desde y hacia el servicio Blob de Azure tomando como origen los datos de blobs de otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que presenta información general sobre el movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## <a name="supported-sources-and-sinks"></a>Orígenes y receptores compatibles
Consulte la tabla [Almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos admitidos como orígenes o receptores por actividad de copia. Puede mover datos de cualquier almacén de datos de origen compatible a Azure Blob Storage o de Azure Blob Storage a cualquier almacén de datos del receptor compatible.

La actividad de copia es compatible con la copia de datos desde y hacia cuentas de Azure Storage de propósito general y el almacenamiento de blobs en frío y en caliente. La actividad admite leer desde blobs en bloques, en anexos o en páginas, pero solo permite escribir en blobs en bloques. 

## <a name="create-pipeline"></a>Creación de una canalización
Puede crear una canalización con actividad de copia que mueva los datos desde Azure Blob Storage o hacia él mediante el uso de diferentes herramientas o API.  

* Asistente para copia
* Portal de Azure
* Visual Studio
* Azure PowerShell
* API de .NET
* API de REST

Consulte el [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia de diferentes formas.

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos a o desde el Almacenamiento de blobs de Azure es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos. 

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre el Almacenamiento de blobs de Azure y la Base de datos SQL de Azure. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) con la actividad de copia de Data Factory de Azure.

## <a name="sample:-copy-data-from-azure-blob-to-azure-sql"></a>Ejemplo: copia de datos de un blob de Azure a SQL de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [BlobSource](#azure-blob-copy-activity-type-properties) y [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

El ejemplo copia los datos de la serie temporal desde un blob de Azure a una tabla de SQL de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos. 

**Servicio vinculado SQL de Azure:**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Servicio vinculado de Almacenamiento de Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory admite dos tipos de servicios vinculados de Azure Storage: **AzureStorage** y **AzureStorageSas**. En el primer caso, especifique la cadena de conexión que incluye la clave de cuenta. En el segundo, especifique el Uri de firma de acceso compartido (SAS). Para más información, consulte la sección [Servicios vinculados](#linked-services).  

**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa a Data Factory de que la tabla es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

**Conjunto de datos de salida SQL de Azure:**

El ejemplo copia los datos a una tabla denominada "MyTable" en una base de datos SQL de Azure. Cree la tabla en la Base de datos SQL de Azure con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **SqlSink**. 

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink"
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

## <a name="sample:-copy-data-from-azure-sql-to-azure-blob"></a>Ejemplo: copia de datos SQL de Azure a un blob de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) y [BlobSink](#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos de la serie temporal desde una tabla de SQL de Azure a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos. 

**Servicio vinculado SQL de Azure:**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Servicio vinculado de Almacenamiento de Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory admite dos tipos de servicios vinculados de Azure Storage: **AzureStorage** y **AzureStorageSas**. En el primer caso, especifique la cadena de conexión que incluye la clave de cuenta. En el segundo, especifique el Uri de firma de acceso compartido (SAS). Para más información, consulte la sección [Servicios vinculados](#linked-services).  

**Conjunto de datos de entrada SQL de Azure:**

El ejemplo supone que ha creado una tabla "MyTable" en SQL de Azure y que contiene una columna denominada "timestampcolumn" para los datos de series temporales. 

Si se establece "external": "true", se informa al servicio Data Factory de que la tabla es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureSQLInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## <a name="linked-services"></a>Servicios vinculados
En los ejemplos se ha usado un servicio vinculado de tipo **AzureStorage** para vincular una cuenta de Azure Storage a una factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Almacenamiento de Azure.

Hay dos tipos de servicios vinculados que puede usar para vincular un almacenamiento de blobs de Azure a una Factoría de datos de Azure. Se trata del servicio vinculado **AzureStorage** y el servicio vinculado **AzureStorageSas**. El servicio vinculado de Almacenamiento de Azure proporciona a la factoría de datos acceso global al Almacenamiento de Azure. Mientras que el servicio vinculado de SAS (firma de acceso compartido) de Almacenamiento de Azure proporciona a la factoría de datos acceso restringido/controlado por tiempo al Almacenamiento de Azure. No existen otras diferencias entre estos dos servicios vinculados. Elija el servicio vinculado que se adapte a sus necesidades. En las siguientes secciones se ofrecen más detalles sobre estos dos servicios vinculados.

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-blob-dataset-type-properties"></a>Propiedades de tipo del conjunto de datos de Blob de Azure
En los ejemplos se ha usado un conjunto de datos de tipo **AzureBlob** para representar un contenedor de blob y una carpeta en un Azure Blob Storage. 

Para obtener una lista completa de las secciones y propiedades JSON disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información acerca de la ubicación, el formato, etc. de los datos del almacén de datos. La sección typeProperties del conjunto de datos de tipo **AzureBlob** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Ruta de acceso para el contenedor y la carpeta en el almacenamiento de blobs. Ejemplo: myblobcontainer\myblobfolder\ |Sí |
| fileName |Nombre del blob. La propiedad fileName es opcional y distingue entre mayúsculas y minúsculas.<br/><br/>Si especifica fileName, la actividad (incluida la copia) funciona en el blob específico.<br/><br/>Cuando no se especifica fileName, la copia incluirá todos los blobs de folderPath para el conjunto de datos de entrada.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo tendría este formato:Data.<Guid>.txt (por ejemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy es una propiedad opcional. Puede usarla para especificar un folderPath dinámico y un nombre de archivo para datos de series temporales. Por ejemplo, se puede parametrizar folderPath por cada hora de datos. Consulte la sección [Uso de la propiedad partitionedBy](#using-partitionedBy-property) para ver información detallada y ejemplos. |No |
| formato |Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Consulte las secciones [Especificación de TextFormat](#specifying-textformat), [Especificación de AvroFormat](#specifying-avroformat), [Especificación de JsonFormat](#specifying-jsonformat), [Especificación de OrcFormat](#specifying-orcformat) y [Especificación de ParquetFormat](#specifying-parquetformat) para más información. Si desea copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), puede omitir la sección de formato en las definiciones de conjunto de datos de entrada y salida. |No |
| compresión |Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate** y **BZip2** y los niveles admitidos son: **Optimal** y **Fastest**. Para más información, vea la sección [Compatibilidad de compresión](#compression-support) . Actualmente, la configuración de la compresión no se admite para los datos con los formatos **AvroFormat**, **OrcFormat** o **ParquetFormat**. Para estos formatos, Data Factory utiliza el códec de compresión en los metadatos para leer los datos. Sin embargo, al escribir en un archivo en estos formatos, Data Factory elige el códec de compresión predeterminado para ese formato. Por ejemplo, SNAPPY para ParquetFormat y ZLIB para OrcFormat. |No |

### <a name="using-partitionedby-property"></a>Uso de la propiedad partitionedBy
Tal como se mencionó en la sección anterior, puede especificar un valor folderPath dinámico y un nombre de archivo para los datos de series temporales con la sección **partitionedBy** , macros de Data Factory y las variables del sistema SliceStart y SliceEnd, que indican las horas de inicio y de finalización de un segmento de datos especificado.

Consulte [Variables del sistema de Data Factory](data-factory-scheduling-and-execution.md#data-factory-system-variables) y [Referencia de las funciones de Data Factory](data-factory-scheduling-and-execution.md#data-factory-functions-reference) para obtener información sobre las funciones y las variables del sistema de Data Factory que puede usar en la sección partitionedBy.   

Para más información sobre los conjuntos de datos de series temporales, la programación y los segmentos, consulte los artículos [Creación de conjuntos de datos](data-factory-create-datasets.md) y [Programación y ejecución](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Ejemplo 1
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Ejemplo 2
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-blob-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Blob de Azure
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, conjuntos de datos de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si va a mover datos desde un Azure Blob Storage, establezca el tipo de origen en la actividad de copia en **BlobSource**. De igual forma, si va a mover datos desde un Azure Blob Storage, establezca el tipo de receptor en la actividad de copia en **BlobSink**. Esta sección proporciona una lista de propiedades admitidas por BlobSource y BlobSink. 

**BlobSource** admite las siguientes propiedades en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True (valor predeterminado), False |No |

**BlobSink** admite las siguientes propiedades en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| copyBehavior |Define el comportamiento de copia cuando el origen es BlobSource o FileSystem. |**PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen que apunta a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino que apunta a la carpeta de destino.<br/><br/>**FlattenHierarchy:** todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen un nombre generado automáticamente. <br/><br/>**MergeFiles: (valor predeterminado)** combina todos los archivos de la carpeta de origen en un solo archivo. Si se especifica el nombre de archivo/blob, el nombre de archivo combinado sería el nombre especificado; de lo contrario, sería el nombre de archivo generado automáticamente. |No |

**BlobSource** también admite estas dos propiedades para ofrecer compatibilidad con versiones anteriores. 

* **treatEmptyAsNull**: especifica si se debe tratar una cadena nula o vacía como un valor nulo.
* **skipHeaderLineCount** : especifica cuántas líneas deben omitirse. Es aplicable únicamente cuando el conjunto de datos de entrada usa TextFormat.

De forma similar, **BlobSink** admite la siguiente propiedad para ofrecer compatibilidad con versiones anteriores.

* **blobWriterAddHeader**: especifica si se debe agregar un encabezado de definiciones de columna al escribir en un conjunto de datos de salida. 

Los conjuntos de datos ahora son compatibles con las siguientes propiedades que implementan la misma funcionalidad: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

En la tabla siguiente se proporciona orientación sobre cómo utilizar las nuevas propiedades de conjunto de datos en lugar de estas propiedades de origen/receptor de blob. 

| Propiedad de la actividad de copia | Propiedad de conjunto de datos |
|:--- |:--- |
| skipHeaderLineCount en BlobSource |skipLineCount y firstRowAsHeader. Las líneas se omiten en primer lugar y, a continuación, se lee la primera fila como encabezado. |
| treatEmptyAsNull en BlobSource |treatEmptyAsNull en el conjunto de datos de entrada |
| blobWriterAddHeader en BlobSink |firstRowAsHeader en el conjunto de datos de salida |

Consulte la sección [Especificación de TextFormat](#specifying-textformat) para obtener información detallada acerca de estas propiedades.    

### <a name="recursive-and-copybehavior-examples"></a>Ejemplos de recursive y copyBehavior
En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de valores recursive y copyBehavior. 

| recursive | copyBehavior | Comportamiento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Si la carpeta de origen Folder1 tiene esta estructura:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la misma estructura que la de origen<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Si la carpeta de origen Folder1 tiene esta estructura:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File5 |
| true |mergeFiles |Si la carpeta de origen Folder1 tiene esta estructura:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 + File3 + File4 + File 5 se combina en un archivo con un nombre de archivo generado automáticamente |
| false |preserveHierarchy |Si la carpeta de origen Folder1 tiene esta estructura:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |
| false |flattenHierarchy |Si la carpeta de origen Folder1 tiene esta estructura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File2<br/><br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |
| false |mergeFiles |Si la carpeta de origen Folder1 tiene esta estructura: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 se combina en un archivo con un nombre de archivo generado automáticamente. Nombre de archivo generado automáticamente para File1<br/><br/>No se selecciona la subcarpeta Subfolder1, que contiene los archivos File3, File4 y File5. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

<!--HONumber=Oct16_HO2-->


