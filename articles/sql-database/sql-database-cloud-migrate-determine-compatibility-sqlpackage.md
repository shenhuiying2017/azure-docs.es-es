---
title: "Determinación de la compatibilidad con SQL Database mediante SqlPackage.exe | Microsoft Docs"
description: "Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, compatibilidad de Base de datos SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 06563b181a8d4ff568eb7cce15189defab0225ca
ms.openlocfilehash: 16cc9beb3c239e583f47f33fe2f8c7caf44e8eb6


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

En este artículo aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a Base de datos SQL mediante la utilidad de símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Uso de SqlPackage.exe
1. Abra un símbolo del sistema y cambie a un directorio que contiene la versión más reciente de sqlpackage.exe. Esta utilidad se incluye con las versiones más recientes de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), o bien puede descargar la versión más reciente de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directamente desde el Centro de descarga Microsoft.

2. Ejecute el siguiente comando SqlPackage con los argumentos siguientes para su entorno:

   ```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
   ```   

   | Argumento | Descripción |
   | --- | --- |
   | < nombre_servidor > |nombre de servidor de origen |
   | <nombre_basedatos > |nombre de base de datos de origen |
   | <archivo_destino > |nombre y ubicación del archivo BACPAC |
   | <nombre_esquema.nombre_tabla > |tablas cuyos datos se envían al archivo de destino |
   | <archivo_salida > |nombre y ubicación del archivo de salida con los errores, si existen |
   
    El motivo del argumento /p:TableName es que solo deseamos comprobar la compatibilidad de la base de datos para la exportación a Base de datos SQL de Azure V12, en lugar de exportar los datos de todas las tablas. Lamentablemente, el argumento de exportación de sqlpackage.exe no permite extraer cero tablas. Debe especificar al menos una tabla, por ejemplo, una sola tabla pequeña. < archivo_salida > contendrá el informe de los errores. La cadena "> 2>&1" une la salida estándar y el error estándar resultado de la ejecución del comando en el archivo de salida especificado.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra el archivo de salida y revise los errores de compatibilidad, si los hay. 
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Pasos siguientes
* [La versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [La versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Solución de problemas de compatibilidad de migración de bases de datos](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Dec16_HO2-->


