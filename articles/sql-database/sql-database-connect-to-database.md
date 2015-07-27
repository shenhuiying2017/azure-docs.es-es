<properties
	pageTitle="Conexión a una base de datos SQL de Azure mediante SSMS" metaKeywords=""
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
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Conexión a una base de datos SQL de Azure con SQL Server Management Studio

A continuación se describen los pasos necesarios para conectarse a una base de datos SQL de Microsoft Azure con SQL Server Management Studio (SSMS).

## Requisitos previos
* Una base de datos SQL de Azure SQL aprovisionada y en ejecución. Para crear una nueva base de datos SQL, vea [Introducción a la base de datos SQL de Microsoft Azure](sql-database-get-started.md).
* El nombre y la contraseña de administrador de la base de datos SQL.
* SQL Server Management Studio 2014. Para obtener la herramienta, vea [Descargar SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx).
* Definir la configuración del firewall para la base de datos. Vea [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md)

## Para conectarse a una instancia de base de datos SQL
1. Inicie sesión en el [Portal de administración de Azure](https://portal.azure.com).
2. Haga clic en el botón **Examinar** y, a continuación, haga clic en **Bases de datos SQL** (b).

	![Haga clic en Examinar y en Base de datos SQL.][1]
3. Con la opción **Bases de datos SQL** seleccionada (a), haga clic en el nombre de una base de datos en el servidor al que desea conectarse (b).

	![Haga clic en el nombre de una base de datos.][2]
4. Con el nombre seleccionado (a), haga clic en Propiedades (b). Copie el nombre del servidor (c) y el nombre del administrador (d). El nombre y la contraseña de administrador se crean al crear la base de datos de SQL. Debe tener la contraseña para poder continuar con el paso siguiente.

	![Haga clic en SQL Server, Configuración y Propiedades.][3]
5. Abra SQL Server Management Studio 2014.
6. En el cuadro de diálogo Conectar al servidor, pegue el nombre del servidor en el cuadro **Nombre del servidor** (a). Establezca la autenticación en **Autenticación de SQL Server** (b). Pegue el nombre del administrador del servidor en el cuadro **Inicio de sesión** (c) y, a continuación, escriba su contraseña (d). A continuación, haga clic en **Opciones** (e).

	![Cuadro de diálogo de inicio de sesión en SSMS.][4]
7. En la pestaña Propiedades de conexión, establezca el cuadro **Conectar con base de datos** como **Maestro** (o como cualquier otra base de datos a la que desee conectarse). A continuación, haga clic en **Conectar**.

	![Establezca el valor como maestro y haga clic en Conectar.][5]

## Solución de problemas de conexión

En caso de problemas, vea [Solucionar problemas de conexión con la base de datos SQL de Azure](https://support.microsoft.com/kb/2980233/). Para obtener una lista de posibles problemas y respuestas, vea [Solución de problemas de conectividad con la base de datos SQL de Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).


## Pasos siguientes
Puede usar instrucciones Transact-SQL para crear o administrar las bases de datos. Vea [CREAR BASE DE DATOS (base de datos SQL DE Azure)](https://msdn.microsoft.com/library/dn268335.aspx) y [Administración de bases de datos SQL de Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md). También puede registrar los eventos en el almacenamiento de Azure. Vea [Introducción a la auditoría de bases de datos SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->