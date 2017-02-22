---
title: "SqlPackage: Exportación de una Base de datos SQL Server a un archivo BACPAC (Azure) | Microsoft Docs"
description: "En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo BACPAC mediante la utilidad de línea de comandos SqlPackage."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos,exportación de archivo BACPAC, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportación de una base de datos de Azure SQL Database o SQL Server a un archivo BACPAC mediante SqlPackage

En este artículo se muestra cómo exportar una base de datos de Azure SQL Database o SQL Server a un archivo BACPAC mediante la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directamente desde el Centro de descarga Microsoft.

Para obtener información general de la exportación a un archivo BACPAC, consulte [Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal](sql-database-export.md).

> [!NOTE]
> También puede exportar el archivo de Azure SQL Database a un archivo BACPAC mediante [Azure Portal](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) o [PowerShell](sql-database-export-powershell.md).
>

## <a name="sqlpackage-utility"></a>Utilidad de SQLPackage

1. Abra un símbolo del sistema y cambie a un directorio que contiene la utilidad de línea de comandos sqlpackage.exe; esta utilidad se incluye con Visual Studio y SQL Server. Use la búsqueda en el equipo para encontrar la ruta de acceso en su entorno.
2. Ejecute el siguiente comando sqlpackage.exe con los argumentos siguientes para su entorno:
   
```    sqlpackage.exe /Action:Export /ssn:<nombre_del_servidor> /sdn:<nombre_de_la_base_de_datos> /tf:<archivo_de_destino>
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](sql-database-import-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)





<!--HONumber=Feb17_HO2-->


