---
title: "Introducción a la API Table de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de datos de clave-valor con latencia baja mediante las populares API MongoDB de OSS."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/09/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ef57753aeeace0086c815d83600f92422996032a
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introducción a la API Table de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB ofrece una [distribución global inmediata](distribute-data-globally.md), [escalado elástico de rendimiento y almacenamiento](partition-data.md) en todo el mundo, latencias inferiores a 10 milisegundos en el percentil 99, [cinco niveles de coherencia bien definidos](consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [acuerdos de nivel de servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de grafo y de columnas. 

![API Table Storage de Azure y Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB proporciona la API de tabla (versión preliminar) para las aplicaciones que necesitan un almacén de pares clave-valor con un esquema flexible, un rendimiento predecible, una distribución global y un alto rendimiento. La API de tabla proporciona la misma funcionalidad que Azure Table Storage, pero saca provecho de las ventajas del motor de Azure Cosmos DB. 

Puede seguir usando Azure Table Storage para las tablas con requisitos de alto almacenamiento y menor rendimiento. Azure Cosmos DB incorporará compatibilidad con tablas optimizadas para almacenamiento en una futura actualización. Además, las cuentas existentes y nuevas de Azure Table Storage se actualizarán a Azure Cosmos DB.

## <a name="premium-and-standard-table-apis"></a>API Table premium y estándar
Si actualmente usa Azure Table Storage, obtendrá las siguientes ventajas si actualiza a la versión preliminar de las "tablas premium" de Azure Cosmos DB:

|  | Azure Table Storage | Table Storage de Azure Cosmos DB (versión preliminar) |
| --- | --- | --- |
| Latency | Rápido, pero no hay límites máximos en la latencia. | Latencia inferior a 10 milisegundos en lecturas y escrituras, respaldada por lecturas de latencia <10 ms y escrituras de latencia <15 ms en el percentil 99, a cualquier escala y en cualquier lugar en el mundo. |
| Rendimiento | Altamente escalable, pero sin un modelo de rendimiento dedicado. Las tablas tienen un límite de escalabilidad de 20 000 operaciones por segundo. | Altamente escalable con [rendimiento reservado dedicado por tabla](request-units.md), respaldado por el SLA. Las cuentas no tienen límite máximo en el rendimiento y admiten más de 10 millones de operaciones por segundo por tabla. |
| Distribución global | Una sola región, con una región de lectura secundaria legible opcional para alta disponibilidad. No se puede iniciar la conmutación por error. | [Distribución global inmediata](distribute-data-globally.md) entre una y más de 30 regiones, compatibilidad con [conmutaciones por error automáticas y manuales](regional-failover.md) en cualquier momento y en cualquier lugar en el mundo. |
| Indización | Índice principal solo en PartitionKey y RowKey. No hay índices secundarios. | Indexación automática y completa en todas las propiedades, sin administración de índices. |
| Consultar | La ejecución de consultas usa el índice de la clave principal y, en caso contrario, examina. | Las consultas pueden aprovechar la indexación automática en las propiedades para reducir el tiempo de consulta. El motor de base de datos de Azure Cosmos DB puede admitir agregados, funciones geoespaciales y ordenación. |
| Coherencia | Fuerte en la región primaria, Final en la región secundaria. | [Cinco niveles de coherencia bien definidos](consistency-levels.md) para compensar la disponibilidad, la latencia, el rendimiento y la coherencia en función de lo que necesita la aplicación |
| Precios | Optimizado para el almacenamiento.  | Optimizado para el rendimiento. |
| SLA | Disponibilidad del 99,9 %. | Disponibilidad del 99,99 % en una sola región y capacidad de agregar más regiones para una mayor disponibilidad. [SLA completos líderes en el sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/) en disponibilidad general. |

## <a name="how-to-get-started"></a>Primeros pasos

Cree una cuenta de Azure Cosmos DB en [Azure Portal](https://portal.azure.com) y empiece a trabajar con nuestro [inicio rápido para la API Table con .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Pasos siguientes

Estas son algunas sugerencias para comenzar:
* Use el SDK de Table de .NET existente para empezar trabajar con la [API Table de Azure Cosmos DB](create-table-dotnet.md).
* Obtenga información sobre la [distribución global con Azure Cosmos DB](distribute-data-globally.md).
* Obtenga información sobre el [rendimiento aprovisionado en Azure Cosmos DB](request-units.md).

