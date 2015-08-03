<properties
	pageTitle="Conexión a una base de datos SQL de Azure mediante SSMS"
	description="Aprenda a establecer una conexión a una base de datos SQL de Azure mediante SSMS."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# Conexión con SQL Server Management Studio

Estos son los pasos necesarios para instalar SQL Server Management Studio (SSMS) y usar SSMS para conectarse y consultar la Base de datos SQL.

## Requisitos previos
* Una base de datos de ejemplo de Base de datos SQL de AdventureWorks como se describe en [Introducción a Base de datos SQL de Microsoft Azure](sql-database-get-started.md).

## Instale e inicie SQL Server Management Studio (SSMS)
1. Vaya a la página de descarga de [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299), haga clic en **Descargar** y elija la versión de 32 bits (x 86) o la versión de 64 bits (x 64) de la descarga de MgmtStudio.

	![MgtmtStudio32BIT o MgmtStudio64BIT][1]
2.	Cuando instale SSMS mediante la configuración predeterminada, siga las indicaciones.
3.	Una vez descargada, busque SQL Server 2014 Management Studio en su PC y, a continuación, inicie SSMS.


## Conexión a la base de datos SQL
1. Abra SSMS.
2. En la ventana **Conectar con el servidor**, en el cuadro **Nombre del servidor**, escriba el nombre del servidor en el formato *&lt;nombreDeServidor>*.**database.windows.net**
3. En el cuadro desplegable **Autenticación**, seleccione **Autenticación de SQL Server**.
4. Escriba el **inicio de sesión** y la **contraseña** que especificó cuando creó el servidor de Base de datos SQL.

	![Cuadro de diálogo Conectar con el servidor][2]
5. Haga clic en el botón **Opciones**.
6. En el cuadro **Conectar con base de datos**, escriba **AdventureWorks** y haga clic en **Conectar**.

	![Conectar con la base de datos][3]

### Si se produce un error en la conexión
Asegúrese de que el firewall del servidor lógico que ha creado permita conexiones desde su equipo local. Consulte [Configuración del firewall (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx)

## Ejecutar consultas de ejemplo

1. En el **Explorador de objetos**, navegue hasta la base de datos de **AdventureWorks**.
2. Haga clic con el botón derecho en la base de datos y haga clic en **Nueva consulta**.

	![Nueva consulta][4]
3. En la ventana de consulta recién abierta, copie y pegue el código siguiente:

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. A continuación, haga clic en el botón **Ejecutar**. Si se realiza correctamente, debe verse de la siguiente manera: ![Correcto][5]


## Pasos siguientes
Puede usar instrucciones Transact-SQL para crear o administrar las bases de datos. Vea [CREAR BASE DE DATOS (base de datos SQL DE Azure)](https://msdn.microsoft.com/library/dn268335.aspx) y [Administración de bases de datos SQL de Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md). También puede registrar los eventos en el almacenamiento de Azure. Vea [Introducción a la auditoría de bases de datos SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=July15_HO4-->