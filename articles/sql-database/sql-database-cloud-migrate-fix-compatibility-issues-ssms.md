---
title: Solución de problemas de compatibilidad con bases de datos SQL Server mediante SQL Server Management Studio antes de la migración a la Base de datos SQL | Microsoft Docs
description: Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Solución de problemas de compatibilidad de bases de datos de SQL Server mediante SQL Server Management Studio antes de la migración a la Base de datos SQL
> [!div class="op_single_selector"]
> * Uso del [Asistente para migración a SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Uso de [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Uso de [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
> 
> 

Los usuarios avanzados pueden corregir los problemas de compatibilidad con las bases de datos SQL Server mediante SQL Server Management Studio antes de realizar la migración a Base de datos SQL de Azure.

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## Uso de SQL Server Management Studio
Use SQL Server Management Studio para corregir los problemas de compatibilidad con varios comandos de Transact-SQL, como **ALTER DATABASE**. Este método va dirigido principalmente a usuarios avanzados que se sienten a gusto trabajando con Transact-SQL en una base de datos activa. De lo contrario, se recomienda el uso de SSDT.

## Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Recursos adicionales
* [Base de datos SQL V12](sql-database-v12-whats-new.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->