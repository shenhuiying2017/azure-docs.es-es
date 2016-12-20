---
title: Uso de Azure Data Factory con SQL Data Warehouse | Microsoft Docs
description: "Sugerencias para usar Factoría de datos de Azure (ADF) con Almacenamiento de datos SQL para el desarrollo de soluciones."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 427a1e8c6bfb11ace38fcf84b7cda89d2d01d091


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Uso de Factoría de datos de Azure con Almacenamiento de datos SQL
Factoría de datos de Azure ofrece un método completamente administrado para organizar la transferencia de datos y la ejecución de los procedimientos almacenados en Almacenamiento de datos SQL.  Esto le permitirá configurar y programar con mayor facilidad los complejos procedimientos de extracción, transformación y carga (ETL) con Almacenamiento de datos SQL. Para una introducción más completa de Azure Data Factory, consulte la [Documentación de Azure Data Factory][Documentación de Azure Data Factory].

## <a name="data-movement"></a>Movimiento de datos
Factoría de datos de Azure permite el movimiento entre orígenes locales y los distintos servicios de Azure.  En general, la integración actual con Factoría de datos de Azure es compatible con el movimiento de datos desde y hacia las ubicaciones siguientes:

* Almacenamiento de blobs de Azure
* Base de datos SQL de Azure
* SQL Server local
* SQL Server en IaaS

Para información sobre cómo configurar una actividad de copia de datos, consulte [Copia de datos con Data Factory de Azure][Copia de datos con Data Factory de Azure]

## <a name="stored-procedures"></a>Procedimientos almacenados
 De la misma manera en que se puede programar la transferencia de datos, Factoría de datos de Azure también se puede usar para organizar la ejecución de procedimientos almacenados.  Esto permite que se creen canalizaciones más complejas y extiende la capacidad de Factoría de datos de Azure para aprovechar la potencia de cálculo de Almacenamiento de datos SQL.

## <a name="next-steps"></a>Pasos siguientes
Para información general sobre la integración, consulte la [Información general de la integración de Almacenamiento de datos SQL][Información general de la integración de Almacenamiento de datos SQL].
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][información general sobre desarrollo de Almacenamiento de datos SQL].

<!--Image references-->

<!--Article references-->

[Copia de datos con Data Factory de Azure]: ../data-factory/data-factory-data-movement-activities.md
[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop.md
[Información general de la integración de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentación de Azure Data Factory]:https://azure.microsoft.com/documentation/services/data-factory/




<!--HONumber=Nov16_HO3-->


