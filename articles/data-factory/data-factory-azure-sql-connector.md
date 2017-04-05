---
title: Traslado de datos hacia Azure SQL Database y desde ella | Microsoft Docs
description: "Obtenga información acerca de cómo mover los datos hacia y desde la base de datos SQL de Azure mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: beec132956ed4d382517379a47da57db983e7b54
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Movimiento de datos hacia y desde Base de datos SQL de Azure mediante Factoría de datos de Azure
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos hacia Azure SQL Database y desde este servicio. Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).  

Puede copiar datos de cualquier almacén de datos de origen compatible a Azure SQL Database o de Azure SQL Database a cualquier almacén de datos del receptor compatible. Para ver la lista de almacenes de datos admitidos como orígenes o receptores por actividad de copia, consulte la tabla [Almacenes de datos y formatos que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos hacia Azure SQL Database y desde esta base de datos mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copia**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia. 

Si usa las herramientas o las API, realice los pasos siguientes para crear una canalización que mueva los datos de un almacén de datos de origen a un almacén de datos del receptor: 

1. Cree **servicios vinculados** para vincular los almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** que representen los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se utiliza el asistente, se crean automáticamente las definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para obtener ejemplos con definiciones de JSON para entidades de Data Factory que se utilizan para copiar datos a Azure SQL Database y desde esta base de datos, consulte la sección [Ejemplos de JSON](#json-examples) de este artículo. 

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Azure SQL Database: 

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un servicio vinculado SQL de Azure vincula una base de datos SQL de Azure a la factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado SQL de Azure.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **AzureSqlDatabase** |Sí |
| connectionString |Especifique la información necesaria para conectarse a la instancia de Base de datos SQL de Azure para la propiedad connectionString. |Sí |

> [!IMPORTANT]
> Configure el [firewall de Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) y el servidor de bases de datos para [permitir que los servicios de Azure accedan al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos a Azure SQL Database desde fuera de Azure, incluidos orígenes de datos locales con puerta de enlace de la factoría de datos, configure el intervalo de direcciones IP adecuado para el equipo que envía datos a Azure SQL Database.

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para especificar un conjunto de datos para representar datos de entrada o salida en una base de datos SQL de Azure, establezca la propiedad de tipo del conjunto de datos en **AzureSqlTable**. Establezca la propiedad **linkedServiceName** del conjunto de datos en el nombre del servicio vinculado SQL de Azure.  

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **AzureSqlTable** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla o vista en la instancia de Azure SQL Database a la que hace referencia el servicio vinculado. |Sí |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si va a mover datos desde una Azure SQL Database, establezca el tipo de origen en la actividad de copia en **SqlSource**. De igual forma, si va a mover datos a una Azure SQL Database, establezca el tipo de receptor en la actividad de copia en **SqlSink**. Esta sección proporciona una lista de propiedades admitidas por SqlSource y SqlSink.

### <a name="sqlsource"></a>SqlSource
En la actividad de copia, si el origen es de tipo **SqlSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Ejemplo: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. |Nombre del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado. |Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |

Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de Base de datos SQL de Azure para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta (`select column1, column2 from mytable`) y ejecutarla en Azure SQL Database. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

> [!NOTE]
> Cuando use **sqlReaderStoredProcedureName**, necesitará especificar un valor para la propiedad **tableName** del conjunto de datos JSON. Pero no se ha realizado ninguna validación en esta tabla.
>
>

### <a name="sqlsource-example"></a>Ejemplo de SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Definición del procedimiento almacenado:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: "00:30:00" (30 minutos). |No |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. |Entero (número de filas) |No (valor predeterminado = 10000) |
| sqlWriterCleanupScript |Especifique una consulta para que se ejecute la actividad de copia de tal forma que se limpien los datos de un segmento específico. Consulte la [sección sobre repetibilidad](#repeatability-during-copy)para más información. |Una instrucción de consulta. |No |
| sliceIdentifierColumnName |Especifique el nombre de columna para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. Consulte la [sección sobre repetibilidad](#repeatability-during-copy)para más información. |Nombre de columna de una columna con el tipo de datos binarios (32). |No |
| sqlWriterStoredProcedureName |Nombre del procedimiento almacenado que actualiza e inserta (operación de upsert) datos en la tabla de destino. |Nombre del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado. |Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |
| sqlWriterTableType |Especifique el nombre del tipo de tabla que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |Un nombre de tipo de tabla. |No |

#### <a name="sqlsink-example"></a>Ejemplo de SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples"></a>Ejemplos de JSON
En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre el Almacenamiento de blobs de Azure y la Base de datos SQL de Azure. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.

## <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Ejemplo: copia de datos de Azure SQL Database a un blob de Azure
El ejemplo define las siguientes entidades de Data Factory:

1. Un servicio vinculado de tipo [AzureSqlDatabase](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureSqlTable](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [blob de Azure](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [SqlSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia los datos de la serie temporal (por horas, días, etc.) de una tabla de Azure SQL Database en un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.  

**Servicio vinculado a Azure SQL Database:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte la sección [Servicio vinculado SQL de Azure](#linked-service) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Servicio vinculado de almacenamiento de blobs de Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Vea el artículo [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obtener la lista de propiedades admitidas por este servicio vinculado.


**Conjunto de datos de entrada SQL de Azure:**

El ejemplo supone que ha creado una tabla "MyTable" en SQL de Azure y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

Si se establece "external": "true", se informa al servicio Azure Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

```JSON
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
```

Consulte la sección [Propiedades de tipo de conjunto de datos SQL de Azure](#dataset) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.  

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
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
```
Consulte la sección [Propiedades de tipo de conjunto de datos Blob de Azure](data-factory-azure-blob-connector.md#dataset-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.  

**Actividad de copia en una canalización con el origen SQL y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

```JSON
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
```
En el ejemplo, **sqlReaderQuery** se especifica para SqlSource. La actividad de copia ejecuta esta consulta en el origen de Base de datos SQL de Azure para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura del conjunto de datos JSON se usan para crear una consulta y ejecutarla en Azure SQL Database. Por ejemplo: `select column1, column2 from mytable`. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

Vea la sección [Sql Source](#sqlsource) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obtener la lista de propiedades admitidas por SqlSource y BlobSink.

## <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Ejemplo: copia de datos de un blob de Azure a SQL Database
El ejemplo define las siguientes entidades de Data Factory:  

1. Un servicio vinculado de tipo [AzureSqlDatabase](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSqlTable](#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) y [SqlSink](#copy-activity-properties).

El ejemplo copia los datos de la serie temporal (por horas, días, etc.) de un blob de Azure a una tabla de Azure SQL Database cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado SQL de Azure:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Consulte la sección [Servicio vinculado SQL de Azure](#linked-service) para obtener la lista de propiedades admitidas por este servicio vinculado.

**Servicio vinculado de almacenamiento de blobs de Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Vea el artículo [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obtener la lista de propiedades admitidas por este servicio vinculado.


**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

```JSON
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
```
Consulte la sección [Propiedades de tipo de conjunto de datos Blob de Azure](data-factory-azure-blob-connector.md#dataset-properties) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Conjunto de datos de salida de Azure SQL Database:**

El ejemplo copia los datos a una tabla denominada "MyTable" en SQL de Azure. Cree la tabla en SQL de Azure con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

```JSON
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
```
Consulte la sección [Propiedades de tipo de conjunto de datos SQL de Azure](#dataset) para obtener la lista de propiedades admitidas por este tipo de conjunto de datos.

**Una actividad de copia en una canalización con el origen de blob y el receptor SQL:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **SqlSink**.

```JSON
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
            "type": "BlobSource",
            "blobColumnSeparators": ","
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
```
Vea la sección [Sql Sink](#sqlsink) y [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) para obtener la lista de propiedades admitidas por SqlSink y BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Columnas de identidad en la base de datos de destino
En esta sección se proporciona un ejemplo para copiar datos de una tabla de origen sin una columna de identidad en una tabla de destino con una columna de identidad.

**Tabla de origen:**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabla de destino:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
Observe que la tabla de destino tiene una columna de identidad.

**Definición de JSON del conjunto de datos de origen**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definición de JSON del conjunto de datos de destino**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }    
}
```

Tenga en cuenta que la tabla de origen y de destino tienen un esquema diferente (el destino tiene una columna adicional con identidad). En este escenario, debe especificar la propiedad **structure** de la definición del conjunto de datos de destino, que no incluye la columna de identidad.

## <a name="map-source-to-sink-columns"></a>Asignación de origen a columnas de receptor
Para más información sobre la asignación de columnas de conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="repeatable-copy"></a>Copia repetible
Cuando se copien datos a SQL Server Database, la actividad de copia anexa datos a la tabla de receptor de forma predeterminada. Para llevar a cabo UPSERT en su lugar, consulte el artículo [Repeatable write to SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) (Escritura repetible en SqlSink). 

Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se leen sin importar cuántas veces se ejecuta un segmento. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocación del procedimiento almacenado para el receptor SQL
Para obtener un ejemplo de invocación a un procedimiento almacenado de receptor SQL en una actividad de copia de una canalización, consulte el artículo [Invoke stored procedure for SQL sink in copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md) (Invocación de un procedimiento almacenado para el receptor SQL en la actividad de copia). 

## <a name="sql-database-to-net-type-mapping"></a>SQL Database para la asignación de tipo .NET
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a SQL de Azure y en sentido contrario, se usarán las siguientes asignaciones del tipo SQL al tipo .NET, y viceversa. La asignación es igual que la asignación de tipo de datos de SQL Server para ADO.NET.

| Tipo de motor de base de datos SQL Server | Tipo .NET Framework |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| fecha |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Doble |
| imagen |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| Twitter en tiempo |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

