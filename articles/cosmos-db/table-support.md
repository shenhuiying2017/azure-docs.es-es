---
title: Compatibilidad de Azure Table Storage con Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre cómo funcionan conjuntamente Table API de Azure Cosmos DB y Azure Table Storage."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Implementación con Table API de Azure Cosmos DB y Azure Table Storage

Table API de Azure Cosmos DB y Azure Table Storage comparten el mismo modelo de datos de tablas y exponen las mismas operaciones de creación, eliminación, actualización y consulta a través de sus SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desarrollo con Table API de Azure Cosmos DB

En este momento, [Table API de Azure Cosmos DB](table-introduction.md) tiene cuatro SDK disponibles para el desarrollo: 
- SDK de .NET de [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Esta biblioteca tiene las mismas firmas de método y clases que el [SDK de Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage) público, pero también se puede conectar a cuentas de Azure Cosmos DB mediante Table API. 
- [SDK de Python](table-sdk-python.md). El nuevo SDK de Python de Azure Cosmos DB es el único SDK que admite Azure Table Storage en Python. Este SDK se conecta con Azure Table Storage y Table API de Azure Cosmos DB.
- [SDK de Java](table-sdk-java.md). Este SDK de Azure Storage tiene la capacidad de conectarse a las cuentas de Azure Cosmos DB mediante Table API.
- [SDK de Node.js](table-sdk-nodejs.md). Este SDK de Azure Storage tiene la capacidad de conectarse a las cuentas de Azure Cosmos DB mediante Table API.

Puede encontrar información adicional sobre el funcionamiento con Table API en el artículo [Preguntas más frecuentes: Desarrollar con Table API](faq.md#develop-with-the-table-api).

## <a name="developing-with-the-azure-table-storage"></a>Desarrollo con Azure Table Storage

[Azure Table Storage](table-storage-overview.md) tiene muchos SDK y tutoriales que actualmente se encuentran disponibles en la sección [Azure Table Storage](table-storage-overview.md). Estos artículos se actualizan a medida que la interoperabilidad entre los SDK de Azure Table Storage y Table API de Azure Cosmos DB está disponible.  





