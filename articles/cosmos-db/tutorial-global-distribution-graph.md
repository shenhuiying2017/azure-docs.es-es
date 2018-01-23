---
title: "Tutorial de distribución global de Azure Cosmos DB para API Graph | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la distribución global de Azure Cosmos DB con la API Graph."
services: cosmos-db
keywords: "distribución global, graph, gremlin"
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1806bde383f04747f1f0fef46e5cf4d38de1e939
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Configuración de la distribución global de Azure Cosmos DB con la API Graph

En este artículo se muestra cómo usar Azure Portal para configurar la distribución global de Azure Cosmos DB y luego conectarse con Graph API.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global con [Graph API](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Conexión a una región de preferencia con la API Graph mediante el SDK de .NET

Graph API se expone como una biblioteca de extensiones sobre SQL API.

Para aprovechar las ventajas de la [distribución global](distribute-data-globally.md), las aplicaciones cliente pueden especificar la lista del orden de preferencia de regiones que se usará para llevar a cabo operaciones de documentos. Esto puede hacerse estableciendo la directiva de conexión. Según la configuración de la cuenta de Azure Cosmos DB, la disponibilidad regional actual y la lista de preferencias especificada, el SDK elegirá el punto de conexión óptimo para realizar las operaciones de lectura y escritura.

Esta lista de preferencias se especifica cuando se inicializa una conexión mediante los SDK. Los SDK aceptan un parámetro opcional "PreferredLocations", que es una lista ordenada de regiones de Azure.

* **Escrituras**: el SDK envía automáticamente todas las escrituras a la región de escritura actual.
* **Lecturas**: todas las lecturas se envían a la primera región disponible de la lista PreferredLocations. Si se produce un error en la solicitud, el cliente pasa a la siguiente región de la lista y así sucesivamente. Los SDK solo intentan leer en las regiones especificadas en PreferredLocations. Así que, por ejemplo, si la cuenta de Cosmos DB está disponible en tres regiones, pero el cliente solo especifica dos de las regiones de no escritura para PreferredLocations, no se atiende ninguna lectura desde la región de escritura, ni siquiera en caso de conmutación por error.

La aplicación puede comprobar los puntos de conexión de escritura y lectura actuales elegidos por el SDK. Para ello, comprueba dos propiedades, WriteEndpoint y ReadEndpoint, disponibles en el SDK 1.8 y versiones posteriores. Si la propiedad PreferredLocations no está establecida, todas las solicitudes se atienden desde la región de escritura actual.

### <a name="using-the-sdk"></a>Uso del SDK

Por ejemplo, en el SDK de .NET, el parámetro `ConnectionPolicy` del constructor `DocumentClient` tiene una propiedad llamada `PreferredLocations`. Esta propiedad se puede establecer en una lista de nombres de regiones. Los nombres para mostrar de las [regiones de Azure][regions] se pueden especificar como parte de `PreferredLocations`.

> [!NOTE]
> Las direcciones URL de los puntos de conexión no deben considerarse constantes de larga duración. El servicio puede actualizarlas en cualquier momento. El SDK administra este cambio automáticamente.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

De este modo finaliza este tutorial. Para información sobre cómo administrar la coherencia de la cuenta replicada globalmente, lea [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md). Para más información sobre cómo funciona la replicación global de bases de datos en Azure Cosmos DB, consulte [Distribución global de datos con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global con las API de SQL

Ahora puede continuar en el tutorial siguiente para aprender a desarrollar localmente con el emulador local de Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desarrollo local con el emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

