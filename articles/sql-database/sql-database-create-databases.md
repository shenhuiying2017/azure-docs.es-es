---
title: "Creación de una base de datos SQL de Azure | Microsoft Docs"
description: "Referencia rápida sobre cómo crear una base de datos de Azure SQL Database mediante Azure Portal, PowerShell o Transact-SQL."
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>Crear una base de datos SQL de Azure

Puede crear una base de datos SQL de Azure mediante [Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, la API de REST o C#. 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>Creación de una base de datos SQL de Azure mediante Azure Portal

1. Abra la hoja **Bases de datos SQL** de [Azure Portal](https://portal.azure.com/). 

    ![bases de datos sql](./media/sql-database-get-started/sql-databases.png)
2. En la hoja Bases de datos SQL, haga clic en **Agregar**.

    ![agregar base de datos sql](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Para ver un tutorial de introducción al uso de Azure Portal y SQL Server Management Studio, consulte [Empezar a trabajar con servidores, bases de datos y reglas de firewall de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="create-an-azure-sql-database-using-powershell"></a>Creación de una base de datos SQL de Azure con PowerShell

Para crear una base de datos SQL, use el cmdlet [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). El grupo de recursos y el servidor ya deben existir en la suscripción. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```
> [!TIP]
> Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md).
>

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Creación de una base de datos SQL de Azure mediante Transact-SQL en SQL Server Management Studio

Para crear una base de datos de SQL Database mediante Transact-SQL en SQL Server Management Studio, siga estos pasos:

1. Con SQL Server Management Studio, conéctese al servidor de base de datos de Azure mediante las credenciales de inicio de sesión de entidad de seguridad de nivel de servidor o las de algún miembro del rol **dbmanager**. Para obtener más información, consulte [Administración de inicios de sesión](sql-database-manage-logins.md).
2. En el explorador de objetos, abra el nodo Base de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestro** y, después, haga clic en **Nueva consulta**.
3. Use la instrucción **CREATE DATABASE** para crear una base de datos. Para obtener más información, consulte [CREATE DATABASE (Base de datos SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La siguiente instrucción crea una base de datos con el nombre **myTestDB** y especifica que se trata de una base de datos Standard S0 Edition con un tamaño máximo de 250 GB.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. En el explorador de objetos, haga clic con el botón derecho en el nodo Bases de datos y haga clic en **Actualizar** para ver la nueva base de datos en dicho explorador. 

> [!TIP]
> Para ver un tutorial de introducción al uso de Azure Portal y SQL Server Management Studio, consulte [Empezar a trabajar con servidores, bases de datos y reglas de firewall de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="additional-resources"></a>Recursos adicionales
* Para ver información general de las herramientas de administración, consulte el [artículo al respecto](sql-database-manage-overview.md).
* Para ver cómo realizar tareas de administración con Azure Portal, lea [Administración de instancias de Azure SQL Database mediante Azure Portal](sql-database-manage-portal.md).
* Para ver cómo realizar otras tareas de administración con PowerShell, visite [Administración de Azure SQL Database mediante PowerShell](sql-database-manage-powershell.md).
* Para ver cómo realizar tareas de administración con SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obtener más información sobre el servicio SQL Database, lea el [artículo en el que se describe qué es SQL Database](sql-database-technical-overview.md). 
* Para obtener más información sobre las características y los servidores de bases de datos de Azure SQL Database, visite [el artículo al respecto](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


