<properties
	pageTitle="Tutorial de Base de datos SQL: Introducción a la seguridad"
	description="Aprenda a crear cuentas de usuario para acceder a una base de datos y administrarla."
	keywords=""
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
	ms.date="07/19/2016"
	ms.author="carlrab"/>

# SQL Database tutorial: Create SQL database user accounts to access and manage a database using the Azure portal (Tutorial de Base de datos SQL: Creación de cuentas de base de datos SQL para acceder a una base de datos y administrarla mediante el Portal de Azure)


> [AZURE.SELECTOR]
- [Tutorial introductorio](sql-database-get-started-security.md)
- [Conceder acceso](sql-database-manage-logins.md)

En este tutorial, aprenderá a usar el Portal de Azure para:

- Iniciar sesión en Base de datos SQL mediante un inicio de sesión de entidad de seguridad de nivel de servidor
- Crear una cuenta de usuario de Base de datos SQL
- Conceder a una cuenta de usuario de Base de datos SQL permisos de dbo dentro de una base de datos de usuario
- Conectarse a una base de datos SQL con una cuenta de usuario que no sea una entidad de seguridad de nivel de servidor

[AZURE.INCLUDE [Inicio de sesión](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Creación de un servidor lógico de Base de datos SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Creación de bases de datos SQL](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Pasos siguientes
Ahora que ha completado este tutorial de Base de datos SQL, ha creado una cuenta de usuario y ha concedido a la cuenta de usuario permisos de dbo, está preparado para aprender más sobre la [seguridad de Base de datos SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0803_2016-->