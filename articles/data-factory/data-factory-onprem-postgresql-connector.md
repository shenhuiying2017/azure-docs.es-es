---
title: Movimiento de datos de PostgreSQL mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información acerca de cómo mover los datos de la base de datos de PostgreSQL mediante Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: dd8a68029449ad013c4df9a46c558efaefd20e96
ms.openlocfilehash: 464db2456b4bd63ee4f0aae95214f2c9fcc3ba41


---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Movimiento de datos de PostgreSQL mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en Factoría de datos de Azure para mover datos de PostgreSQL a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

El servicio Factoría de datos admite la conexión a orígenes de PostgreSQL locales mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

> [!NOTE]
> La puerta de enlace es necesaria incluso si la base de datos PostgreSQL está hospedada en una máquina virtual de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual de IaaS como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.
>
>

Factoría de datos solo admite mover datos desde PostgreSQL a otros almacenes de datos, no desde otros almacenes de datos a PostgreSQL.

## <a name="supported-versions-and-installation"></a>Versiones compatibles e instalación
Para que la puerta de enlace de administración de datos se conecte a la base de datos de PostgreSQL, es preciso instalar la versión 2.0.12 o posterior del [proveedor de datos Ngpsql para PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) en el mismo sistema que la puerta de enlace de administración de datos. Se admite la versión 7.4 o posterior de PostgreSQL.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.
>
>

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos PostgreSQL en cualquiera de los almacenes de datos receptores admitidos es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde la base de datos PostgreSQL al almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.   

## <a name="sample-copy-data-from-postgresql-to-azure-blob"></a>Ejemplo: copia de datos de PostgreSQL a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos PostgreSQL a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. La [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos PostgreSQL en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure Data Management Gateway. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de PostgreSQL:**

    {
        "name": "OnPremPostgreSqlLinkedService",
        "properties": {
            "type": "OnPremisesPostgreSql",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Servicio vinculado de almacenamiento de blobs de Azure:**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
      }
    }

**Conjunto de datos de entrada de PostgreSQL**

El ejemplo supone que ha creado una tabla "MyTable" en PostgreSQL y que contiene una columna denominada "timestamp" para los datos de serie temporal.

Si se establece "external": true, se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

    {
        "name": "PostgreSqlDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremPostgreSqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

    {
        "name": "AzureBlobPostgreSqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla public.usstates de la base de datos PostgreSQL.

    {
        "name": "CopyPostgreSqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"public\".\"usstates\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "PostgreSqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobPostgreSqlDataSet"
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
                    "name": "PostgreSqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="postgresql-linked-service-properties"></a>Propiedades del servicio vinculado de PostgreSQL
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de PostgreSQL.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesPostgreSql** |Sí |
| server |Nombre del servidor de PostgreSQL. |Sí |
| database |Nombre de la base de datos de PostgreSQL. |Sí |
| schema |Nombre del esquema de la base de datos. El nombre del esquema distingue mayúsculas de minúsculas. |No |
| authenticationType |Tipo de autenticación usado para conectarse a la base de datos de PostgreSQL. Los valores posibles son: Anonymous, Basic y Windows. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación Basic o Windows. |No |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |No |
| gatewayName |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos de PostgreSQL local. |Sí |

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información sobre cómo establecer las credenciales para un origen de datos de PostgreSQL local.

## <a name="postgresql-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de PostgreSQL
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de PostgreSQL) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de Base de datos de PostgreSQL a la que hace referencia el servicio vinculado. tableName distingue mayúsculas de minúsculas. |No (si se especifica **query** de **RelationalSource**) |

## <a name="postgresql-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de PostgreSQL
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Cuando la actividad de copia es de tipo **RelationalSource** (lo que incluye PostgreSQL), están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: "query": "select * from \"MySchema\".\"MyTable\"". |No (si se especifica **tableName** de **dataset**) |

> [!NOTE]
> Los nombres de esquemas y tabla distinguen mayúsculas de minúsculas y deben ir entre "" (comillas) en la consulta.  
>
>

**Ejemplo:**

 "query": "select * from \"MySchema\".\"MyTable\""

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-postgresql"></a>Asignación de tipos para PostgreSQL
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a PostgreSQL, se usan las asignaciones siguientes de tipo PostgreSQL a tipo .NET.

| Tipo de Base de datos de PostgreSQL | Alias de PostgresSQL | Tipo .NET Framework |
| --- | --- | --- |
| abstime | |Datetime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit | |Byte[], String |
| bit variable [(n)] |varbit |Byte[], String |
| boolean |booleano |boolean |
| Box | |Byte[], String |
| bytea | |Byte[], String |
| character [ (n) ] |char [ (n) ] |String |
| character varying [ (n) ] |varchar [ (n) ] |String |
| cid | |String |
| cidr | |String |
| circle | |Byte[], String |
| fecha | |Datetime |
| daterange | |String |
| double precision |float8 |Doble |
| inet | |Byte[], String |
| intarry | |String |
| int4range | |String |
| int8range | |String |
| integer |int, int4 |Int32 |
| interval [ fields ] [ (p) ] | |TimeSpan |
| json | |String |
| jsonb | |Byte[] |
| line | |Byte[], String |
| lseg | |Byte[], String |
| macaddr | |Byte[], String |
| money | |Decimal |
| numeric [ (p, s) ] |decimal [ (p, s) ] |Decimal |
| numrange | |String |
| oid | |Int32 |
| path | |Byte[], String |
| pg_lsn | |Int64 |
| point | |Byte[], String |
| polygon | |Byte[], String |
| real |float4 |Single |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serial |serial4 |Int32 |
| text | |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Jan17_HO4-->


