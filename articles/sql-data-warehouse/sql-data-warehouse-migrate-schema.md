---
title: "Migración del esquema a SQL Data Warehouse | Microsoft Docs"
description: Sugerencias para migrar el esquema a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
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
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.contentlocale: es-es
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migración del esquema a Almacenamiento de datos SQL
Los resúmenes siguientes lo ayudan a comprender las diferencias entre SQL Server y Almacenamiento de datos SQL para que le resulte más fácil migrar la base de datos.

## <a name="table-migration"></a>Migración de la tabla
Al migrar las tablas, deberá familiarizarse con las características de la tabla de las tablas de Almacenamiento de datos SQL.  La [información general sobre tablas][table overview] es un excelente lugar para comenzar.  Este artículo presenta las consideraciones más importantes al crear una tabla, como las estadísticas de tabla, la distribución, la creación de particiones y los índices.  También describe algunas [características no admitidas de las tablas][unsupported table features] y las soluciones alternativas a tales carencias.

Almacenamiento de datos SQL admite los tipos de datos empresariales más habituales.  Consulte el artículo sobre [tipos de datos][data types] para obtener una lista de tipos de datos admitidos y [no admitidos][unsupported data types].  El artículo sobre [tipos de datos][data types] contiene asimismo una consulta para identificar [tipos de datos no admitidos][unsupported data types].  Al convertir los tipos de datos, asegúrese de consultar los [procedimientos recomendados para los tipos de datos][data type best practices].

## <a name="next-steps"></a>Pasos siguientes
Una vez migrado correctamente el esquema de base de datos al Almacenamiento de datos SQL, continúe con uno de los siguientes artículos:

* [Migración de los datos][Migrate your data]
* [Migración del código][Migrate your code]

Para obtener más información sobre los procedimientos recomendados de SQL Data Warehouse, consulte el artículo sobre [procedimientos recomendados][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

