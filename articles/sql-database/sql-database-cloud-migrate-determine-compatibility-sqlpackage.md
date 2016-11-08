---
title: Determinación de la compatibilidad con Base de datos SQL mediante SqlPackage.exe | Microsoft Docs
description: Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, compatibilidad de Base de datos SQL, SqlPackage
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
# Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [Asesor de actualizaciones](http://www.microsoft.com/download/details.aspx?id=48119)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

En este artículo aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a Base de datos SQL mediante la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Uso de SqlPackage.exe
1. Abra un símbolo del sistema y cambie a un directorio que contiene la versión más reciente de sqlpackage.exe. Esta utilidad se incluye con las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/es-ES/download/details.aspx?id=53876) directamente desde el Centro de descarga Microsoft.
2. Ejecute el siguiente comando SqlPackage con los argumentos siguientes para su entorno:
   
    'sqlpackage.exe /Action:Export /ssn:< nombre\_servidor > /sdn:< nombre\_basedatos > /tf:< archivo\_destino > /p:TableData=< schema\_name.table\_name > > < archivo\_salida > 2>&1'
   
   | Argumento | Descripción |
   | --- | --- |
   | < nombre\_servidor > |nombre de servidor de origen |
   | < nombre\_basedatos > |nombre de base de datos de origen |
   | < archivo\_destino > |nombre y ubicación del archivo BACPAC |
   | <nombre\_esquema.nombre\_tabla > |tablas cuyos datos se envían al archivo de destino |
   | < archivo\_salida > |nombre y ubicación del archivo de salida con los errores, si existen |
   
    El motivo del argumento /p:TableName es que solo deseamos comprobar la compatibilidad de la base de datos para la exportación a Base de datos SQL de Azure V12, en lugar de exportar los datos de todas las tablas. Lamentablemente, el argumento de exportación de sqlpackage.exe no permite extraer cero tablas. Debe especificar al menos una tabla, por ejemplo, una sola tabla pequeña. < archivo\_salida > contendrá el informe de los errores. La cadena "> 2>&1" une la salida estándar y el error estándar resultado de la ejecución del comando en el archivo de salida especificado.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)
3. Abra el archivo de salida y revise los errores de compatibilidad, si los hay.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Solución de problemas de compatibilidad de migración de bases de datos](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Recursos adicionales
* [Base de datos SQL V12](sql-database-v12-whats-new.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->