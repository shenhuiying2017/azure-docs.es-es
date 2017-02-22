---
title: "SSMS: Exportación de una base de datos a un archivo BACPAC (Azure) | Microsoft Docs"
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
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportación de una base de datos de Azure SQL Database o SQL Server a un archivo BACPAC mediante SQL Server Management Studio        
        
En este artículo se muestra cómo exportar una base de datos de Azure de SQL Database o SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio. Para obtener información general de la exportación a un archivo BACPAC, consulte [Exportación de una base de datos Azure SQL Database a un archivo BACPAC mediante Azure Portal](sql-database-export.md).    
        
1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.        
           
   > [!IMPORTANT]        
   > Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Versión más reciente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx).        
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
* [Versión más reciente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* Para obtener información sobre cómo importar un BACPAC a una base de datos de SQL Server, consulte [Importación de un BACPCAC en una Base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)
* Para obtener información sobre el proceso de migración de bases de datos de SQL Server completo, incluidas las recomendaciones de rendimiento, consulte [Migración de una base de datos de SQL Server a Azure SQL Database](sql-database-cloud-migrate.md).
* Para obtener información acerca de la conservación de copias de seguridad a largo plazo de una copia de seguridad de Azure SQL Database como una alternativa a la exportación de una base de datos para archivarla, consulte [Retención a largo plazo](sql-database-long-term-retention.md).


    



<!--HONumber=Feb17_HO2-->


