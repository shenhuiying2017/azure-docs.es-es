---
title: "SqlPackage: Importación a Azure SQL Database desde un archivo BACPAC | Microsoft Docs"
description: "En este artículo se muestra cómo importar a SQL Database desde un archivo BACPAC mediante la utilidad de línea de comandos SqlPackage."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, importación de archivo BACPAC, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>Importación de una base de datos de un archivo BACPAC a Azure SQL Database mediante SqlPackage

En este artículo se muestra cómo importar a SQL Database desde un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad de línea de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directamente desde el Centro de descarga Microsoft.

> [!NOTE]
> En los pasos siguientes se da por supuesto que un servidor de Base de datos SQL está ya aprovisionado, que tiene a mano la información de conexión y ha comprobado que la base de datos de origen es compatible.
> 
> 

## <a name="import-the-database"></a>Importar la base de datos
Use la utilidad de línea de comandos [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar una base de datos SQL Server compatible (o una base de datos de Azure SQL Database) desde un archivo BACPAC.

> [!NOTE]
> En los pasos siguientes se da por supuesto que un servidor de Base de datos SQL de Azure está ya aprovisionado y que tiene a mano la información de conexión.
>  

En una ventana del símbolo del sistema en el directorio que contiene la versión más reciente de la utilidad de línea de comandos sqlpackage.exe, ejecute un comando similar al siguiente comando de muestra que importa un archivo BACPAC en una base de datos de Azure SQL Database como Premium P11.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
* Para ver contenido de referencia sobre SQLPackage, consulte [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).
* Para descargar la versión más reciente de SQLPackage, consulte [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876).
* Consulte cómo [migrar de SQL Server a Azure SQL Database con archivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) en el blog de Customer Advisory Team de SQL Server.




<!--HONumber=Feb17_HO2-->


