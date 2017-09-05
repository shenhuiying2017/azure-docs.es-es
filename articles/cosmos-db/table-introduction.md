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
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introducción a la API Table de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) proporciona la API de Table (versión preliminar) para las aplicaciones escritas para el servicio de almacenamiento de Azure Table y necesitan funcionalidades premium como la [distribución global inmediata](distribute-data-globally.md), [rendimiento dedicado ](partition-data.md) a nivel mundial, latencias de un solo dígito (en milisegundos) en el percentil 99, alta disponibilidad garantizada e [indexación automática secundaria](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Estas aplicaciones pueden migrar a Azure Cosmos DB mediante la API de Table sin realizar ningún cambio en el código y pueden sacar provecho de las funcionalidades premium.

Se recomienda empezar por ver el vídeo siguiente, donde Aravind Ramachandran explica cómo empezar a usar la API de Table en Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>API Table premium y estándar
Si actualmente usa Azure Table Storage, obtendrá las siguientes ventajas si actualiza a la versión preliminar de las "tablas premium" de Azure Cosmos DB:

|  | Azure Table Storage | Table Storage de Azure Cosmos DB (versión preliminar) |
| --- | --- | --- |
| Latency | Rápido, pero no hay límites máximos en la latencia. | Latencia en segundos con un solo dígito para lecturas y escrituras, respaldada con lecturas con una latencia inferior a 10 ms y escrituras con una latencia inferior a 15 ms en el percentil 99, a cualquier escala, en cualquier lugar del mundo |
| Rendimiento | modelo de rendimiento variable. Las tablas tienen un límite de escalabilidad de 20 000 operaciones por segundo. | Altamente escalable con [rendimiento reservado dedicado por tabla](request-units.md), respaldado por el SLA. Las cuentas no tienen límite máximo en el rendimiento y admiten más de 10 millones de operaciones por segundo por tabla. |
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
* [Azure Cosmos DB: Compilación de una aplicación de .NET mediante Table API](create-table-dotnet.md)
* [Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)
* [Azure Cosmos DB: instrucciones de realización de consultas de tablas de datos con Table API (versión preliminar)](tutorial-query-table.md)
* [Configuración de la distribución global de Azure Cosmos DB con Table API](tutorial-global-distribution-table.md)
* [Table API .NET de Azure Cosmos DB: descarga y notas de la versión](table-sdk-dotnet.md)

