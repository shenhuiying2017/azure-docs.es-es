---
title: "SSMS: Exportación de una Base de datos SQL Server a un archivo BACPAC (Azure) | Microsoft Docs"
description: "En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo BACPAC mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio."
keywords: "Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos, exportación de archivo BACPAC, asistente para exportación de aplicaciones de capa de datos"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f0238ac34a4a047bd0286a1a87d2427caf0e06fd


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportación de una base de datos SQL Server a un archivo BACPAC mediante SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio. 

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.
   
   > [!IMPORTANT]
   > Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Abra Management Studio y conéctese a la base de datos de origen en el Explorador de objetos.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. Haga clic con el botón derecho en la base de datos de origen en el Explorador de objetos, seleccione **Tareas** y haga clic en **Exportar aplicación de capa de datos**.
   
    ![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. En el asistente para la exportación, configure la exportación para guardar el archivo BACPAC en una ubicación de disco local o en un blob de Azure. El archivo BACPAC exportado siempre incluye el esquema de la base de datos completa y, de manera predeterminada, los datos de todas las tablas. Si desea excluir los datos de algunas tablas o de todas ellas, use la pestaña de opciones avanzadas. Por ejemplo, puede exportar únicamente los datos para las tablas de referencia en lugar de desde todas las tablas.

    ![Exportar configuración](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > Al exportar un archivo BACPAC a Azure Blob Storage, utilice el almacenamiento estándar. No se admite la importación de un archivo BACPAC de Almacenamiento premium.
   >
   
## <a name="next-steps"></a>Pasos siguientes
* [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Importar un archivo BACPAC a la Base de datos SQL de Azure mediante el Portal de Azure](sql-database-import.md)
* [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Características de SQL Database](sql-database-features.md)
* [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
* [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


