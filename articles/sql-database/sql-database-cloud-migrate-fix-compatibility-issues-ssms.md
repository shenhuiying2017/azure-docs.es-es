---
title: "SSMS: Corrección de problemas con la migración de compatibilidad de Azure SQL Database | Microsoft Docs"
description: "En este artículo aprenderá a corregir problemas de compatibilidad con las bases de datos SQL Server mediante SQL Server Management Studio antes de realizar la migración a Azure SQL Database."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 841c4d644c6b096251e1ecefac17a8254b5ca712


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Solución de problemas de compatibilidad de bases de datos de SQL Server mediante SQL Server Management Studio antes de la migración a la Base de datos SQL
> [!div class="op_single_selector"]
> * Uso de [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Uso de [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Uso de [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

Los usuarios avanzados pueden corregir los problemas de compatibilidad con las bases de datos SQL Server mediante SQL Server Management Studio antes de realizar la migración a Base de datos SQL de Azure.

> [!IMPORTANT]
> Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="using-sql-server-management-studio"></a>Uso de SQL Server Management Studio
Use SQL Server Management Studio para corregir los problemas de compatibilidad con varios comandos de Transact-SQL, como **ALTER DATABASE**. Este método va dirigido principalmente a usuarios avanzados que se sienten a gusto trabajando con Transact-SQL en una base de datos activa. De lo contrario, se recomienda el uso de SSDT. 

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


