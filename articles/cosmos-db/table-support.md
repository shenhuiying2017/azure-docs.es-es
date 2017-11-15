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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Implementación con Table API de Azure Cosmos DB y Azure Table Storage

Table API de Azure Cosmos DB y Azure Table Storage comparten el mismo modelo de datos de tablas y exponen las mismas operaciones de creación, eliminación, actualización y consulta a través de sus SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desarrollo con Table API de Azure Cosmos DB

En este momento, [Table API de Azure Cosmos DB](table-introduction.md) tiene un SDK de .NET disponible, [Windows Azure Storage Premium Table (versión preliminar)](https://aka.ms/premiumtablenuget). Esta biblioteca tiene las mismas firmas de método y clases que el [SDK de Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage) público, pero también se puede conectar a cuentas de Azure Cosmos DB mediante Table API (versión preliminar). Para consultar un tutorial y una guía de inicio rápido con Table API de Azure Cosmos DB, vea los artículos siguientes:
- Guía de inicio rápido: [Azure Cosmos DB: Compilación de una aplicación de .NET mediante Table API](create-table-dotnet.md)
- Tutorial: [Azure Cosmos DB: desarrollo con Table API en .NET](tutorial-develop-table-dotnet.md)

Puede encontrar información adicional sobre el funcionamiento con Table API en el artículo [Preguntas más frecuentes: Desarrollar con Table API](faq.md#develop-with-the-table-api-preview).

## <a name="developing-with-the-azure-table-storage"></a>Desarrollo con Azure Table Storage

[Azure Table Storage](table-storage-overview.md) tiene muchos SDK y tutoriales que actualmente se encuentran disponibles en la sección [Azure Table Storage](table-storage-overview.md). Estos artículos se actualizan a medida que la interoperabilidad entre los SDK de Azure Table Storage y Table API de Azure Cosmos DB está disponible.  





