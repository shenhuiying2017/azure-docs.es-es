<properties
   pageTitle="Cómo establecer una conexión a una base de datos SQL de Azure mediante SSMS | Microsoft Azure"
   description="Aprenda a establecer una conexión a una base de datos SQL de Azure mediante SSMS."
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jhubbard"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# Conexión con SQL Server Management Studio (SSMS)

Siga los pasos que se indican a continuación para conectarse a la base de datos SQL y consultarla con SQL Server Management Studio (SSMS).

## Requisitos previos

* SQL Server Management Studio (SSMS): para descargar la versión más reciente de SSMS, vea [Descargar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* La base de datos de ejemplo de AdventureWorks, que se describe en [Introducción a Base de datos SQL de Microsoft Azure](sql-database-get-started.md).


## Obtención del nombre completo del servidor de Azure SQL Server

Para conectarse a la base de datos necesita el nombre completo del servidor (****servername**.database.windows.net*) que contiene la base de datos a la que quiere conectarse.

1. Vaya al [Portal de vista previa de Azure](https://portal.azure.com).
2. Vaya a la base de datos a la que quiere conectarse.
3. Busque el nombre completo del servidor:

    ![nombre completo del servidor][6]

    Use el nombre completo del servidor en el paso 3 siguiente.



## Conexión a la Base de datos SQL

1. Abra SSMS.
2. Haga clic en **Conectar** > **Motor de base de datos...**

    ![Conectar > Motor de base de datos][7]

2. En el cuadro de diálogo **Conectar con el servidor**, en el cuadro **Nombre del servidor**, escriba el nombre del servidor en el formato *&lt;nombreDeServidor>*.**database.windows.net**.
3. En la lista **Autenticación**, seleccione **Autenticación de SQL Server**.
4. Escriba el **inicio de sesión** y la **contraseña** que especificó al crear el servidor de Base de datos SQL y haga clic en **Conectar**.

	![Cuadro de diálogo Conectar con el servidor][2]



### Si se produce un error en la conexión
Asegúrese de que el firewall del servidor lógico que ha creado permita conexiones desde su equipo local. Para obtener más información, vea [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).

## Ejecutar consultas de ejemplo

1. En el **Explorador de objetos**, navegue hasta la base de datos de **AdventureWorks**.
2. Haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

	![Nueva consulta][4]

3. En la ventana de consulta, copie y pegue el código siguiente.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Haga clic en el botón **Ejecutar**. En la siguiente captura de pantalla se muestra una consulta correcta.

	![Correcto][5]




## Pasos siguientes
Puede usar instrucciones Transact-SQL para crear o administrar las bases de datos. Para obtener más información, vea [CREATE DATABASE (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn268335.aspx) y [Administración de Base de datos SQL de Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md). También puede registrar los eventos en el almacenamiento de Azure. Vea [Introducción a la auditoría de bases de datos SQL](sql-database-auditing-get-started.md) para obtener más información.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=Oct15_HO3-->