---
title: Migración del esquema a Almacenamiento de datos SQL | Microsoft Docs
description: Sugerencias para migrar el esquema a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/25/2016
ms.author: jrj;barbkess;sonyama

---
# Migración del esquema a Almacenamiento de datos SQL
Los resúmenes siguientes lo ayudan a comprender las diferencias entre SQL Server y Almacenamiento de datos SQL para que le resulte más fácil migrar la base de datos.

## Migración de la tabla
Al migrar las tablas, deberá familiarizarse con las características de la tabla de las tablas de Almacenamiento de datos SQL. La [información general de la tabla][información general de la tabla] es un excelente lugar para comenzar. Este artículo presenta las consideraciones más importantes al crear una tabla, como las estadísticas de tabla, la distribución, la creación de particiones y los índices. También describe algunas [características de las tablas no admitidas][características de las tablas no admitidas] y las soluciones alternativas.

Almacenamiento de datos SQL admite los tipos de datos empresariales más habituales. Consulte el artículo [Tipos de datos][Tipos de datos] para obtener una lista de tipos de datos admitidos y [no admitidos][no admitidos]. El artículo [Tipos de datos][Tipos de datos] también contiene una consulta para identificar los [Tipos de datos no admitidos][Tipos de datos no admitidos]. Al convertir los tipos de datos, asegúrese de consultar la sección [Procedimientos recomendados para los tipos de datos][Procedimientos recomendados para los tipos de datos].

## Pasos siguientes
Una vez migrado correctamente el esquema de base de datos al Almacenamiento de datos SQL, continúe con uno de los siguientes artículos:

* [Migración de los datos][Migración de los datos]
* [Migración del código][Migración del código]

Para obtener más información sobre los procedimientos recomendados de Almacenamiento de datos SQL, consulte el artículo sobre [procedimientos recomendados][procedimientos recomendados].

<!--Image references-->

<!--Article references-->
[Migración del código]: ./sql-data-warehouse-migrate-code.md
[Migración de los datos]: ./sql-data-warehouse-migrate-data.md
[procedimientos recomendados]: ./sql-data-warehouse-best-practices.md
[información general de la tabla]: ./sql-data-warehouse-tables-overview.md
[características de las tablas no admitidas]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Tipos de datos no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Procedimientos recomendados para los tipos de datos]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->