---
title: "Migración de la solución a SQL Data Warehouse | Microsoft Docs"
description: "Guía de migración para llevar una solución a la plataforma Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.contentlocale: es-es
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migración de una solución a Almacenamiento de datos SQL
Almacenamiento de datos SQL es un sistema de base de datos distribuidas que se amplía elásticamente para satisfacer sus necesidades. Para mantener tanto el rendimiento como la escala, no se implementan todas las características de SQL Server en Almacenamiento de datos SQL. En los siguientes temas sobre migración se abordan algunos de los factores clave para migrar una solución a Almacenamiento de datos SQL. El diseño del almacenamiento de datos para escala presenta diferentes patrones de diseño, de manera que los enfoques tradicionales no son siempre la mejor opción. Por lo tanto, puede que observe que adaptar su solución existente garantiza que saca el máximo rendimiento de la plataforma distribuida que ofrece Almacenamiento de datos SQL.

También es importante recordar que Almacenamiento de datos SQL es una plataforma basada en Microsoft Azure. Por lo tanto, parte de la migración también puede incluir la transferencia de datos a la nube. La transferencia de datos es un tema en sí mismo y debe considerarse detenidamente, en especial a medida que aumentan los volúmenes. La transferencia y la carga de datos son temas discretos.

## <a name="migration-guidance"></a>Guía de migración
Lea estos artículos para asegurarse de que entiende algunas de las diferencias y los conceptos fundamentales del producto antes de comenzar la migración.

* [Migración del esquema][Migrate your schema]
* [Migración de los datos][Migrate your data]
* [Migración del código][Migrate your code]

## <a name="next-steps"></a>Pasos siguientes
El equipo de asesoramiento al cliente también cuenta con buenas directrices sobre Almacenamiento de datos SQL que publican a través de blogs.  Eche un vistazo a su artículo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migración de datos a Azure SQL Data Warehouse en la práctica) para obtener más instrucciones acerca de la migración.

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

