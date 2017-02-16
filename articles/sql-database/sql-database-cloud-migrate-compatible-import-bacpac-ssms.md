---
title: "SSMS: Importación a Azure SQL Database desde un archivo BACPAC | Microsoft Docs"
description: "En este artículo se muestra cómo importar desde un archivo BACPAC a SQL Database mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio."
keywords: "Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 24375fc6-c94c-43ef-97ec-fce77343b581
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
ms.openlocfilehash: b5a6d90069e87d6115a3b80711f06ccf286b3e88


---
# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importar desde BACPAC a la Base de datos SQL con SSMS
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portal de Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

En este artículo se muestra cómo importar desde un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) a la Base de datos SQL mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio.

> [!NOTE]
> En los pasos siguientes se da por supuesto que la instancia lógica SQL de Azure está ya aprovisionada y que tiene a mano la información de conexión.
> 
> 

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.
   
   > [!IMPORTANT]
   > Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Conéctese al servidor de Azure SQL Database, haga clic con el botón derecho en la carpeta **Bases de datos** y haga clic en **Importar la aplicación de capa de datos**.
   
    ![Importar elemento de menú de aplicación de capa de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)
3. Para crear la base de datos en Base de datos SQL de Azure, importe el archivo BACPAC desde el disco local o seleccione la cuenta de almacenamiento de Azure y el contenedor en el que cargó el archivo BACPAC.
   
   ![Importar configuración](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)
   
   > [!IMPORTANT]
   > Al importar un archivo BACPAC a Almacenamiento de blobs de Azure, utilice el almacenamiento estándar. No se admite la importación de un archivo BACPAC de Almacenamiento premium.
   > 
   > 
4. Especifique un valor en **Nuevo nombre de base de datos** para la base de datos de Azure SQL Database, establezca **Edición de Microsoft Azure SQL Database** (nivel de servicio), **Tamaño máximo de la base de datos** y **Objetivo de servicio** (nivel de rendimiento).
   
   ![Configuración de base de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)
5. Haga clic en **Siguiente** y, después, en **Finalizar** para importar el archivo BACPAC a una nueva base de datos en el servidor de Azure SQL Database.
6. Con el Explorador de objetos, conéctese a la base de datos migrada en el servidor de Base de datos SQL de Azure.
7. Use el Portal de Azure para ver la base de datos y sus propiedades.

## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


