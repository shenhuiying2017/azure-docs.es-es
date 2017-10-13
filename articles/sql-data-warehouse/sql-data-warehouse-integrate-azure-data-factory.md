---
title: Uso de Azure Data Factory con SQL Data Warehouse | Microsoft Docs
description: "Sugerencias para usar Factoría de datos de Azure (ADF) con Almacenamiento de datos SQL para el desarrollo de soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Uso de Factoría de datos de Azure con Almacenamiento de datos SQL
Factoría de datos de Azure ofrece un método completamente administrado para organizar la transferencia de datos y la ejecución de los procedimientos almacenados en Almacenamiento de datos SQL.  Esto le permitirá configurar y programar con mayor facilidad los complejos procedimientos de extracción, transformación y carga (ETL) con Almacenamiento de datos SQL. Para obtener una introducción más completa de Azure Data Factory, consulte [Documentación de Azure Data Factory][Azure Data Factory documentation].

## <a name="data-movement"></a>Movimiento de datos
Factoría de datos de Azure permite el movimiento entre orígenes locales y los distintos servicios de Azure.  En general, la integración actual con Factoría de datos de Azure es compatible con el movimiento de datos desde y hacia las ubicaciones siguientes:

* Almacenamiento de blobs de Azure
* Base de datos SQL de Azure
* SQL Server local
* SQL Server en IaaS

Para obtener información sobre cómo configurar una actividad de copia de datos, consulte [Copia de datos con Azure Data Factory][Copy data with Azure Data Factory].

## <a name="stored-procedures"></a>Procedimientos almacenados
 De la misma manera en que se puede programar la transferencia de datos, Factoría de datos de Azure también se puede usar para organizar la ejecución de procedimientos almacenados.  Esto permite que se creen canalizaciones más complejas y extiende la capacidad de Factoría de datos de Azure para aprovechar la potencia de cálculo de Almacenamiento de datos SQL.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general sobre la integración, consulte la [información general de la integración de SQL Data Warehouse][SQL Data Warehouse integration overview].
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

