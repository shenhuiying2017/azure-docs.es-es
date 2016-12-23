---
title: "Uso de SQL Server Management Studio para determinar la compatibilidad con SQL Database antes de la migración a Azure SQL Database | Microsoft Docs"
description: "Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad de Base de datos SQL, Asistente para exportación de aplicaciones de capa de datos"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: c9ead868-aa1e-4a92-a099-6baf7c0dda32
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/29/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 15d0bf86d6094b6a8c0d6aaf1e1bb59382a81ffb


---
# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Uso de SQL Server Management Studio para determinar la compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 
> 

En este artículo aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a la Base de datos SQL mediante el Asistente para exportación de aplicaciones de capa de datos en SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Uso de SQL Server Management Studio
1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.
   
   > [!IMPORTANT]
   > Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Abra Management Studio y conéctese a la base de datos de origen en el Explorador de objetos.
3. Haga clic con el botón derecho en la base de datos de origen en el Explorador de objetos, seleccione **Tareas** y haga clic en **Exportar aplicación de capa de datos**.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. En el asistente para la exportación, haga clic en **Siguiente** y, en la pestaña **Configuración**, configure la exportación para guardar el archivo BACPAC en una ubicación de disco local o en un blob de Azure. Solo se guarda un archivo BACPAC si no tiene problemas de compatibilidad de bases de datos. Si hay problemas de compatibilidad, estos aparecerán en la consola.
   
    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)
5. Haga clic en la pestaña **Avanzadas** y desactive la casilla **Seleccionar todo** para omitir la exportación de datos. En este caso, nuestro objetivo es solo comprobar la compatibilidad.
   
    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)
6. Haga clic en **Siguiente** y, después, en **Finalizar**. Los problemas de compatibilidad de bases de datos, si los hay, aparecen después de que el asistente valide el esquema.
   
    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)
7. Si no aparece ningún error, la base de datos es compatible y estará listo para la migración. Si se producen errores, tiene que corregirlos. Para ver los errores, haga clic en **Error** de **Validando esquema**. 
    ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)
8. Si el archivo *.BACPAC se generó correctamente, indica que la base de datos es compatible con la Base de datos SQL; ya puede migrar.

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Solución de problemas de compatibilidad de migración de bases de datos](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


