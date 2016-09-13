<properties
	pageTitle="Tutorial de Base de datos SQL: creación de una base de datos SQL | Microsoft Azure"
	description="Obtenga información sobre cómo configurar un servidor lógico de SQL Database, una regla de firewall del servidor, una base de datos SQL y datos de ejemplo. Aprenda también a conectarse con herramientas de cliente, configurar usuarios y configurar una regla de firewall de base de datos."
	keywords="tutorial de base de datos SQL, creación de una base de datos SQL"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# Tutorial de SQL Database: creación de una base de datos SQL en cuestión de minutos con datos de ejemplo y el portal de Azure

**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

En este tutorial, aprenderá a usar el Portal de Azure para:

- Crear un servidor lógico de Azure SQL Database para hospedar bases de datos SQL.
- Crear una base de datos SQL sin datos, con datos de ejemplo o con la copia de seguridad de una base de datos SQL.
- Crear una regla de firewall de nivel de servidor para una única dirección IP o un intervalo de direcciones IP.

Puede realizar estas mismas tareas con [C#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Inicio de sesión](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [Creación de un servidor lógico de Base de datos SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Pasos siguientes
Ahora que completó este tutorial de SQL Database y que creó una base de datos con algunos datos de ejemplo, está listo para explorar con sus herramientas favoritas.

- Si está familiarizado con Transact-SQL y SQL Server Management Studio, consulte [Conexión y consulta de una base de datos SQL con SSMS](sql-database-connect-query-ssms.md).

- Si conoce Excel, consulte [Conexión a una base de datos SQL con Excel](sql-database-connect-excel.md).

- Si está listo para comenzar a codificar, elija el lenguaje de programación en [Bibliotecas de conexiones para SQL Database y SQL Server](sql-database-libraries.md).

- Si desea mover las bases de datos de SQL Server locales a Azure, consulte [Migrar una base de datos a SQL Database](sql-database-cloud-migrate.md) para más información.

- Si desea cargar datos en una tabla nueva desde un archivo CSV con la herramienta de línea de comandos BCP, consulte cómo [cargar datos en SQL Database desde un archivo CSV con BCP](sql-database-load-from-csv-with-bcp.md).


## Recursos adicionales

[¿Qué es Base de datos SQL?](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->