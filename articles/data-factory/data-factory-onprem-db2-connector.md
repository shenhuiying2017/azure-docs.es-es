---
title: Movimiento de datos de DB2 mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información acerca de cómo mover los datos de la base de datos de DB2 mediante Factoría de datos de Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 6d54203797ad970d590b853b171b383708dbcb5d
ms.lasthandoff: 04/12/2017


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Movimiento de datos de DB2 mediante Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de Azure Data Factory para copiar datos de una base de datos de DB2 local en cualquier almacén de datos que aparezca en la columna Receptor de la sección sobre [orígenes y receptores compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En la actualidad, Data Factory solo permite mover datos de una base de datos DB2 a [almacenes de datos receptores admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats), pero no permite mover datos de otros almacenes de datos a una base de datos de DB2.

## <a name="prerequisites"></a>Requisitos previos
Data Factory admite la conexión a la base de datos de DB2 local mediante Data Management Gateway. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más información acerca de la puerta de enlace de administración de datos y el artículo [Movimiento de datos entre orígenes locales y la nube con la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace de una canalización de datos para mover los datos.

La puerta de enlace es necesaria incluso si DB2 está hospedado en una máquina virtual de IaaS de Azure. Puede instalar la puerta de enlace en la misma máquina virtual de IaaS como almacén de datos o en una máquina virtual diferente, siempre y cuando la puerta de enlace se pueda conectar a la base de datos.

La puerta de enlace de administración de datos proporciona un controlador de DB2 integrado, por lo tanto, no es necesario instalar manualmente los controladores cuando se copian datos de DB2.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.

## <a name="supported-versions"></a>Versiones compatibles
Este conector de DB2 admite las siguientes plataformas y versiones de IBM DB2 admite las versiones 9, 10 y 11 de SQL Access Manager (SQLAM) de la arquitectura distribuida de bases de datos relacionales (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Si aparece un error que indica: "No se encontró el paquete correspondiente a una solicitud de ejecución de la instrucción SQL. SQLSTATE = 51002 SQLCODE =-805 ", utilice una cuenta con privilegios elevados (de usuario avanzado o de administrador) para ejecutar la actividad de copia de una vez. A Después, el paquete necesario se creará automáticamente durante la copia. A continuación, puede cambiar al usuario normal para las ejecuciones de copia posteriores.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un almacén de datos DB2 local mediante el uso de diferentes herramientas o API. 

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos. 
- También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para un ejemplo con definiciones de JSON para entidades de Data Factory que se utilizan para copiar los datos de un almacén de datos DB2 local, consulte la sección [Ejemplo de JSON: Copiar datos de DB2 a un blob de Azure](#json-example-copy-data-from-db2-to-azure-blob) de este artículo. 

Las secciones siguientes proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un almacén de datos de DB2:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de DB2.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesDB2** |Sí |
| server |Nombre del servidor DB2. |Sí |
| database |Nombre de la base de datos DB2. |Sí |
| schema |Nombre del esquema de la base de datos. El nombre del esquema distingue mayúsculas de minúsculas. |No |
| authenticationType |Tipo de autenticación usado para conectarse a la base de datos DB2. Los valores posibles son: Anonymous, Basic y Windows. |Sí |
| nombre de usuario |Especifique el nombre de usuario si usa la autenticación Basic o Windows. |No |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. |No |
| gatewayName |Nombre de la puerta de enlace que debe usar el servicio Factoría de datos para conectarse a la base de datos DB2 local. |Sí |


## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo RelationalTable (que incluye el conjunto de datos de DB2) tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de base de datos DB2 a la que hace referencia el servicio vinculado. tableName distingue mayúsculas de minúsculas. |No (si se especifica **query** de **RelationalSource**) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

Si el origen es de tipo **RelationalSource** (que incluye DB2), están disponibles las siguientes propiedades en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: `"query": "select * from "MySchema"."MyTable""`. |No (si se especifica **tableName** de **dataset**) |

> [!NOTE]
> Los nombres de esquema y tabla distinguen mayúsculas de minúsculas. Incluya los nombres entre comillas dobles ("") en la consulta.  

**Ejemplo:**

```sql
"query": "select * from "DB2ADMIN"."Customers""
```


## <a name="json-example-copy-data-from-db2-to-azure-blob"></a>Ejemplo de JSON: Copiar datos de DB2 a un blob de Azure
Este ejemplo proporciona definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde la base de datos DB2 a Azure Blob Storage. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos cada hora de un resultado de consulta de una base de datos DB2 en un blob de Azure. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, instale y configure una puerta de enlace de administración de datos. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado DB2:**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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
```

**Servicio vinculado de almacenamiento de blobs de Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Conjunto de datos de entrada de DB2**

El ejemplo supone que ha creado una tabla "MyTable" en DB2 y que contiene una columna denominada "timestamp" para los datos de serie temporal.

Si se establece "external": true, se informa al servicio Data Factory de que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos. Tenga en cuenta que **type** se establece en **RelationalTable**.

```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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
```

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **RelationalSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la tabla Orders.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```


## <a name="type-mapping-for-db2"></a>Asignación de tipos para DB2
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a DB2, se usarán las asignaciones siguientes de tipo DB2 a tipo .NET.

| Tipo de base de datos DB2 | Tipo .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Entero |Int32 |
| BigInt |Int64 |
| Real |Single |
| Doble |Doble |
| Float |Doble |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Entero |Int32 |
| BigInt |Int64 |
| Real |Single |
| Doble |Doble |
| Float |Doble |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Hora |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |string |

## <a name="map-source-to-sink-columns"></a>Asignación de columnas de origen a columnas de receptor
Para obtener más información sobre la asignación de columnas del conjunto de datos de origen a las del conjunto de datos receptor, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones. Consulte [Lectura repetible de orígenes relacionales](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

