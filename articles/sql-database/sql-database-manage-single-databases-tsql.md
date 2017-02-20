---
title: "T-SQL: Creación y administración de instancias de Azure SQL Database únicas | Microsoft Docs"
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
ms.sourcegitcommit: 38c53d6f1a2635afbba199e6f0a2adb23abb6a3a
ms.openlocfilehash: fcf8326edc2ff84d274f7c4dadeae1fbe2595349


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Creación y administración de instancias de Azure SQL Database únicas con Transact-SQL

Puede crear y administrar instancias de Azure SQL Database únicas con [Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, la API de REST o C#. Este tema trata de cómo usar Azure Portal. Para PowerShell, consulte [Creación y administración de bases de datos únicas con Powershell](sql-database-manage-single-databases-powershell.md). Para Transact-SQL, consulte [Creación y administración de bases de datos únicas con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Creación de una base de datos SQL de Azure mediante Transact-SQL en SQL Server Management Studio

Para crear una base de datos de SQL Database mediante Transact-SQL en SQL Server Management Studio, siga estos pasos:

1. Con SQL Server Management Studio, conéctese al servidor de base de datos de Azure mediante las credenciales de inicio de sesión de entidad de seguridad de nivel de servidor o las de algún miembro del rol **dbmanager**. Para obtener más información, consulte [Administración de inicios de sesión](sql-database-manage-logins.md).
2. En el explorador de objetos, abra el nodo Base de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestro** y, después, haga clic en **Nueva consulta**.
3. Use la instrucción **CREATE DATABASE** para crear una base de datos. Para obtener más información, consulte [CREATE DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La siguiente instrucción crea una base de datos con el nombre **myTestDB** y especifica que se trata de una base de datos Standard S0 Edition con un tamaño máximo de 250 GB.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. En el explorador de objetos, haga clic con el botón derecho en el nodo Bases de datos y haga clic en **Actualizar** para ver la nueva base de datos en dicho explorador. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Cambio del nivel de servicio y el nivel de rendimiento de una base de datos única
Cambio del tamaño máximo de la base de datos usando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx)

> [!TIP]
> Para obtener un tutorial de creación de una base de datos con Transact-SQL, consulte [Creación de una base de datos: Azure Portal](sql-database-get-started.md).
>

## <a name="next-steps"></a>Pasos siguientes
* Para ver información general de las herramientas de administración, consulte el [artículo al respecto](sql-database-manage-overview.md).
* Para ver cómo realizar tareas de administración con Azure Portal, lea [Administración de instancias de Azure SQL Database mediante Azure Portal](sql-database-manage-portal.md).
* Para ver cómo realizar otras tareas de administración con PowerShell, visite [Administración de Azure SQL Database mediante PowerShell](sql-database-manage-powershell.md).
* Para ver cómo realizar tareas de administración con SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obtener más información sobre el servicio SQL Database, lea el [artículo en el que se describe qué es SQL Database](sql-database-technical-overview.md). 
* Para obtener más información sobre las características y los servidores de bases de datos de Azure SQL Database, visite [el artículo al respecto](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


