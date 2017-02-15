---
title: "Información general: herramientas de administración para SQL Database | Microsoft Docs"
description: Compara las herramientas y opciones para administrar Base de datos SQL de Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 915292a191da82af9e24e89460d46dc61a062b18


---
# <a name="overview-management-tools-for-sql-database"></a>Información general: herramientas de administración para Base de datos SQL
En este tema se exploran y se comparan las herramientas y opciones para administrar Bases de datos SQL de Azure con el fin de que pueda elegir la herramienta adecuada para el trabajo, la empresa y para usted. Elegir la herramienta adecuada depende del número de bases de datos que administre, la tarea y la frecuencia con la que se realiza una tarea.

## <a name="azure-portal"></a>Portal de Azure
El [Portal de Azure](https://portal.azure.com) es una aplicación basada en web donde puede crear, actualizar y eliminar bases de datos y servidores lógicos, así como supervisar la actividad de las bases de datos. Esta herramienta es fantástica si acaba de empezar a usar Azure, administra unas cuentas bases de datos o necesita hacer algo rápidamente.

Para más información sobre cómo utilizar el portal, consulte [Administración de SQL Database con Azure Portal](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio y SQL Server Data Tools en Visual Studio
SQL Server Management Studio (SSMS) y SQL Server Data Tools (SSDT) son herramientas de cliente que se ejecutan en el equipo para administrar y desarrollar la base de datos en la nube. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), [pruebe a usar SSDT en Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muchos administradores de bases de datos están familiarizados con SSMS, que se puede usar con Bases de datos SQL de Azure. [Descargue la última versión de SSMS](https://msdn.microsoft.com/library/mt238290) y use siempre la más reciente cuando trabaje con la Base de datos SQL de Azure. Para obtener más información acerca de cómo administrar Bases de datos SQL de Azure con SSMS, consulte [Administración de Bases de datos SQL mediante SSMS](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Use siempre la versión más reciente de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) y de [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y SQL Database.
>  

## <a name="powershell"></a>PowerShell
Puede usar PowerShell para administrar bases de datos y grupos elásticos, y para automatizar las implementaciones de recursos de Azure. Microsoft recomienda esta herramienta para administrar un gran número de bases de datos y automatizar la implementación y los cambios de los recursos en un entorno de producción.

Para más información, consulte [Manage SQL Database with PowerShell](sql-database-manage-powershell.md) (Administrar SQL Database con PowerShell).

## <a name="elastic-database-tools"></a>Herramientas de Base de datos elástica
Use las herramientas de bases de datos elásticas para realizar acciones como las siguientes: 

* Ejecutar un script de T-SQL con un conjunto de bases de datos mediante un [trabajo elástico](sql-database-elastic-jobs-overview.md)
* Mover bases de datos de modelos multiinquilino a un modelo de inquilino único con la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md)
* Administrar bases de datos en un modelo de inquilino único o un modelo multiinquilino con la [biblioteca cliente de escalado elástico](sql-database-elastic-database-client-library.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Administrador de recursos de Azure](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO2-->


