---
title: Migración de una base de datos SQL Server a Base de datos SQL de Azure | Microsoft Docs
description: Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración
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
# Importar desde BACPAC a la Base de datos SQL con SSMS
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
2. Conéctese al servidor de Base de datos SQL de Azure, haga clic con el botón secundario en la carpeta **Bases de datos** y haga clic en **Importar la aplicación de capa de datos**.
   
    ![Importar elemento de menú de aplicación de capa de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)
3. Para crear la base de datos en Base de datos SQL de Azure, importe el archivo BACPAC desde el disco local o seleccione la cuenta de almacenamiento de Azure y el contenedor en el que cargó el archivo BACPAC.
   
   ![Importar configuración](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)
   
   > [!IMPORTANT]
   > Al importar un archivo BACPAC a Almacenamiento de blobs de Azure, utilice el almacenamiento estándar. No se admite la importación de un archivo BACPAC de Almacenamiento premium.
   > 
   > 
4. Proporcione el **Nuevo nombre de base de datos** para la base de datos en Base de datos SQL de Azure, establezca **Edición de Base de datos SQL de Microsoft Azure** (nivel de servicio), **Tamaño máximo de la base de datos** y **Objetivo de servicio** (nivel de rendimiento).
   
   ![Configuración de base de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)
5. Haga clic en **Siguiente** y, luego, en **Finalizar** para importar el archivo BACPAC a una nueva base de datos en el servidor de Base de datos SQL de Azure.
6. Con el Explorador de objetos, conéctese a la base de datos migrada en el servidor de Base de datos SQL de Azure.
7. Use el Portal de Azure para ver la base de datos y sus propiedades.

## Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Recursos adicionales
* [Base de datos SQL V12](sql-database-v12-whats-new.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->