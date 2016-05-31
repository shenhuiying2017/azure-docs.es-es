<properties
	pageTitle="Conexión a Base de datos SQL - SQL Server Management Studio | Microsoft Azure"
	description="Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). Después, ejecute una consulta de ejemplo con Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="conexión a base de datos sql,sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/09/2016"
	ms.author="sstein;carlrab" />

# Conexión a Base de datos SQL con SQL Server Management Studio y ejecución de una consulta T-SQL de ejemplo

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

En este artículo se muestra cómo conectarse a una base de datos SQL de Azure con SQL Server Management Studio (SSMS) y realizar una consulta simple con instrucciones Transact-SQL (T-SQL).

[AZURE.INCLUDE [Iniciar sesión](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Instalación de SSMS](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [Conexión SSMS](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Para más información sobre las reglas de firewall, consulte [Configuración del firewall en la Base de datos SQL mediante el Portal de Azure](sql-database-configure-firewall-settings.md).

## Ejecutar consultas de ejemplo

Después de conectarse al servidor lógico, puede conectarse a una base de datos y ejecutar una consulta de ejemplo.

1. En el **Explorador de objetos**, vaya a una base de datos en el servidor para el que quiere tener permiso, por ejemplo, la base de datos de ejemplo **AdventureWorks**.
2. Haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

	![Nueva consulta. Conexión a servidor de Base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. En la ventana de consulta, copie y pegue el código siguiente.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Haga clic en el botón **Ejecutar**. En la siguiente captura de pantalla se muestra una consulta correcta.

	![Correcta. Conexión a servidor de Base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Pasos siguientes

Puede usar instrucciones T-SQL para crear y administrar bases de datos en Azure casi de la misma manera que con SQL Server. Si está familiarizado con el uso de T-SQL con SQL Server, consulte [Información sobre Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) para obtener un resumen de las diferencias.

Si no está familiarizado con T-SQL, consulte [Tutorial: Escribir instrucciones Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) y [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx).

Para comenzar con la creación de usuarios de base de datos y administradores de usuarios de base de datos, consulte [SQL Database tutorial: Create SQL database user accounts to access and manage a database using the Azure portal](sql-database-get-started-security.md) (Tutorial de Base de datos SQL: Creación de cuentas de base de datos SQL para acceder a una base de datos y administrarla mediante el Portal de Azure).

<!---HONumber=AcomDC_0525_2016-->