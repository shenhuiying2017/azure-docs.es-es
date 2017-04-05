---
title: Traslado de datos con SQL Server como origen y destino | Microsoft Docs
description: "Aprenda a mover los datos hacia y desde una base de datos SQL Server en un entorno local o en una máquina virtual de Azure mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 3ab6d21263ee2f4082cae8d772e715ed04d7d7c0
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Movimiento de los datos entre entornos locales de SQL Server o en IaaS (máquina virtual de Azure) mediante Factoría de datos de Azure
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de una base de datos SQL Server local. Se basa en la información general ofrecida por el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md). 

Puede mover datos de cualquier almacén de datos de origen compatible a SQL Server Database o de SQL Server Database a cualquier almacén de datos del receptor admitido. Consulte la tabla [Almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos admitidos como orígenes o receptores por actividad de copia. 

## <a name="supported-sql-server-versions"></a>Versiones admitidas de SQL Server
Este conector de SQL Server admite la copia de datos hacia y desde las siguientes versiones de la instancia hospedada local o en Azure IaaS mediante autenticación de SQL y autenticación de Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
Los conceptos y los pasos necesarios para conectar con SQL Server hospedado de forma local o en máquinas virtuales de IaaS de Azure (infraestructura como servicio) son los mismos. En ambos casos, tendrá que utilizar Data Management Gateway para establecer la conectividad.

Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. La configuración de una instancia de puerta de enlace es un requisito previo para conectarse con SQL Server.

Aunque puede instalar la puerta de enlace en la misma máquina local o una instancia de máquina virtual en la nube, como SQL Server, para mejorar el rendimiento, se recomienda instalarlos en máquinas independientes. Tener la puerta de enlace y SQL Server en máquinas diferentes reduce la contención de recursos.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde una base de datos de SQL Server local o hacia ella mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia. 

Tanto si usa las herramientas o las API, realice los pasos siguientes para crear una canalización que mueva los datos de un almacén de datos de origen a un almacén de datos del receptor: 

1. Cree **servicios vinculados** para vincular los almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** para representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome un conjunto de datos como entrada y un conjunto de datos como salida. 

Cuando se usa el asistente, se crean automáticamente definiciones JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver ejemplos con definiciones JSON de entidades de Data Factory que se usan para copiar datos a/desde una base de datos SQL Server local, consulte la sección [Ejemplos de JSON](#json-examples) de este artículo. 

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de SQL Server: 

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En los ejemplos se ha usado un servicio vinculado de tipo **OnPremisesSqlServer** para vincular una base de datos de SQL Server local a una factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de SQL Server local.

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio SQL Server vinculado.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type se debe establecer en **OnPremisesSqlServer**. |Sí |
| connectionString |Especifique la información de connectionString necesaria para conectarse a la Base de datos SQL Server local mediante autenticación de SQL o autenticación de Windows. |Sí |
| gatewayName |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la Base de datos SQL Server local. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación de Windows. Ejemplo: **nombreDeDominio\\nombreDeUsuario**. |No |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |No |

Puede cifrar las credenciales con el cmdlet **New-AzureRmDataFactoryEncryptValue** y usarlas en la cadena de conexión, como se muestra en el ejemplo siguiente (propiedad **EncryptedCredential**):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Muestras
**JSON para usar autenticación de SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlLinkedService",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON para usar autenticación de Windows**

Si se especifican el nombre de usuario y la contraseña, la puerta de enlace los usará para suplantar la cuenta de usuario especificada para conectarse a la Base de datos SQL Server local. En caso contrario, la puerta de enlace se conectará directamente a SQL Server con el contexto de seguridad de puerta de enlace (su cuenta de inicio).

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlLinkedService",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
En los ejemplos se ha usado un conjunto de datos de tipo **SqlServerTable** para representar una tabla en una base de datos de SQL Server.  

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Server, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección **typeProperties** del conjunto de datos de tipo **SqlServerTable** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de base de datos de SQL Server a la que hace referencia el servicio vinculado. |Sí |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Si va a mover datos desde una base de datos de SQL Server, establezca el tipo de origen en la actividad de copia en **SqlSource**. De igual forma, si va a mover datos a una base de datos de SQL Server, establezca el tipo de receptor en la actividad de copia en **SqlSink**. Esta sección proporciona una lista de propiedades admitidas por SqlSource y SqlSink.

Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

### <a name="sqlsource"></a>SqlSource
Cuando el origen es una actividad de copia de tipo **SqlSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. Es posible hacer referencia a varias tablas de la base de datos a la que hace referencia el conjunto de datos de entrada. Si no se especifica, la instrucción SQL que se ejecuta: select from MyTable. |No |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. |Nombre del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado. |Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |

Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de Base de datos SQL de Azure para obtener los datos.

Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura se usan para crear una consulta Select para ejecutarla en Base de datos de SQL Server. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

> [!NOTE]
> Cuando use **sqlReaderStoredProcedureName**, necesitará especificar un valor para la propiedad **tableName** del conjunto de datos JSON. Pero no se ha realizado ninguna validación en esta tabla.

### <a name="sqlsink"></a>SqlSink
**SqlSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: "00:30:00" (30 minutos). |No |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. |Entero (número de filas) |No (valor predeterminado = 10000) |
| sqlWriterCleanupScript |Especifique la consulta para que la actividad de copia se ejecute de tal forma que se limpien los datos de un segmento específico. Consulte la [sección sobre repetibilidad](#repeatability-during-copy) para más información. |Una instrucción de consulta. |No |
| sliceIdentifierColumnName |Especifique el nombre de columna para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. Consulte la [sección sobre repetibilidad](#repeatability-during-copy) para más información. |Nombre de columna de una columna con el tipo de datos binarios (32). |No |
| sqlWriterStoredProcedureName |Nombre del procedimiento almacenado que actualiza e inserta (operación de upsert) datos en la tabla de destino. |Nombre del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado. |Pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |
| sqlWriterTableType |Especifique el nombre del tipo de tabla que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |Un nombre de tipo de tabla. |No |


## <a name="json-examples"></a>Ejemplos de JSON
En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En los siguientes ejemplos, se muestra cómo copiar datos entre SQL Server y Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Ejemplo: Copia de datos de SQL Server a Blob de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [OnPremisesSqlServer](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [SqlServerTable](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [SqlSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia los datos de la serie temporal desde una tabla de SQL Server a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure Data Management Gateway. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Servicio vinculado de almacenamiento de blobs de Azure**

```json
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
**Conjunto de datos de entrada de SQL Server**

El ejemplo supone que ha creado una tabla "MyTable" en SQL Server y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal. Puede consultar en varias tablas dentro de la misma base de datos mediante un único conjunto de datos, pero se debe utilizar una única tabla para typeProperty tableName del conjunto de datos.

Si se establece external: true, se informa al servicio Data Factory de que el conjunto de datos es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
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
```
**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar estos conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **SqlSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
En este ejemplo, **sqlReaderQuery** se especifica para SqlSource. La actividad de copia ejecuta esta consulta en el origen de Base de datos de SQL Server para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros). sqlReaderQuery puede hacer referencia a varias tablas de la base de datos a la que hace referencia el conjunto de datos de entrada. No se limita exclusivamente a la tabla establecida como typeProperty tableName del conjunto de datos.

Si no especifica sqlReaderQuery ni sqlReaderStoredProcedureName, las columnas definidas en la sección sobre la estructura se usan para crear una consulta Select para ejecutarla en Base de datos de SQL Server. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.

Vea la sección [Sql Source](#sqlsource) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) para obtener la lista de propiedades admitidas por SqlSource y BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Ejemplo: Copia de datos de Blob de Azure a SQL Server
El ejemplo siguiente muestra:

1. El servicio vinculado de tipo [OnPremisesSqlServer](#linked-service-properties).
2. El servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) y [SqlSink](#sql-server-copy-activity-type-properties).

El ejemplo copia los datos de la serie temporal desde un blob de Azure a una tabla de SQL Server cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Servicio vinculado de almacenamiento de blobs de Azure**

```json
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
**Conjunto de datos de entrada de blob de Azure**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor external: true informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```json
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
```
**Conjunto de datos de salida de SQL Server**

El ejemplo copia los datos a una tabla denominada "MyTable" en SQL Server. Cree la tabla en SQL Server con el mismo número de columnas que espera que contenga el archivo CSV de blob. Se agregan nuevas filas a la tabla cada hora.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar estos conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Solución de problemas de conexión
1. Configure su SQL Server para que acepte conexiones remotas. Inicie **SQL Server Management Studio**, haga clic con el botón derecho en **Servidor** y después en **Propiedades**. Seleccione **Conexiones** en la lista y active **Permitir conexiones remotas con este servidor**.

    ![Habilitar conexiones remotas](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Consulte los pasos detallados de [Configurar la opción de configuración del servidor Acceso remoto](https://msdn.microsoft.com/library/ms191464.aspx) .
2. Inicie el **Administrador de configuración de SQL Server**. Expanda **Configuración de red de SQL Server** para la instancia que desee y seleccione **Protocols for MSSQLSERVER** (Protocolos para MSSQLSERVER). Debería ver protocolos en el panel derecho. Para habilitar TCP/IP, haga clic con el botón derecho en **TCP/IP** y haga clic en **Habilitar**.

    ![Habilitar TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Consulte [Habilitar o deshabilitar un protocolo de red de servidor](https://msdn.microsoft.com/library/ms191294.aspx) para ver información y maneras alternativas de habilitar el protocolo TCP/IP.
3. En la misma ventana, haga doble clic en **TCP/IP** para abrir la ventana **TCP/IP Properties** (Propiedades de TCP/IP).
4. Cambie a la pestaña **Direcciones IP** . Desplácese hacia abajo hasta la sección **IPAll** . Anote el valor de **Puerto TCP** (el valor predeterminado es **1433**).
5. Cree una **regla del Firewall de Windows** en la máquina para permitir el tráfico entrante a través de este puerto.  
6. **Compruebe la conexión**: use SQL Server Management Studio en una máquina diferente para conectarse a SQL Server con el nombre completo. Por ejemplo: <machine><domain>.corp<company>.com,1433.

   > [!IMPORTANT]

   > Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener información detallada.
   >
   > Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Columnas de identidad en la base de datos de destino
En esta sección se proporciona un ejemplo para copiar datos de una tabla de origen sin una columna de identidad en una tabla de destino con una columna de identidad.

**Tabla de origen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Tabla de destino:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observe que la tabla de destino tiene una columna de identidad.

**Definición de JSON del conjunto de datos de origen**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para más información sobre la asignación de columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="repeatable-copy"></a>Copia repetible
Cuando se copian datos en SQL Server Database, la actividad de copia anexa datos a la tabla del receptor de forma predeterminada. Para ejecutar una semántica UPSERT en su lugar, consulte el artículo [Escritura repetible en SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink). 

Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se leen sin importar cuántas veces se ejecuta un segmento. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocación del procedimiento almacenado desde el receptor de SQL
Para ver un ejemplo de invocación de un procedimiento almacenado desde el receptor de SQL en una actividad de copia de una canalización, consulte el artículo [Invoke stored procedure for SQL sink in copy activity](data-factory-invoke-stored-procedure-from-copy-activity.md) (Invocación de un procedimiento almacenado desde el receptor de SQL en la actividad de copia).

## <a name="type-mapping-for-sql-server--azure-sql"></a>Asignación de tipos para SQL Server y SQL de Azure
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a y desde SQL de Azure, SQL Server y Sybase, se usarán las siguientes asignaciones del tipo SQL al tipo .NET, y viceversa.

La asignación es igual que la asignación de tipo de datos de SQL Server para ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte el artículo [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

