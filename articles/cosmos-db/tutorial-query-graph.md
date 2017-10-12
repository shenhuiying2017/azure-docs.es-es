---
title: "¿Cómo consultar datos del gráfico en Azure Cosmos DB? | Microsoft Docs"
description: "Aprenda a consultar datos del gráfico en Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: 81713c72da037f127e81239d214d7a877247dca1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api-preview"></a>Azure Cosmos DB: ¿cómo realizar consultas con la API Graph (versión preliminar)?

La [API Graph](graph-introduction.md) de Azure Cosmos DB (versión preliminar) admite consultas de [Gremlin](https://docs.mongodb.com/manual/tutorial/query-documents/). En este artículo se proporcionan consultas y documentos de ejemplo a modo de introducción. En el artículo [Compatibilidad con Gremlin](gremlin-support.md) se incluye una referencia de Gremlin detallada.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Consulta de datos con Gremlin

## <a name="prerequisites"></a>Requisitos previos

Para que estas consultas funcionen, debe tener una cuenta de Azure Cosmos DB, así como datos del gráfico en el contenedor. ¿No tiene nada de lo anterior? Complete el [inicio rápido en 5 minutos](create-graph-dotnet.md) o el [tutorial de desarrolladores](tutorial-query-graph.md) para crear una cuenta y rellenar la base de datos. Puede ejecutar las siguientes consultas mediante la [biblioteca de gráficos de .NET de Azure Cosmos DB](graph-sdk-dotnet.md), la [consola de Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) o su controlador de Gremlin favorito.

## <a name="count-vertices-in-the-graph"></a>Recuento de vértices del gráfico

En el siguiente fragmento se muestra cómo contar el número de vértices del gráfico:

```
g.V().count()
```

## <a name="filters"></a>Filtros

Puede aplicar filtros mediante los pasos `has` y `hasLabel` de Gremlin y combinarlos con `and`, `or` y `not` para crear filtros más complejos. Azure Cosmos DB proporciona indexación independiente del esquema de todas las propiedades de los vértices y grados para consultas rápidas:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Proyección

Puede proyectar determinadas propiedades en los resultados de la consulta mediante el paso `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Búsqueda de bordes y vértices relacionados

Hasta ahora, solo hemos visto operadores de consulta que funcionan en cualquier base de datos. Los grafos son rápidos y eficientes para operaciones de cruce seguro si tiene que ir a bordes y vértices relacionados. Encontremos a todos los amigos de Thomas. Lo hacemos usando el paso `outE` de Gremlin para encontrar todos los bordes exteriores de Thomas y atravesando a continuación por los vértices interiores de esos bordes mediante el paso `inV` de Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

La siguiente consulta realiza dos saltos para encontrar a todos los "amigos de los amigos" de Thomas, llamando a `outE` y `inV` dos veces. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Puede crear consultas más complejas e implementar una lógica de cruce seguro del grafo eficaz con Gremlin, incluidas la combinación de expresiones de filtro, la realización de bucles mediante el paso `loop` y la implementación de la navegación condicional mediante el paso `choose`. Obtenga más información sobre lo que puede hacer con [Compatibilidad con Gremlin](gremlin-support.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha obtenido información sobre cómo realizar consultas con Graph 

Ahora puede continuar con el tutorial siguiente para obtener información sobre cómo distribuir sus datos globalmente.

> [!div class="nextstepaction"]
> [Distribución de datos global](tutorial-global-distribution-documentdb.md)