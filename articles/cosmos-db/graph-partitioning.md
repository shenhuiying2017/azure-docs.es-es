---
title: Creación de particiones en Graph API | Microsoft Docs
description: Aprenda a usar Graph con particiones en Azure Cosmos DB.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso de Graph con particiones en Azure Cosmos DB

Una de las características clave de Graph API en Azure Cosmos DB es la capacidad para manejar grafos a gran escala con escalabilidad horizontal. Este proceso se consigue mediante las [funcionalidades de creación de particiones de Azure Cosmos DB](partition-data.md#how-does-partitioning-work), que usan colecciones, también conocidas como contenedores, que se pueden escalar de forma independiente en términos de almacenamiento y rendimiento. Azure Cosmos DB admite los siguientes tipos de contenedores a través de todas las API:

- **Colecciones fijas**: pueden almacenar una base de datos de grafos de hasta 10 GB con un máximo de 10 000 unidades de solicitud por segundo asignadas. Para crear una colección de tamaño fijo que no es necesario especificar una propiedad de clave de partición en los datos.

- **Colecciones ilimitadas**: pueden escalarse automáticamente para almacenar un grafo de más de 10 GB mediante particionamiento horizontal. Cada partición almacenará 10 GB y los datos se equilibrarán automáticamente según la **clave de partición especificada**, que será un parámetro obligatorio con las colecciones ilimitadas. Este tipo de contenedor puede almacenar un tamaño de datos prácticamente ilimitado y permite hasta 100 000 unidades de solicitud por segundo, o más ([póngase en contacto con soporte técnico](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request)).

En este documento se describirán los detalles sobre cómo se crean particiones en las bases de datos de grafos junto con sus implicaciones para ambos vértices (o nodos) y bordes.

## <a name="requirements-for-partitioned-graph"></a>Requisitos para el grafo con particiones

Estos son los detalles que es necesario comprender para crear un contenedor de grafos con particiones:
- La **configuración de la partición será obligatoria** si se prevé que la colección tenga más de 10 GB o si se deben asignar más de 10 000 unidades de solicitud por segundo (RU/s).
- Tanto los **vértices como los bordes se almacenan como documentos JSON** en el back-end del contenedor de Graph API de Azure Cosmos DB.
- Los **vértices requieren una clave de partición**. Esta clave determinará en qué partición se almacenará el vértice a través de un algoritmo hash. El nombre de esta clave de partición es una cadena de una única palabra sin espacios ni caracteres especiales, que se define al crear una colección con formato `/partitioning-key-name` en el portal.
- Los **bordes se almacenarán con sus vértices de origen**. En otras palabras, para cada vértice, su clave de partición definirá dónde se almacenará junto con sus bordes salientes. Esto se hace para evitar consultas entre particiones cuando se usa la cardinalidad `out()` en las consultas de grafo.
- Las **consultas de grafo tienen que especificar una clave de partición**. Para aprovechar al máximo la partición horizontal en Azure Cosmos DB, la clave de partición debe especificarse al seleccionar un solo vértice, siempre que sea posible. Estas son las consultas para seleccionar uno o varios vértices en un grafo con particiones:

    - Selección de un vértice por el identificador, **use el paso `.has()` para especificar la propiedad de clave de partición**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selección de un vértice, **especifique una tupla con valor de clave de partición e identificador**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Especificación de una **matriz de tuplas de identificadores y valores de clave de partición**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selección de un conjunto de vértices y **especificación de una lista de valores de clave de partición**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Procedimientos recomendados con un grafo con particiones

Estas son directrices que deben seguirse para garantizar la eficacia de rendimiento y escalabilidad con los grafos con particiones en colecciones ilimitadas:
- **Especifique siempre el valor de la clave de partición al consultar un vértice**. Obtener un vértice de una partición conocida es la manera más eficaz en términos de rendimiento.
- **Use la dirección saliente al consultar los bordes siempre que sea posible**. Tal y como se mencionó anteriormente, los bordes se almacenan con sus vértices de origen en la dirección saliente. Esto significa que se minimizan las posibilidades de recurrir a las consultas entre particiones cuando los datos y las consultas están diseñados con este patrón en mente.
- **Elija una clave de partición que distribuya los datos uniformemente por las particiones**. Esta decisión depende mucho el modelo de datos de la solución. Más información sobre la creación de una clave de partición apropiada en [Partición y escalado en Azure Cosmos DB](partition-data.md).
- **Optimice las consultas para obtener los datos dentro de los límites de una partición siempre que sea posible**. Una estrategia de partición óptima se alinea con los patrones de consulta. Las consultas que obtienen datos de una sola partición proporcionan el mejor rendimiento.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se ha proporcionado información general sobre los conceptos y procedimientos recomendados para crear particiones con cualquier Graph API de Azure Cosmos DB. 

* Información acerca de la [Partición y escalado en Azure Cosmos DB](partition-data.md).
* Información acerca de la [compatibilidad de Gremlin en Graph API](gremlin-support.md).
* Información acerca de la [introducción a Graph API](graph-introduction.md).
