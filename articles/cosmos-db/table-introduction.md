---
title: "Introducción a la API Table de Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de datos de pares clave-valor con latencia baja mediante las populares API MongoDB de OSS."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 7e116400f55375afe1e57abc6dadae0536cdfceb
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introducción a Table API de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) proporciona Table API para aplicaciones escritas para Azure Table Storage y que necesitan funcionalidades premium como:

* [Distribución global inmediata](distribute-data-globally.md).
* [Rendimiento dedicado](partition-data.md) en todo el mundo.
* Latencias en milisegundos de un solo dígito en el percentil 99.
* Alta disponibilidad garantizada.
* [Indexación secundaria automática](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Las aplicaciones escritas para Azure Table Storage pueden migrarse a Azure Cosmos DB mediante la API Table sin realizar ningún cambio en el código y pueden sacar provecho de las funcionalidades premium. Table API tiene SDK de cliente disponibles para .NET, Java, Python y Node.js.

Se recomienda ver el vídeo siguiente, donde Aravind Ramachandran explica cómo empezar a usar la API Table de Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="table-offerings"></a>Ofertas de Table
Si actualmente usa Azure Table Storage, obtendrá las siguientes ventajas al actualizar a Table API de Azure Cosmos DB:

| | Almacenamiento de tablas de Azure | Table API de Azure Cosmos DB |
| --- | --- | --- |
| Latency | Rápido, pero no hay límites máximos en la latencia. | Latencia en milisegundos de un solo dígito para lecturas y escrituras, respaldada con lecturas con una latencia inferior a 10 ms y escrituras con una latencia inferior a 15 ms en el percentil 99, a cualquier escala, en cualquier lugar del mundo. |
| Throughput | Modelo de rendimiento variable. Las tablas tienen un límite de escalabilidad de 20.000 operaciones por segundo. | Altamente escalable con [rendimiento reservado dedicado por tabla](request-units.md) respaldado por los SLA. Las cuentas no tienen límite máximo en el rendimiento y admiten más de 10 millones de operaciones por segundo por tabla. |
| Distribución global | Una sola región, con una región de lectura secundaria legible opcional para alta disponibilidad. No se puede iniciar la conmutación por error. | [Distribución global inmediata](distribute-data-globally.md) desde 1 a más de 30 regiones. Admite [conmutaciones por error automáticas y manuales](regional-failover.md) en cualquier momento y en cualquier lugar del mundo. |
| Indización | Índice principal solo en PartitionKey y RowKey. No hay índices secundarios. | Indexación automática y completa en todas las propiedades, sin administración de índices. |
| Consultar | La ejecución de consultas usa el índice de la clave principal y, en caso contrario, examina. | Las consultas pueden aprovechar la indexación automática en las propiedades para reducir el tiempo de consulta. |
| Coherencia | Seguro dentro de la región principal. Ocasional en la región secundaria. | [Cinco niveles de coherencia bien definidos](consistency-levels.md) para compensar la disponibilidad, la latencia, el rendimiento y la coherencia en función de las necesidades de la aplicación. |
| Precios | Optimizado para el almacenamiento. | Optimizado para el rendimiento. |
| SLA | disponibilidad del 99,99%. | Acuerdo de Nivel de Servicio con disponibilidad del 99,99 % para todas las cuentas de región individual y todas las cuentas de varias regiones con coherencia menos estricta y disponibilidad del 99,999 % para todos los [Acuerdos de Nivel de Servicio completos líderes en el sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de todas las cuentas de base de datos de varias regiones de lectura sobre la disponibilidad general. |

## <a name="get-started"></a>Introducción

Crear una cuenta de Azure Cosmos DB en [Azure Portal](https://portal.azure.com). A continuación, empezar a trabajar con nuestra [Guía de inicio rápido para la API Table mediante .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Si ha creado una cuenta de Table API durante la versión preliminar, cree una [nueva cuenta de Table API](create-table-dotnet.md#create-a-database-account) para trabajar con los SDK de Table API disponibles con carácter general.
>

## <a name="next-steps"></a>pasos siguientes

Estas son algunas sugerencias para comenzar:
* [Creación de una aplicación .NET con la API Table](create-table-dotnet.md)
* [Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)
* [Azure Cosmos DB: instrucciones de realización de consultas de tablas de datos con Table API (versión preliminar)](tutorial-query-table.md)
* [Configuración de la distribución global de Azure Cosmos DB con la API Table](tutorial-global-distribution-table.md)
* [API Table de .NET de Azure Cosmos DB](table-sdk-dotnet.md)
* [Table API de Azure Cosmos DB para Java](table-sdk-java.md)
* [Table API de Azure Cosmos DB para Node.js](table-sdk-nodejs.md)
* [SDK de Table de Azure Cosmos DB para Python](table-sdk-python.md)

