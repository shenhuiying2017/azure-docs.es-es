---
title: Salida de JSON para Stream Analytics | Microsoft Docs
description: "Obtenga información sobre cómo Stream Analytics puede tener como destino Azure Cosmos DB para la salida de JSON, para el archivado de datos y las consultas de latencia baja en datos de JSON no estructurados."
keywords: Salida de JSON
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Tener como destino Azure Cosmos DB para la salida de JSON de Stream Analytics
Stream Analytics puede tener como destino [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para la salida de JSON, habilitando el archivado de datos y las consultas de latencia baja en datos de JSON no estructurados. En este documento tratan algunas prácticas recomendadas para implementar esta configuración.

Aquellos que no estén familiarizados con Cosmos DB pueden comenzar por la [ruta de aprendizaje de Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/). 

Nota: las colecciones de Cosmos DB basadas en Mongo DB API no se admiten actualmente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Aspectos básicos de Cosmos DB como destino de salida
La salida de Azure Cosmos DB de Stream Analytics de permite escribir los resultados del procesamiento de secuencias como salida de JSON en sus colecciones de Cosmos DB. Análisis de transmisiones no crea colecciones en la base de datos, ya no requiere que las cree por adelantado. Esto es para que los costes de facturación de las colecciones de Cosmos DB sean transparentes para usted y para que pueda optimizar el rendimiento, la coherencia y la capacidad de las colecciones directamente mediante las [API de Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Se recomienda utilizar una base de datos de Cosmos DB por trabajo de streaming para separar lógicamente las colecciones de un trabajo de streaming.

A continuación se detallan algunas de las opciones de la colección de Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar la coherencia, la disponibilidad y la latencia
Para satisfacer las necesidades de su aplicación, Cosmos DB permite optimizar la base de datos y las colecciones y buscar el equilibrio entre coherencia, disponibilidad y latencia. En función de los niveles de coherencia de lectura que requiera su situación en comparación con la latencia de lectura y escritura, puede elegir un nivel de coherencia u otro en la cuenta de base de datos. También de forma predeterminada, Cosmos DB permite la indexación sincrónica en cada operación CRUD de la colección. Esta es otra opción útil para controlar el rendimiento de escritura o lectura en Cosmos DB. Para obtener más información sobre este tema, revise el artículo [Cambio de los niveles de coherencia de la base de datos y las consultas](../documentdb/documentdb-consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upserts de Análisis de transmisiones
La integración de Stream Analytics con Cosmos DB permite insertar o actualizar registros en su colección de Cosmos DB en función de una columna de identificador de documento determinada. Esto se conoce también como *Upsert*.

El Análisis de transmisiones utiliza un enfoque Upsert optimista, donde las actualizaciones solo se realizan cuando se produce un error en la inserción debido a un conflicto de identificador de documento. Esta actualización se realiza por el Análisis de transmisiones como una operación PATCH, por lo que permite actualizaciones parciales en el documento; es decir, la adición de nuevas propiedades o la sustitución de una propiedad existente se realiza de forma incremental. Tenga en cuenta que los cambios en los valores de las propiedades de la matriz en el resultado del documento JSON de toda la matriz se sobrescriben; es decir, la matriz no se combina.

## <a name="data-partitioning-in-cosmos-db"></a>Creación de particiones de datos en Cosmos DB
Ahora se admiten las [colecciones de documentos con particiones](../cosmos-db/partition-data.md) de Cosmos DB y es el enfoque recomendado para crear particiones de los datos. 

Para las colecciones únicas de Cosmos DB, Stream Analytics permite particionar los datos según los patrones de consulta y los requisitos de rendimiento de la aplicación. Cada colección puede contener hasta 10 GB de datos (máximo) y actualmente no hay ninguna manera de escalar verticalmente (o desbordar) una colección. Para escalar horizontalmente, Análisis de transmisiones permite escribir a varias colecciones con un prefijo determinado (consulte los detalles de uso a continuación). Análisis de transmisiones utiliza la estrategia coherente de [resolución de la partición de hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) basada en la columna PartitionKey ofrecida por el usuario para crear particiones en sus registros de salida. El número de colecciones con el prefijo especificado en el inicio del trabajo de streaming se utiliza como el recuento de las particiones de salida en las que el trabajo escribe en paralelo (colecciones de Cosmos DB = particiones de salida). En el caso de una sola colección con una indexación diferida que realiza solo operaciones de inserción, se puede esperar en torno a 0,4 MB/s de rendimiento de escritura. El uso de varias colecciones puede permitirle lograr mayor rendimiento y capacidad.

Si desea aumentar el número de particiones en el futuro, puede que necesite detener su trabajo, volver a crear particiones de los datos de las colecciones existentes para crear nuevas colecciones y, a continuación, reiniciar el trabajo de Análisis de transmisiones. Se incluirá más información sobre el uso de PartitionResolver y la nueva creación de particiones junto con código de ejemplo en una publicación posterior. En el artículo [Partición y escalado en Cosmos DB](../documentdb/documentdb-partition-data.md), también se ofrecen detalles sobre esto.

## <a name="cosmos-db-settings-for-json-output"></a>Configuración de Cosmos DB para salida de JSON
La creación de Cosmos DB como una salida en Stream Analytics genera una solicitud de información, tal como se muestra a continuación. En esta sección se proporciona una explicación de la definición de propiedades.

Colección particionada | Varias colecciones de partición única
---|---
![pantalla de salida de análisis de transmisiones de documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![pantalla de salida de análisis de transmisiones de documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> El escenario **Varias colecciones de partición única** requiere una clave de partición y es una configuración compatible. 

* **Alias de salida** : un alias para hacer referencia a esta salida en la consulta ASA.  
* **Nombre de cuenta**: el nombre o el identificador URI del punto de conexión de la cuenta de Cosmos DB.  
* **Clave de cuenta**: la clave de acceso compartido para la cuenta de Cosmos DB.  
* **Base de datos**: nombre de la base de datos de Cosmos DB.  
* **Patrón de nombre de colección**: el patrón de nombre de colección para las colecciones que se usarán. El formato de nombre de la colección se pueden construir con el token opcional {partition}, donde las particiones comienzan desde 0. Las siguientes entradas de ejemplo son válidas:  
  1\) MyCollection: debe existir una colección denominada "MyCollection".  
  2\) MyCollection{partición}: deben existir esas colecciones: "MyCollection0", "MyCollection1", "MyCollection2", etc.  
* **Clave de partición**: opcional. Solo es necesario si usa un token {partition} en el patrón de nombre de la colección. Nombre del campo en los eventos de salida que se utiliza para especificar la clave de partición de salida entre colecciones. Para una salida de colección sencilla, se puede utilizar cualquier columna de salida arbitraria (por ejemplo, PartitionId).  
* **Identificador de documento** : opcional. Nombre del campo de los eventos de salida utilizado para especificar la clave principal en la que se basan las operaciones de inserción o actualización.  
