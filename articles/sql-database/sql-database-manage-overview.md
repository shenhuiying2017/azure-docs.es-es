---
title: Herramientas para administrar y desarrollar con Azure SQL Database | Microsoft Docs
description: "Presenta las herramientas de administración y desarrollo, y las opciones de Azure SQL Database."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>Información general: herramientas para administrar y desarrollar con Azure SQL Database
En este tema se presentan las herramientas para administrar y desarrollar con las bases de datos de Azure SQL Database.

> [!IMPORTANT]
> Este conjunto de documentación incluye guías de inicio rápido, muestras y guías de instrucciones que explican cómo administrar y desarrollar aplicaciones con Azure SQL Database usando las herramientas que se presentan en los párrafos siguientes. Utilice el cuadro de filtros y el panel de navegación de la izquierda para buscar contenido específico para Azure Portal, PowerShell y T-SQL.
>

## <a name="azure-portal"></a>Portal de Azure
El [Portal de Azure](https://portal.azure.com) es una aplicación basada en web donde puede crear, actualizar y eliminar bases de datos y servidores lógicos, así como supervisar la actividad de las bases de datos. Esta herramienta es fantástica si acaba de empezar a usar Azure, administra unas cuentas bases de datos o necesita hacer algo rápidamente.

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio y Transact-SQL
SQL Server Management Studio (SSMS) es una herramienta de cliente que se ejecuta en el equipo para administrar su base de datos en la nube mediante Transact-SQL. Muchos administradores de bases de datos están familiarizados con SSMS, que se puede usar con Bases de datos SQL de Azure. [Descargue la última versión de SSMS](https://msdn.microsoft.com/library/mt238290) y use siempre la más reciente cuando trabaje con la Base de datos SQL de Azure. 

> [!IMPORTANT]
> Use siempre la versión más reciente de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290).
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools en Visual Studio
SQL Server Data Tools (SSDT) es una herramienta de cliente que se ejecuta en el equipo para desarrollar su base de datos en la nube. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), [pruebe a usar SSDT en Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx).  

> [!IMPORTANT]
> Use siempre la versión más reciente de [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y SQL Database.
>  
## <a name="powershell"></a>PowerShell
Puede usar PowerShell para administrar bases de datos y grupos elásticos, y para automatizar las implementaciones de recursos de Azure. Microsoft recomienda esta herramienta para administrar un gran número de bases de datos y automatizar la implementación y los cambios de los recursos en un entorno de producción.

## <a name="elastic-database-tools"></a>Herramientas de Base de datos elástica
Use las herramientas de bases de datos elásticas para realizar acciones como las siguientes: 

* Ejecutar un script de T-SQL con un conjunto de bases de datos mediante un [trabajo elástico](sql-database-elastic-jobs-overview.md)
* Mover bases de datos de modelos multiinquilino a un modelo de inquilino único con la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md)
* Administrar bases de datos en un modelo de inquilino único o un modelo multiinquilino con la [biblioteca cliente de escalado elástico](sql-database-elastic-database-client-library.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Administrador de recursos de Azure](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)


