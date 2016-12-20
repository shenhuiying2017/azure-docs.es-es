---
title: "Inserción de datos en un índice de Search mediante Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo insertar datos en un índice de Azure Search mediante el uso de Data Factory de Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: f1d7df6163336cd66600dc22ff72a2bc1f29a1d5
ms.openlocfilehash: 138ac79846a2e7d0ae4af59ce13b6d36cce05047

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Inserción de datos en un índice de Azure Search mediante el uso de Data Factory de Azure
En este artículo se describe cómo usar la actividad de copia para insertar datos de un almacén de datos local compatible con el servicio Data Factory a un índice de Azure Search. Los almacenes de datos de origen compatibles se muestran en la columna Se admite como origen de la tabla de [almacenes de datos y receptores que se admiten](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Este artículo se basa en el artículo sobre [movimiento de datos y actividad de copia](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones de almacén de datos admitidas.

Data Factory de Azure solo permite mover datos a Azure Search desde [almacenes de datos de origen local compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats). No se puede migrar información de Azure Search a otros almacenes de datos.

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
Para que el servicio Data Factory pueda conectarse con un almacén de datos local, instale la puerta de enlace de administración de datos en el entorno local. No podrá instalar la puerta de enlace en la misma máquina que hospeda el almacén de datos de origen ni en una independiente para evitar la competencia por los recursos con el almacén de datos. 

La puerta de enlace de administración de datos conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway. 

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de cualquiera de los orígenes de datos compatibles a Azure Search es usar el Asistente para copiar datos. Para ver un tutorial rápido, consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md).

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización usando [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En ellas se muestra cómo copiar datos de un servidor SQL Server local a un índice de Azure Search. Sin embargo, este proceso puede realizarse desde cualquiera de los almacenes de datos locales indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia de Data Factory de Azure.   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Ejemplo: Copiar los datos de un servidor SQL Server local a un índice de Azure Search

El ejemplo siguiente muestra:

1.  Un servicio vinculado de tipo [AzureSearch](#azure-search-linked-service-properties).
2.  Un servicio vinculado de tipo [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
3.  Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.  Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureSearchIndex](#azure-search-index-dataset-properties).
4.  Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) y [AzureSearchIndexSink](#azure-search-index-sink-properties).

En el ejemplo se copian datos de serie temporal de una base de datos de SQL Server local a un índice de Azure Search cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de ellos. 

En primer lugar, configure la puerta de enlace de administración de datos en la máquina local. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de Azure Search**:

```JSON
{   
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Servicio vinculado de SQL Server**

```JSON
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

**Conjunto de datos de entrada de SQL Server**

El ejemplo supone que ha creado una tabla "MyTable" en SQL Server y que contiene una columna denominada "timestampcolumn" para los datos de serie temporal. Puede consultar en varias tablas dentro de la misma base de datos mediante un único conjunto de datos, pero se debe utilizar una única tabla para typeProperty tableName del conjunto de datos.

Si se establece external: true, se informa al servicio Data Factory de que el conjunto de datos es externa a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```JSON
{
  "name": "SqlServerDataset",
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

**Conjunto de datos de salida Azure Search**:

En el ejemplo se copian los datos a un índice de Azure Search denominado "**products**". Data Factory no crea el índice. Para probar el ejemplo, cree un índice con este nombre. Cree el índice de Azure Search con el mismo número de columnas que el conjunto de datos de entrada. Las nuevas entradas se agregan al índice de Azure Search cada hora.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición JSON de la canalización, el tipo **source** se establece en **SqlSource**, y el tipo **sink**, en **AzureSearchIndexSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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


## <a name="azure-search-linked-service-properties"></a>Propiedades del servicio vinculado de Azure Search

En la tabla siguiente se proporcionan descripciones de los elementos JSON específicos del servicio vinculado de Azure Search.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en **AzureSearch**. | Sí |
| url | La URL del servicio Azure Search. | Sí |
| key | La clave de administración del servicio Azure Search. | Sí |

## <a name="azure-search-index-dataset-properties"></a>Propiedades del conjunto de datos de índices de Azure Search

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo sobre [creación de conjuntos de datos](data-factory-create-datasets.md) . Las secciones como estructura, disponibilidad y directiva de un JSON de conjunto de datos son similares para todos los tipos de conjunto de datos. La sección **typeProperties** es diferente para cada tipo de conjunto de datos. La sección typeProperties de un conjunto de datos del tipo **AzureSearchIndex** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| type | La propiedad type debe establecerse en **AzureSearchIndex**.| Sí |
| indexName | Nombre del índice de Azure Search. Data Factory no crea el índice. El índice debe existir en Azure Search. | Sí |


## <a name="azure-search-index-sink-properties"></a>Propiedades del receptor de índices de Azure Search
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [creación de canalizaciones](data-factory-create-pipelines.md) . Las propiedades (como el nombre, la descripción, las tablas de entrada y salida, y las distintas directivas) están disponibles en todos los tipos de actividades. Las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En la actividad de copia, si el origen es de tipo **AzureSearchIndexSink**, están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica si, cuando ya haya un documento en el índice, se realizará una operación de combinación o de reemplazo. Consulte la propiedad [WriteBehavior](#writebehavior-property).| Combinar (predeterminado)<br/>Cargar| No | 
| WriteBatchSize | Carga datos en el índice de Azure Search cuando el tamaño del búfer alcanza el valor de WriteBatchSize. Consulte la propiedad [WriteBatchSize](#writebatchsize-property) para obtener más información. | De 1 a 1000. El valor predeterminado es 1000. | No |

### <a name="writebehavior-property"></a>Propiedad WriteBehavior
AzureSearchSink realiza una operación upsert al escribir los datos. Es decir, al crear un documento, si la clave de este ya se encuentra en el índice de Azure Search, este servicio actualiza el documento existente en lugar de generar una excepción de conflicto.

AzureSearchSink proporciona los siguientes dos comportamientos de upsert (mediante el SDK de Azure Search):

- **Combinar**: combina todas las columnas del nuevo documento con el existente. En las columnas con valor null del nuevo documento, se conserva el valor del existente.
- **Cargar**: el nuevo documento reemplaza al existente. En cuanto a las columnas no especificadas en el nuevo documento, el valor se establece en null con independencia de que haya un valor distinto de null en el documento existente.

El comportamiento predeterminado es **Combinar**.

### <a name="writebatchsize-property"></a>Propiedad WriteBatchSize
Azure Search puede crear documentos como lotes. Un lote puede contener entre 1 y 1000 acciones. Una acción controla un documento para llevar a cabo la operación de combinación o de carga.

### <a name="data-type-support"></a>Compatibilidad con los tipos de datos 
En la tabla siguiente se especifica si se admite o no un tipo de datos de Azure Search. 

| Tipo de datos de Azure Search | Compatible con el receptor de Azure Search |
| ---------------------- | ------------------------------ |
| Cadena | Y | 
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Booleano | Y |
| DataTimeOffset | Y | 
| Matriz de cadenas | N | 
| GeographyPoint | N |

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificación de la definición de la estructura de los conjuntos de datos rectangulares
La sección structure de JSON de los conjuntos de datos es una sección **opcional** para tablas rectangulares (con filas y columnas) y contiene una colección de columnas de la tabla. Use la sección structure con el fin de proporcionar información de tipos para realizar conversiones de tipos o asignaciones de columnas. En las siguientes secciones se describen estas configuraciones con más detalle. 

Cada columna contiene las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| name | Nombre de la columna. | Sí |
| type | Tipo de datos de la columna. Consulte la sección de [conversiones de tipos](#type-conversion-sample) para obtener más información sobre cuándo debe especificar la información de tipos. | No |
| culture | Referencia cultural basada en .NET que se usará cuando se especifica el tipo y sea un tipo .NET `Datetime` o `Datetimeoffset`. El valor predeterminado es `en-us`.  | No |
| formato | Cadena de formato que se usará cuando se especifica el tipo y sea un tipo .NET `Datetime` o `Datetimeoffset`. | No |

En el ejemplo siguiente se muestra el JSON de la sección structure de una tabla con tres columnas: `userid`, `name` y `lastlogindate`.

```JSON
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
Utilice las siguientes directrices sobre cuándo incluir la información de la sección **structure** y qué incluir en ella.

- **Para los orígenes de datos estructurados** que almacenan el esquema de datos y la información de tipos junto con los propios datos (por ejemplo, SQL Server, Oracle, tablas de Azure, etc.), solo debe especificar la sección structure si asigna columnas de orígenes específicas en columnas concretas del receptor y sus nombres no son iguales. Vea los detalles en la sección de asignación de columnas. 

    Como se ha mencionado antes, la información de tipos es opcional en la sección structure. En los orígenes estructurados, la información de tipos está disponible como parte de la definición del conjunto de datos del almacén de datos, por lo que no debería incluir la información de tipos cuando incluya la sección structure.
- **En cuanto al esquema de los orígenes de datos de lectura (en concreto, el blob de Azure)**, puede elegir guardar los datos sin almacenar el esquema o la información de tipos con ellos. Para estos tipos de orígenes de datos, debe incluir la sección structure en los dos casos siguientes:
    - Asigna columnas de orígenes a columnas de receptores.
    - Cuando el conjunto de datos es un origen de una actividad de copia, puede proporcionar información de los tipos en la sección structure. Data Factory usa esta información para convertirlos a tipos nativos del receptor. Consulte el artículo sobre cómo [mover datos con el blob de Azure como origen y destino](data-factory-azure-blob-connector.md) para obtener más información.

### <a name="supported-net-based-types"></a>Tipos basados en .NET admitidos 
Data factory admite los siguientes valores de tipo basados en .NET compatible con CLS con el fin de proporcionar información de tipos en la sección structure del esquema de los orígenes de datos de lectura como el blob de Azure.

- Int16
- Int32 
- Int64
- Single
- Doble
- Decimal
- Booleano
- Cadena 
- Datetime
- Datetimeoffset
- TimeSpan 

Para Datetime y Datetimeoffset, también puede, si lo desea, especificar las cadenas culture y format para facilitar el análisis de la cadena de fecha y hora personalizada. Vea un ejemplo de conversión de tipos en la sección siguiente:


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) y las diversas formas de optimizarlo.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes: 

* [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para la creación de una canalización con una actividad de copia. 



<!--HONumber=Nov16_HO3-->


