---
title: "Importación de datos para su uso con Table API de Azure Cosmos DB | Microsoft Docs"
description: "Aprenda a importar datos que se usarán con Table API de Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importación de datos para su uso con Table API de Azure Cosmos DB

En este tutorial se proporcionan instrucciones sobre cómo importar datos para su uso con [Table API](table-introduction.md) de Azure Cosmos DB. Si tiene datos almacenados en Azure Table Storage, puede usar la Herramienta de migración de datos o az copy para importar los datos en Azure Cosmos DB. Una vez importados los datos, podrá aprovechas las funcionalidades premium que ofrece Azure Cosmos DB, como distribución global inmediata, rendimiento dedicado, latencias inferiores a 10 milisegundos en el percentil 99, alta disponibilidad garantizada e indexación secundaria automática.

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Importación de datos con la Herramienta de migración de datos
> * Importación de datos con AzCopy

## <a name="data-migration-tool"></a>Herramienta de migración de datos

La Herramienta de migración de datos de Azure Cosmos DB es una opción para importar los datos de almacenamiento existentes de Azure Table Storage. Para importar datos con la herramienta, seleccione Azure Table Storage como origen y Table API de Azure Cosmos DB como destino, y proporcione el resto de la información tal y como se solicita en la herramienta. Los datos se pueden exportar en bloque o mediante la importación secuencial de registros. 

En las secciones siguientes se proporciona información sobre la definición de Azure Table Storage como origen y Table API como destino:
- [Using Azure Table storage as a data migration source](import-data.md#AzureTableSource) (Uso de Azure Table Storage como origen de datos de migración). 
- [Exportación a Table API con importación en bloque](import-data.md#tableapibulkexport)
- [Exportación a Table API con importación de registros secuencial](import-data.md#tableapiseqtarget)

## <a name="azcopy-command"></a>Comando AzCopy

La utilidad de línea de comandos AzCopy es la otra opción para migrar datos desde Azure Table Storage a Table API de Azure Cosmos DB. Para usar AzCopy, primero exporte los datos a un archivo de manifiesto, tal y como se describe en [Exportación de datos desde Table Storage](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) y luego importe los datos desde el archivo de manifiesto a [Table API de Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Importar datos con la Herramienta de migración de datos
> * Importar datos con AzCopy

Ahora puede pasar al siguiente tutorial y aprender a consultar los datos mediante Table API de Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Consulta de datos](../cosmos-db/tutorial-query-table.md)



