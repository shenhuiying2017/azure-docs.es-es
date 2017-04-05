---
title: Movimiento de datos desde Amazon Simple Storage Service mediante Azure Data Factory | Microsoft Docs
description: Aprenda a mover datos desde Amazon Simple Storage Service (S3) mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 23d0092915c98da54ed486aed22afba4839befd1
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Movimiento de datos desde Amazon Simple Storage Service mediante Azure Data Factory
En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos de Amazon Simple Storage Service (S3). Se basa en la información general que ofrece el artículo [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md). 

Puede copiar datos de Amazon Simple Storage Service (S3) en cualquier almacén de datos de receptor admitido. Para ver una lista de almacenes de datos admitidos como receptores por la actividad de copia, consulte la tabla [Almacenes de datos y formatos que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory solo admite actualmente el movimiento de datos de Amazon S3 a otros almacenes de datos, pero no de otros almacenes de datos a Amazon S3.

## <a name="required-permissions"></a>Permisos necesarios
Para copiar datos de Amazon S3, asegúrese de que se han concedido los siguientes permisos:

* `s3:GetObject` y `s3:GetObjectVersion` para operaciones de objeto de Amazon S3
* `s3:ListBucket` para operaciones de depósito de Amazon S3. Si usa el Asistente para copia, `s3:ListAllMyBuckets` también es necesario.

Puede consultar la lista completa de los permisos de Amazon S3 en [Specifying Permissions in a Policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Especificación de permisos en una directiva).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un origen de Amazon S3 mediante el uso de diferentes herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copia**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia. 

Si usa las herramientas o las API, realice los pasos siguientes para crear una canalización que mueva los datos de un almacén de datos de origen a un almacén de datos del receptor: 

1. Cree **servicios vinculados** para vincular los almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** que representen los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se utiliza el asistente, se crean automáticamente las definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para obtener un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan para copiar los datos de un almacén de datos de Amazon S3, consulte la sección [Ejemplo de JSON: Copiar datos de Amazon S3 a un blob de Azure](#json-example-copy-data-from-amazon-s3-to-azure-blob) de este artículo. 

En las secciones siguientes se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de Amazon S3: 

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un servicio vinculado vincula un almacén de datos a una factoría de datos. Se crea un servicio vinculado de tipo **AwsAccessKey** para vincular el almacén de datos de Amazon S3 a la factoría de datos. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Amazon S3 (AwsAccessKey).

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| accessKeyID |Id. de la clave de acceso secreta. |string |Sí |
| secretAccessKey |La propia clave de acceso secreta. |Cadena secreta cifrada |Sí |

Este es un ejemplo: 

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para especificar un conjunto de datos para representar datos de entrada en Azure Blob Storage, establezca la propiedad de tipo del conjunto de datos en **AmazonS3**. Establezca la propiedad **linkedServiceName** del conjunto de datos en el nombre del servicio vinculado Amazon S3.  Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy son similares para todos los tipos de conjunto de datos (Azure SQL, Azure Blob, Azure Table, etc.). La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **AmazonS3** (que incluye el conjunto de datos de Amazon S3) tiene las propiedades siguientes:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| bucketName |Nombre del depósito de S3. |string |Sí |
| key |La clave del objeto S3. |string |No |
| prefix |Prefijo de la clave del objeto S3. Se seleccionan objetos cuyas claves comienzan por este prefijo. Se aplica solo cuando la clave está vacía. |string |No |
| versión |La versión del objeto S3 si está habilitado el control de versiones de S3. |string |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No | |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Los niveles admitidos son **Optimal** y **Fastest**. Para más información, consulte [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support) (Formatos de archivo y compresión en Azure Data Factory). |No | |


> [!NOTE]
> bucketName + key especifica la ubicación del objeto S3, donde bucket es el contenedor raíz de los objetos S3 y key es la ruta de acceso completa al objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de datos de ejemplo con prefijo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-data-set-with-version"></a>Conjunto de datos de ejemplo (con versión)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>Rutas de acceso dinámicas para S3
En el ejemplo, usamos valores fijos para las propiedades key y bucketName en el conjunto de datos de Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Puede hacer que Data Factory calcule estas propiedades dinámicamente en tiempo de ejecución mediante variables del sistema como SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Y lo mismo puede hacer para la propiedad prefix de un conjunto de datos de Amazon S3. Consulte [Azure Data Factory: funciones y variables del sistema](data-factory-functions-variables.md) para ver una lista de funciones y variables admitidas.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores. Si el origen en la actividad de copia es de tipo **FileSystemSource** (lo que incluye Amazon S3), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Especifica si se mostrarán objetos S3 de manera recursiva en el directorio. |true/false |No |


## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob"></a>Ejemplo de JSON: Copiar datos de Amazon S3 a un blob de Azure
En este ejemplo se muestra cómo copiar datos de Amazon S3 a Azure Blob Storage. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure. 

El ejemplo proporciona definiciones de JSON para las siguientes entidades de Data Factory. Puede utilizar estas definiciones para crear una canalización para copiar datos desde Amazon S3 a Azure Blob Storage mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* Un servicio vinculado de tipo [AwsAccessKey](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de tipo [AmazonS3](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos de Amazon S3 a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de Amazon S3:**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
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

**Conjunto de datos de entrada de Amazon S3:**

Si se establece **"external": true** , se informa al servicio Data Factory que el conjunto de datos es externo a la factoría de datos y que no lo genera ninguna actividad de la factoría de datos. Establezca esta propiedad en true en un conjunto de datos de entrada no generado por una actividad en la canalización.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Actividad de copia en una canalización con el origen de Amazon S3 (origen de sistema de archivos) y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).


## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes:

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia.

