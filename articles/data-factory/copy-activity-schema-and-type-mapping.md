---
title: "Asignación de esquemas en la actividad de copia | Microsoft Docs"
description: "Obtenga información acerca de cómo la actividad de copia de Azure Data Factory asigna esquemas y tipos de datos desde datos de origen hasta datos receptores al copiar datos."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: c1fde0abd2f5fa8e36c71707d14cd5afeb3a0d2d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="schema-mapping-in-copy-activity"></a>Asignación de esquemas en la actividad de copia
Este artículo describe el modo en que la actividad de copia de Azure Data Factory realiza la asignación de esquemas y la asignación del tipo de datos desde datos de origen hasta datos receptores al realizar la copia de datos.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).


## <a name="column-mapping"></a>Asignación de columnas

De forma predeterminada, la actividad de copia **asigna datos de origen a datos receptores por nombres de columna**, a menos que esté configurada la [asignación de columnas explícita](#explicit-column-mapping). Más concretamente, la actividad de copia:

1. Lee los datos desde el origen y determina el esquema de origen.

    * En el caso de los orígenes de datos con un esquema predefinido en el almacén de datos o el formato de archivo, como las bases de datos o los archivos con metadatos (Avro/ORC/Parquet/Texto con encabezado), el esquema de origen se extrae del resultado de la consulta o los metadatos del archivo.
    * Para los orígenes de datos con un esquema flexible, como Azure Table/Cosmos DB, el esquema de origen se deduce del resultado de la consulta. Es posible sobrescribirlo especificando el elemento "structure" en el conjunto de datos.
    * Para el archivo de texto sin encabezado, los nombres de columna predeterminados se generan con el patrón "Prop_0", "Prop_1"... Se pueden sobrescribir especificando el elemento "structure" en el conjunto de datos.
    * Para el origen de Dynamics, tendrá que proporcionar la información de esquema en la sección "structure" del conjunto de datos.

2. Aplique la asignación de columnas explícita si se especifica.

3. Escritura de datos en el receptor

    * En los almacenes de datos con un esquema definido previamente, los datos se escriben en las columnas con el mismo nombre.
    * En los almacenes de datos sin esquema fijo y en los formatos de archivo, los nombres de columna o metadatos se generarán en función del esquema de origen.

### <a name="explicit-column-mapping"></a>Asignación de columnas explícita

Puede especificar **columnMappings** en la sección **typeProperties** de la actividad de copia para realizar una asignación de columna explícita. En este escenario, la sección "structure" es necesaria para los conjuntos de datos de entrada y salida. La asignación de columnas admite **la asignación de todas las columnas o un subconjunto de ellas del conjunto de datos de origen "structure" a todas las columnas del conjunto de datos receptor "structure"**. Las siguientes son las condiciones de error que tienen como resultado una excepción:

* El resultado de la consulta del almacén de datos de origen no tiene un nombre de columna que se especifique en la sección "structure" del conjunto de datos de entrada.
* El almacén de datos receptor (si está en el esquema predefinido) no tiene un nombre de columna que se especifique en la sección "structure" del conjunto de datos de salida.
* O bien menos columnas o más columnas en "structure" del conjunto de datos receptor de las que se especifican en la asignación.
* Asignación duplicada.

#### <a name="explicit-column-mapping-example"></a>Ejemplo de asignación de columnas explícita

En este ejemplo, la tabla de entrada tiene una estructura y apunta a una tabla en una base de datos SQL local.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

En este ejemplo, la tabla de salida tiene una estructura y apunta a una tabla en una base de datos SQL de Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

El siguiente fragmento JSON define una actividad de copia en una canalización. Las columnas del origen se asignan a columnas del receptor (**columnMappings**) usando la propiedad **Translator**.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**Flujo de asignación de columnas:**

![Flujo de asignación de columnas](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Asignación de tipos de datos

La actividad de copia realiza la asignación de tipos de origen a tipos receptores con este proceso de dos pasos:

1. Conversión de los tipos de origen nativo a tipos de datos provisionales de Azure Data Factory
2. Conversión de los tipos de datos provisionales de Azure Data Factory al tipo de receptor nativo

Puede encontrar la asignación entre un tipo nativo y un tipo provisional en la sección "Asignación de tipos de datos" en cada tema de conector.

### <a name="supported-data-types"></a>Tipos de datos admitidos

Data Factory admite los siguientes tipos de datos provisionales: puede especificar valores inferiores al proporcionar la información de tipo en la configuración de la [estructura del conjunto de datos](concepts-datasets-linked-services.md#dataset-structure):

* Byte[]
* boolean
* DateTime
* Datetimeoffset
* DECIMAL
* Doble
* Guid
* Int16
* Int32
* Int64
* Single
* string
* TimeSpan

### <a name="explicit-data-type-conversion"></a>Conversión de tipos de datos explícitos

Al copiar datos en almacenes de datos con esquema fijo (como SQL Server y Oracle), cuando el origen y el receptor tienen un tipo diferente en la misma columna, la conversión de tipos explícitos debe declararse en el lado del origen:

* Para el origen de archivo, por ejemplo, CSV o Avro, la conversión de tipos se declarará a través de la estructura de origen con la lista de columnas completa (nombre de columna en el lado de origen y tipo en el lado receptor).
* Para el origen relacional (como SQL/Oracle), la conversión de tipos debe realizarse mediante el envío de tipo explícito en la instrucción de la consulta.

## <a name="when-to-specify-dataset-structure"></a>Cuándo se debe especificar el conjunto de datos "structure"

En los escenarios siguientes, se requiere el elemento "structure" en el conjunto de datos:

* Aplicación de la [conversión de tipos de datos explícitos](#explicit-data-type-conversion) para los orígenes de archivo durante la copia (conjunto de datos de entrada)
* Aplicación de la [asignación de columnas explícitas](#explicit-column-mapping) durante la copia (en el conjunto de datos tanto de entrada como de salida)
* Copia desde el origen Dynamics 365/CRM (conjunto de datos de entrada)
* Copia a Cosmos DB como objeto anidado cuando el origen no es un archivo JSON (conjunto de datos de salida)

En los escenarios siguientes, se sugiere el elemento "structure" en el conjunto de datos:

* Copia desde el archivo de texto sin encabezado (conjunto de datos de entrada). Puede especificar los nombres de columna para que el archivo de texto se alinee con las columnas receptoras correspondientes, a fin de no tener que proporcionar la asignación de columnas explícitas.
* Copia desde almacenes de datos con esquema flexible, como Azure Table/Cosmos DB (conjunto de datos de entrada), para garantizar que los datos esperados (columnas) se copien en lugar de permitir que la actividad de copia interfiera en el esquema en función de las filas superiores durante cada ejecución de la actividad.


## <a name="next-steps"></a>pasos siguientes
Consulte los otros artículos de la actividad de copia:

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Tolerancia a errores de la actividad de copia](copy-activity-fault-tolerance.md)
- [Rendimiento de la actividad de copia](copy-activity-performance.md)
