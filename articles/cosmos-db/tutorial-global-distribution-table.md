---
title: "Tutorial de distribución global de Azure Cosmos DB para Table API | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la distribución global de Azure Cosmos DB con Table API."
services: cosmos-db
keywords: "distribución global, Table"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Configuración de la distribución global de Azure Cosmos DB con Table API

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global con [Table API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Conexión a una región de preferencia con Table API

Para aprovechar las ventajas de la [distribución global](distribute-data-globally.md), las aplicaciones cliente pueden especificar la lista del orden de preferencia de regiones que se usará para llevar a cabo operaciones de documentos. Para poder hacer esto, debe establecer el valor de configuración `TablePreferredLocations` en el archivo app.config del SDK de Table API de Azure Cosmos DB. El SDK de Table API de Azure Cosmos DB elegirá el mejor punto de conexión para comunicarse, en función de la configuración de la cuenta, la disponibilidad regional actual y la lista de preferencias proporcionada.

`TablePreferredLocations` debe contener una lista separada por comas de las ubicaciones preferidas (hospedaje múltiple) para las lecturas. Cada instancia de cliente puede especificar un subconjunto de estas regiones en el orden de preferencia para las lecturas de latencia baja. Estas regiones se deben denominar según sus [nombres para mostrar](https://msdn.microsoft.com/library/azure/gg441293.aspx), por ejemplo, `West US`.

Todas las lecturas se envían a la primera región disponible en la lista `TablePreferredLocations`. Si se produce un error en la solicitud, el cliente pasará a la siguiente región de la lista y así sucesivamente.

El SDK intenta leer desde las regiones especificadas en `TablePreferredLocations`. Así que, por ejemplo, si la cuenta de base de datos está disponible en tres regiones, pero el cliente solo especifica dos de las regiones de no escritura para `TablePreferredLocations`, no se atenderá ninguna lectura desde la región de escritura, incluso en caso de conmutación por error.

El SDK enviará automáticamente todas las escrituras a la región de escritura actual.

Si la propiedad `TablePreferredLocations` no está establecida, atenderá todas las solicitudes procedentes de la región de escritura actual.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

De este modo finaliza este tutorial. Para información sobre cómo administrar la coherencia de la cuenta replicada globalmente, lea [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md). Para más información sobre cómo funciona la replicación global de bases de datos en Azure Cosmos DB, consulte [Distribución global de datos con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configurar la distribución global mediante Table API de Azure Cosmos DB

