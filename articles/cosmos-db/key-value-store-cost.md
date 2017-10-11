---
title: "Azure Cosmos DB como almacén de pares valor-clave: Información general de costos | Microsoft Docs"
description: "Obtenga información sobre el bajo costo de usar Azure Cosmos DB como un almacén de pares valor-clave."
keywords: "almacén de pares valor-clave"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mimig
ms.openlocfilehash: 33eef1b51a5ee00b0fa67096030ed9ce92cf768e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como almacén de pares valor-clave: Información general de costos

Azure Cosmos DB es un servicio de base de datos multimodelo globalmente distribuido para crear aplicaciones de alta disponibilidad y a gran escala fácilmente. De forma predeterminada, Azure Cosmos DB indexa automática y eficazmente todos los datos que ingiere. Esto permite consultas [SQL](documentdb-sql-query.md) (y [JavaScript](programming.md)) rápidas y coherentes en todos los tipos de datos. 

En este artículo se describe el costo de Azure Cosmos DB para operaciones simples de escritura y lectura cuando se utiliza como un almacén de pares valor-clave. Las operaciones de escritura incluyen inserciones, reemplazos, eliminaciones y upserts de documentos. Además de garantizar una alta disponibilidad del 99,99 %, Azure Cosmos DB ofrece una latencia garantizada de < 10 ms para lecturas y una latencia de < 15 ms para escrituras (indexadas), en el percentil 99. 

## <a name="why-we-use-request-units-rus"></a>¿Por qué usamos unidades de solicitud (RU)?

El rendimiento de Azure Cosmos DB se basa en la cantidad de [unidades de solicitud](request-units.md) (RU) aprovisionadas para la partición. El aprovisionamiento tiene una granularidad de un segundo y se adquiere en RU/s y RU/min ([no se debe confundir con la facturación horaria](https://azure.microsoft.com/pricing/details/cosmos-db/)). Las RU deben considerarse una moneda que simplifica el aprovisionamiento de rendimiento necesario para la aplicación. Nuestros clientes no tienen que pensar en diferenciar entre unidades de capacidad de lectura y escritura. El modelo de moneda única de RU crea eficiencias para compartir la capacidad aprovisionada entre lecturas y escrituras. Este modelo de capacidad aprovisionada permite que el servicio proporcione un rendimiento predecible y coherente, baja latencia garantizada y alta disponibilidad. Por último, utilizamos RU para modelar el rendimiento, pero cada RU aprovisionada también tiene una cantidad definida de recursos (memoria y núcleos). RU/s no es solo E/S por segundo.

Como un sistema de base de datos distribuido globalmente, Cosmos DB es el único servicio de Azure que proporciona un Acuerdo de Nivel de Servicio sobre latencia, rendimiento y coherencia además de alta disponibilidad. El rendimiento que se aprovisiona se aplica a cada una de las regiones asociadas a su cuenta de base de datos de Cosmos DB. Para lecturas, Cosmos DB ofrece varios [niveles de coherencia](consistency-levels.md) bien definidos entre los que elegir. 

La tabla siguiente muestra el número de RU necesarias para realizar transacciones de lectura y escritura basadas en el tamaño de documento de 1 KB y 100 KB.

|Tamaño del elemento|1 lectura|1 escritura|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>Costo de lecturas y escrituras

Si aprovisiona 1000 RU/s, esto asciende a 3,6 millones de RU/hora y costará 0,08 USD para la hora (en Estados Unidos y Europa). Para un documento de 1 KB de tamaño, esto significa que puede consumir 3,6 millones de lecturas o 0,72 millones de escrituras (3,6 millones de RU/5) con el rendimiento aprovisionado. Normalizado a millones de lecturas y escrituras, el costo sería de 0,022 USD/millones de lecturas (0,08 USD/3,6) y 0,111/millones de escrituras (0,08 USD/0,72). El costo por millón pasa a ser mínimo tal como se muestra en la tabla siguiente.

|Tamaño del elemento|1 millón de lecturas|1 millón de escrituras|
|-------------|-------|--------|
|1 KB|0,022 USD|0,111 USD|
|100 KB|0,222 USD|1,111 USD|


La mayoría de los almacenes de blobs u objetos básicos cobran 0,40 USD por cada millón de transacciones de lectura y 5 USD por cada millón de transacciones de escritura. Si se usa de forma óptima, Cosmos DB puede ser hasta un 98 % más económico que estas otras soluciones (para transacciones de 1 KB).

## <a name="next-steps"></a>Pasos siguientes

Permanezca atento para buscar nuevos artículos sobre la optimización del aprovisionamiento de recursos de Azure Cosmos DB. Mientras tanto, no dude en usar nuestra [calculadora de RU](https://www.documentdb.com/capacityplanner).

