<properties
	pageTitle="Información general: herramientas de administración para Base de datos SQL"
	description="Compara las herramientas y opciones para administrar Base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="sstein"/>

# Información general: herramientas de administración para Base de datos SQL

En este tema se exploran y se comparan las herramientas y opciones para administrar Bases de datos SQL de Azure con el fin de que pueda elegir la herramienta adecuada para el trabajo, la empresa y para usted. Elegir la herramienta adecuada depende del número de bases de datos que administre, la tarea y la frecuencia con la que se realiza una tarea.

## Portal de Azure

El [Portal de Azure](https://portal.azure.com) es una aplicación basada en web donde puede crear, actualizar y eliminar bases de datos y servidores lógicos, así como supervisar la actividad de las bases de datos. Esta herramienta es fantástica si acaba de empezar a usar Azure y administra un pequeño número de bases de datos o debe realizar algo rápidamente.

Para obtener información más detallada acerca de cómo usar el portal, consulte [Administración de Bases de datos SQL mediante el Portal de Azure clásico](sql-database-manage-portal.md).

## SQL Server Management Studio y SQL Server Data Tools en Visual Studio

SQL Server Management Studio (SSMS) y SQL Server Data Tools (SSDT) en Visual Studio son herramientas de cliente que se ejecutan en el equipo y permiten conectar, administrar y desarrollar la base de datos en la nube. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), [pruebe a usar SSDT en Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muchos administradores de bases de datos están familiarizados con SSMS, que se puede usar con Bases de datos SQL de Azure. [Descargue la última versión de SSMS](https://msdn.microsoft.com/library/mt238290) y use siempre la más reciente cuando trabaje con la Base de datos SQL de Azure. Para obtener más información acerca de cómo administrar Bases de datos SQL de Azure con SSMS, consulte [Administración de Bases de datos SQL mediante SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Debe usar siempre la versión más reciente de SQL Server Management Studio y de SQL Server Data Tools para Visual Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. Las versiones anteriores de estas herramientas no funcionarán correctamente con Base de datos SQL.

## Herramientas de línea de comandos

Puede usar herramientas de línea de comandos como PowerShell para administrar bases de datos y grupos de bases de datos elásticas y para automatizar las implementaciones de recursos de Azure. Microsoft recomienda esta herramienta para administrar un gran número de bases de datos y automatizar la implementación y los cambios de los recursos en un entorno de producción.

Para obtener más información acerca de cómo administrar Bases de datos SQL de Azure con las herramientas de línea de comandos, consulte [Administrar Base de datos SQL con PowerShell](sql-database-command-line-tools.md).

## Herramientas de Base de datos elástica
Use las herramientas de bases de datos elásticas para realizar acciones como las siguientes:

* Ejecutar un script de T-SQL con un conjunto de bases de datos mediante un [trabajo elástico](sql-database-elastic-jobs-overview.md)
* Mover bases de datos de modelos multiinquilino a un modelo de inquilino único con la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md)
* Administrar bases de datos en un modelo de inquilino único o un modelo multiinquilino con la [biblioteca cliente de escalado elástico](sql-database-elastic-database-client-library.md)
 

<!---HONumber=AcomDC_0720_2016-->