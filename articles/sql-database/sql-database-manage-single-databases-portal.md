---
title: "Azure Portal: creación y administración de instancias de Azure SQL Database únicas | Microsoft Docs"
description: "Referencia rápida sobre cómo crear y administrar una instancia de Azure SQL Database única con Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 319e22e2fb423e0fe1fabf95d0018d4fa3232e0b
ms.openlocfilehash: 5d7f11b9b027f86adf3d2382e82f2d382d71ee51


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Creación y administración de instancias de Azure SQL Database únicas con Azure Portal

Puede crear y administrar instancias de Azure SQL Database únicas con [Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, la API de REST o C#. Este tema trata de cómo usar Azure Portal. Para PowerShell, consulte [Creación y administración de bases de datos únicas con Powershell](sql-database-manage-single-databases-powershell.md). Para Transact-SQL, consulte [Creación y administración de bases de datos únicas con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Creación de una instancia de Azure SQL Database única con Azure Portal

1. Abra la hoja **Bases de datos SQL** de [Azure Portal](https://portal.azure.com/). 

    ![bases de datos sql](./media/sql-database-get-started/sql-databases.png)
2. En la hoja Bases de datos SQL, haga clic en **Agregar**.

    ![agregar base de datos sql](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Para obtener un tutorial de creación de una base de datos con Azure Portal, consulte [Creación de una base de datos: Azure Portal](sql-database-get-started.md).
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Visualización y actualización de la configuración de SQL Database mediante Azure Portal

1. Abra la hoja **SQL Database** de [Azure Portal](https://portal.azure.com/). 

    ![bases de datos sql](./media/sql-database-get-started/sql-databases.png)

2. Haga clic en la base de datos con la que quiere trabajar y luego en el valor deseado en la hoja SQL Database.

    ![hoja nueva base de datos de ejemplo](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Cambio del nivel de servicio y el nivel de rendimiento de una base de datos única
Abra la hoja Base de datos SQL correspondiente a la base de datos que desea escalar o reducir verticalmente:

1. En [Azure Portal](https://portal.azure.com), haga clic en **Más servicios** > **SQL Database**.
2. Haga clic en la base de datos que desee cambiar.
3. En la hoja **SQL Database**, haga clic en **Plan de tarifa (escalar DTU)**:
   
   ![Plan de tarifa](./media/sql-database-manage-single-database-portal/new-tier.png)

4. Seleccione un nuevo nivel y haga clic en **Seleccionar**:
   
   Al hacer clic en **Seleccionar** se envía una petición de escala para cambiar el plan de tarifa. Según el tamaño de la base de datos, la operación de escala puede tardar algún tiempo en completarse (vea la información de la parte superior de este artículo).
   
   > [!NOTE]
   > Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![seleccione nivel de precios](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. Haga clic en el icono de notificación (campana), en la parte superior derecha:
   
   ![Notificaciones](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. Haga clic en el texto de notificación para abrir el panel de detalles donde puede ver el estado de la petición.

## <a name="next-steps"></a>Pasos siguientes
* Para ver información general de las herramientas de administración, consulte el [artículo al respecto](sql-database-manage-overview.md).
* Para ver cómo realizar tareas de administración con Azure Portal, lea [Administración de instancias de Azure SQL Database mediante Azure Portal](sql-database-manage-portal.md).
* Para ver cómo realizar otras tareas de administración con PowerShell, visite [Administración de Azure SQL Database mediante PowerShell](sql-database-manage-powershell.md).
* Para ver cómo realizar tareas de administración con SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obtener más información sobre el servicio SQL Database, lea el [artículo en el que se describe qué es SQL Database](sql-database-technical-overview.md). 
* Para obtener más información sobre las características y los servidores de bases de datos de Azure SQL Database, visite [el artículo al respecto](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


