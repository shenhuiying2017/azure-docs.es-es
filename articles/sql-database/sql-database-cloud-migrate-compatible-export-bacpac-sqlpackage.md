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
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: e38c42ea1c078f9f2e11ae51be37d66671392828


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directamente desde el Centro de descarga Microsoft.

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
* [Newest version of SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Import a BACPAC to Azure SQL Database using SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Import a BACPAC to Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Import a BACPAC to Azure SQL Database Azure portal](sql-database-import.md)
* [Import a BACPAC to Azure SQL Database PowerShell](sql-database-import-powershell.md)

## Additional resources
* [SQL Database features](sql-database-features.md)
* [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


