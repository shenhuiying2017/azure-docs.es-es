---
title: Movimiento de datos de Amazon Redshift mediante Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo mover datos desde Amazon Redshift mediante Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: e3079b55036a514b31249e5930f40ab4346f437a
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Movimiento de datos de Amazon Redshift mediante Azure Data Factory
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de Amazon Redshift. El artículo se basa en la información general sobre el movimiento de datos con la actividad de copia que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md). 

Puede copiar datos de Amazon Redshift en cualquier almacén de datos de receptor admitido. Consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores. Data Factory solo admite actualmente el movimiento de datos de Amazon Redshift a otros almacenes de datos, pero no de otros almacenes de datos a Amazon Redshift.

## <a name="prerequisites"></a>Requisitos previos
* Si mueve datos a un almacén de datos local, instale [Data Management Gateway](data-factory-data-management-gateway.md) en una máquina local. Conceda a Data Management Gateway (use la dirección IP del equipo) el acceso al clúster de Amazon Redshift. Consulte [Authorize access to the cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autorización para acceder al clúster) para obtener instrucciones.
* Si va a mover datos a un almacén de datos de Azure, consulte [Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalos de direcciones IP de Azure Data Center) para ver los intervalos de direcciones IP de Compute y los intervalos SQL que se utilizan en los centros de datos de Azure.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un origen de Amazon Redshift mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copia**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor: 

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para obtener un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan para copiar los datos de un almacén de datos de Amazon Redshift, consulte la sección [Ejemplo de JSON: Copiar datos de Amazon Redshift a un blob de Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) de este artículo. 

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Amazon Redshift: 

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Amazon Redshift.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **AmazonRedshift**. |Sí |
| server |Dirección IP o nombre de host del servidor de Amazon Redshift. |Sí |
| puerto |El número del puerto TCP que el servidor de Amazon Redshift utiliza para escuchar las conexiones del cliente. |No, valor predeterminado: 5439 |
| database |Nombre de la base de datos de Amazon Redshift. |Sí |
| nombre de usuario |Nombre del usuario que tiene acceso a la base de datos. |Sí |
| contraseña |Contraseña para la cuenta de usuario. |Sí |

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy son similares para todos los tipos de conjunto de datos (Azure SQL, Azure Blob, Azure Table, etc.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos. Proporciona información acerca de la ubicación de los datos del almacén de datos. La sección typeProperties del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de Amazon Redshift) tiene las propiedades siguientes

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la base de datos de Amazon Redshift a la que hace referencia el servicio vinculado. |No (si se especifica **query** de **RelationalSource**) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen de la actividad de copia es del tipo **RelationalSource** (que incluye Amazon Redshift), las propiedades siguientes están disponibles en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. |No (si se especifica **tableName** de **dataset**) |

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>Ejemplo de JSON: Copiar datos de Amazon Redshift a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos de Amazon Redshift a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

* Un servicio vinculado de tipo [AmazonRedshift](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de Amazon Redshift en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de Amazon Redshift:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Servicio vinculado de Almacenamiento de Azure:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de datos de entrada de Amazon Redshift:**

La opción `"external": true` informa al servicio Data Factory de que el conjunto de datos es externo a la factoría de datos y no la produce ninguna actividad de dicha factoría. Establezca esta propiedad en true en un conjunto de datos de entrada no generado por una actividad en la canalización.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Actividad de copia en una canalización con el origen de Azure Redshift (RelationalSource) y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Asignación de tipos para Amazon Redshift
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a Amazon Redshift, se usan las asignaciones siguientes de los tipos Amazon Redshift a los tipos .NET.

| Tipo Amazon Redshift | Tipo basado en .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |Single |
| DOUBLE PRECISION |Doble |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |string |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes:

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia.

