---
title: "Introducción a la API Table de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de datos de pares clave-valor con latencia baja mediante las populares API MongoDB de OSS."
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
ms.openlocfilehash: c184b02f46ac2ad66c6ce607967a687c82d31477
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introducción a la API Table de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) proporciona la API Table (versión preliminar) para aplicaciones escritas para Azure Table Storage y que necesitan funcionalidades premium como:

* [Distribución global inmediata](distribute-data-globally.md).
* [Rendimiento dedicado](partition-data.md) en todo el mundo.
* Latencias en milisegundos de un solo dígito en el percentil 99.
* Alta disponibilidad garantizada.
* [Indexación secundaria automática](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Estas aplicaciones pueden migrar a Azure Cosmos DB mediante la API Table sin realizar ningún cambio en el código y pueden sacar provecho de las funcionalidades premium. La API Table está disponible para .NET y Python.

Se recomienda ver el vídeo siguiente, donde Aravind Ramachandran explica cómo empezar a usar la API Table de Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>API Table premium y estándar
Si actualmente usa Table Storage, obtendrá las siguientes ventajas si actualiza a la versión preliminar de las "tablas premium" de Azure Cosmos DB:

| | Almacenamiento de tablas de Azure | Table Storage de Azure Cosmos DB (versión preliminar) |
| --- | --- | --- |
| Latency | Rápido, pero no hay límites máximos en la latencia. | Latencia en milisegundos de un solo dígito para lecturas y escrituras, respaldada con lecturas con una latencia inferior a 10 ms y escrituras con una latencia inferior a 15 ms en el percentil 99, a cualquier escala, en cualquier lugar del mundo. |
| Rendimiento | Modelo de rendimiento variable. Las tablas tienen un límite de escalabilidad de 20.000 operaciones por segundo. | Altamente escalable con [rendimiento reservado dedicado por tabla](request-units.md) respaldado por los SLA. Las cuentas no tienen límite máximo en el rendimiento y admiten más de 10 millones de operaciones por segundo por tabla. |
| Distribución global | Una sola región, con una región de lectura secundaria legible opcional para alta disponibilidad. No se puede iniciar la conmutación por error. | [Distribución global inmediata](distribute-data-globally.md) desde 1 a más de 30 regiones. Admite [conmutaciones por error automáticas y manuales](regional-failover.md) en cualquier momento y en cualquier lugar del mundo. |
| Indización | Índice principal solo en PartitionKey y RowKey. No hay índices secundarios. | Indexación automática y completa en todas las propiedades, sin administración de índices. |
| Consultar | La ejecución de consultas usa el índice de la clave principal y, en caso contrario, examina. | Las consultas pueden aprovechar la indexación automática en las propiedades para reducir el tiempo de consulta. El motor de base de datos de Azure Cosmos DB puede admitir agregados, funciones geoespaciales y ordenación. |
| Coherencia | Seguro dentro de la región principal. Ocasional en la región secundaria. | [Cinco niveles de coherencia bien definidos](consistency-levels.md) para compensar la disponibilidad, la latencia, el rendimiento y la coherencia en función de las necesidades de la aplicación. |
| Precios | Optimizado para el almacenamiento. | Optimizado para el rendimiento. |
| SLA | disponibilidad del 99,99%. | Disponibilidad del 99,99 % en una sola región y capacidad de agregar más regiones para una mayor disponibilidad. [SLA completos líderes en el sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/) en disponibilidad general. |

## <a name="get-started"></a>Primeros pasos

Crear una cuenta de Azure Cosmos DB en [Azure Portal](https://portal.azure.com). A continuación, empezar a trabajar con nuestra [Guía de inicio rápido para la API Table mediante .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Pasos siguientes

Estas son algunas sugerencias para comenzar:
* [Creación de una aplicación .NET con la API Table](create-table-dotnet.md)
* [Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)
* [Azure Cosmos DB: instrucciones de realización de consultas de tablas de datos con Table API (versión preliminar)](tutorial-query-table.md)
* [Configuración de la distribución global de Azure Cosmos DB con la API Table](tutorial-global-distribution-table.md)
* [API Table de .NET de Azure Cosmos DB](table-sdk-dotnet.md)
* [SDK de Table de Azure Cosmos DB para Python](table-sdk-python.md)

