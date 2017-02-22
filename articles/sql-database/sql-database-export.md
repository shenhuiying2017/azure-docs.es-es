---
title: "Exportación de una base de datos Azure SQL Database a un archivo BACPAC | Microsoft Docs"
description: "Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: b8a2a33909f77dd1bbe0a7b454e15df7432164a8


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Exportación de una base de datos de Azure SQL Database o SQL Server a un archivo BACPAC

En este artículo se analiza cómo exportar la base de datos Azure SQL Database o SQL Server a un archivo BACPAC. 

> [!IMPORTANT]
> La exportación automática de Azure SQL Database Automated Export se encuentra ahora en versión preliminar y se retirará el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, ya no podrá configurar la exportación automatizada en cualquier base de datos SQL. Los trabajos de exportación automatizada existentes continuarán funcionando hasta que el 1 de marzo de 2017. A partir del 1 de diciembre de 2016, podrá usar la [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) o [Azure Automation](../automation/automation-intro.md) para archivar las bases de datos SQL periódicamente mediante PowerShell de acuerdo con la programación que elija. Para obtener un script de ejemplo, puede descargar [este que está disponible en Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="overview"></a>Información general

Cuando necesite exportar una base de datos para archivar o migrar a otra plataforma, puede exportar el esquema de base de datos y los datos a un archivo BACPAC. Un archivo BACPAC es, sencillamente, un archivo ZIP con la extensión BACPAC. Este archivo BACPAC se puede guardar en el almacenamiento de blobs de Azure o en cualquier almacenamiento en una ubicación local y, luego, importarlo a Base de datos SQL de Azure o a una instalación local de SQL Server. 

* Puede exportar la base de datos Azure SQL Database mediante [Azure Portal](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) o [SQL Server Management Studio](sql-database-export-ssms.md).
* Puede exportar una base de datos SQL Server mediante [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) o [SQL Server Management Studio](sql-database-export-ssms.md).

> [!IMPORTANT]
> Si va a exportar desde SQL Server como paso previo a la migración a Azure SQL Database, consulte [Migración de una base de datos SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Consideraciones

* Para que una exportación sea transaccionalmente coherente, debe asegurarse de que no haya ninguna actividad de escritura durante la exportación, o bien de exportar desde una [copia transaccionalmente coherente](sql-database-copy.md) de Azure SQL Database.
* Si va a exportar a Blob Storage, el tamaño máximo de un archivo BACPAC es 200 GB. Para archivar un archivo BACPAC de mayor tamaño, exporte al almacenamiento local.
* No se admite la exportación de un archivo BACPAC en Premium Storage de Azure con los métodos que se describen en este artículo.
* Si la operación de exportación desde Azure SQL Database demora más de 20 horas, es posible que se cancele. Para aumentar el rendimiento durante la exportación, puede hacer lo siguiente:
  * Aumentar temporalmente el nivel de servicio.
  * Detener toda actividad de lectura y escritura durante la exportación.
  * Use un [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) con valores distintos de NULL en todas las tablas de gran tamaño. Sin índices agrupados, la exportación podría no producirse si tarda más de 6-12 horas. Esto se debe a que el servicio de exportación necesita completar el recorrido de tabla para tratar de exportar toda la tabla. Una buena forma de determinar si las tablas están optimizadas para la exportación es ejecutar **DBCC SHOW_STATISTICS** y asegurarse de que *RANGE_HI_KEY* no es NULL y su valor tiene buena distribución. Para obtener más información, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para detalles, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md) y [Copias de seguridad de SQL Database](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Pasos siguientes

* Para información sobre el proceso de migración de bases de datos SQL Server completo, consulte [Migración de una base de datos SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
* Para información general sobre cómo copiar una base de datos dentro de Azure, consulte también [Copia de una base de datos Azure SQL Database](sql-database-copy.md).
* Puede copiar una base de datos Azure SQL Database dentro de Azure mediante [Azure Portal](sql-database-copy-portal.md), [PowerShell](sql-database-copy-powershell.md) o [Transact-SQL](sql-database-copy-transact-sql.md). 



<!--HONumber=Feb17_HO2-->


