---
title: "Supervisión y depuración con métricas de Azure Cosmos DB | Microsoft Docs"
description: "Use las métricas de Azure Cosmos DB para depurar problemas comunes y supervisar la base de datos."
keywords: "Métricas"
services: cosmos-db
author: gnot
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 3b3de91c3850071d7c3fbff1faccde6c17a606e3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Supervisión y depuración con métricas de Azure Cosmos DB

Azure Cosmos DB proporciona métricas de rendimiento, almacenamiento, coherencia, disponibilidad y la latencia. [Azure Portal](https://portal.azure.com) proporciona una vista agregada de estas métricas; para métricas más granulares, están disponibles tanto el SDK de cliente como los [registros de diagnóstico](./logging.md).

Para información general sobre las nuevas métricas y aprender a buscar particiones activas en la base de datos, vea el siguiente vídeo de Azure Friday:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Este artículo le guía a través de casos de uso comunes y cómo se pueden utilizar las métricas de Azure Cosmos DB para analizar y depurar estos problemas. Las métricas se recopilan cada cinco minutos y se conservan durante siete días.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Descripción de cuántas solicitudes se realizan correctamente o causan errores

Para empezar, vaya a [Azure Portal](https://portal.azure.com) y navegue hasta la hoja **Métricas**. En la hoja, busque el gráfico **Cantidad de solicitudes que superaron la capacidad durante 1 minuto**. Este gráfico muestra las solicitudes totales minuto a minuto segmentadas por el código de estado. Para más información sobre los códigos de estado HTTP, consulte [HTTP Status Codes for Azure Cosmos DB](https://docs.microsoft.com/rest/api/documentdb/http-status-codes-for-documentdb) (Códigos de estado HTTP para Azure Cosmos DB).

El código de estado de error más común es 429 (limitación), lo que significa que las solicitudes a Azure Cosmos DB superan el rendimiento aprovisionado. La solución más común para este problema consiste en [escalar verticalmente las RU](./set-throughput.md) para la colección dada.

![Número de solicitudes por minuto](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Determinación de la distribución de rendimiento en las particiones

Tener una buena cardinalidad de las claves de partición es esencial para cualquier aplicación escalable. Para determinar la distribución de rendimiento de cualquier colección particionada dividida en particiones, vaya a la **hoja Métricas** en [Azure Portal](https://portal.azure.com). En la pestaña **Rendimiento**, se muestra el desglose de almacenamiento en el gráfico **Máximo de RU/segundo consumidas por cada partición física**. En el siguiente gráfico se ilustra un ejemplo de una distribución deficiente de los datos como lo evidencia la partición sesgada en el extremo izquierdo. 

![Una sola partición ve un uso intensivo a las 3:05 p.m.](media/use-metrics/metrics-17.png)

Una distribución de rendimiento desigual puede provocar particiones *activas*, lo que pueden dar lugar a solicitudes limitadas y la necesidad de volver a crear particiones. Para más información sobre la creación de particiones en Azure Cosmos DB, consulte [Partición y escalado en Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Determinación de la distribución de almacenamiento en las particiones

Tener una buena cardinalidad de la partición es esencial para cualquier aplicación escalable. Para determinar la distribución de rendimiento de cualquier colección particionada dividida en particiones, vaya a la hoja Métricas en [Azure Portal](https://portal.azure.com). En la pestaña Rendimiento, se muestra el desglose de almacenamiento en el gráfico Máximo de RU/segundo consumidas por cada partición física. En el siguiente gráfico se ilustra una distribución deficiente de los datos como lo evidencia la partición sesgada en el extremo izquierdo. 

![Ejemplo de distribución de datos deficiente](media/use-metrics/metrics-07.png)

Puede determinar la causa raíz por la que la partición está sesgando la distribución haciendo clic en la partición en el gráfico. 

![La clave de partición sesga la distribución](media/use-metrics/metrics-05.png)

Después de identificar qué clave de partición está causando el sesgo en la distribución, puede que tenga que volver a crear particiones en la colección con una clave de partición más distribuida. Para más información sobre la creación de particiones en Azure Cosmos DB, consulte [Partición y escalado en Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Comparación del tamaño de los datos con el tamaño de índice

En Azure Cosmos DB, el almacenamiento total consumido es la combinación del tamaño de los datos y del tamaño del índice. Normalmente, el tamaño del índice es una fracción del tamaño de los datos. En la hoja Métricas de [Azure Portal](https://portal.azure.com), la pestaña Almacenamiento muestra el desglose de consumo de almacenamiento en función de los datos y el índice. Imagen (quizás) O bien, desde el SDK, puede encontrar el uso de almacenamiento actual a través de una lectura de la recopilación.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Si desea conservar el espacio de índice, puede ajustar la [directiva de indexación](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Depuración de por qué mis consultas se ejecutan lentamente

En los SDK de la API de SQL, Azure Cosmos DB proporciona estadísticas de ejecución de consultas. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* proporciona información detallada sobre cuánto tardó cada componente de la consulta en la ejecución. La causa principal de las consultas ejecución prolongada son los exámenes (la consulta no pudo aprovechar los índices), que se puede resolver con una condición de filtro mejor.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo supervisar y depurar problemas con las métricas proporcionadas en Azure Portal, puede que desee obtener más información sobre cómo mejorar el rendimiento de la base de datos. Para ello, lea los siguientes artículos:

* [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md)
* [Sugerencias de rendimiento para Azure Cosmos DB](performance-tips.md)
