<properties urlDisplayName="SQL Database" pageTitle="Uso de la base de datos SQL (PHP) - Guías de Azure" metaKeywords ="PHP de Base de datos SQL de Azure, PHP de Base de datos SQL" description="Obtenga información acerca de cómo crear y conectarse a una base de datos SQL de Azure desde PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/29/2014" ms.author="tomfitz" />

#Acceso a una base de datos SQL de Azure desde PHP 

Esta guía le mostrará los aspectos básicos del uso de Base de datos SQL de Azure desde PHP. Los ejemplos están escritos en PHP. Entre los escenarios descritos se incluyen la **creación de una base de datos SQL** y la **conexión a una base de datos SQL**. Abordaremos la creación de una base de datos SQL desde el [Portal de administración][management-portal]. Para obtener información sobre la realización de estas tareas desde el portal de producción, consulte [Getting Started with PHP and SQL Database][prod-portal-instructions]. Para obtener más información, consulte la sección [Pasos siguientes](#NextSteps).

##Qué es una base de datos SQL de Azure

Base de datos SQL de Azure ofrece un sistema de administración de bases de datos relacionales para Azure y se basa en la tecnología de SQL Server. Con Base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de nivel empresarial con las ventajas de contar con funciones integradas de protección de datos y recuperación automática.

##Tabla de contenido

* [Conceptos](#Concepts)
* [Direccionamiento del del entorno](#Setup)
* [Direccionamiento del de una Base de datos SQL](#CreateServer)
* [Direccionamiento del de información de conexión de la Base de datos SQL](#ConnectionInfo)
* [Direccionamiento del a una instancia de Base de datos SQL](#Connect)
* [Pasos siguientes](#NextSteps)

##<a id="Concepts"></a>Conceptos
Puesto que Base de datos SQL de Azure se basa en las tecnologías de SQL Server, el acceso a Base de datos SQL desde PHP es muy similar al acceso de SQL Server desde PHP. Puede desarrollar una aplicación localmente (con SQL Server) y, a continuación, conectarse a Base de datos SQL cambiando solo la cadena de conexión. Sin embargo, existen algunas diferencias entre Base de datos SQL y SQL Server que podrían afectar a su aplicación. Para obtener más información, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure][limitations].

El método recomendado para obtener acceso a Base de datos SQL desde PHP es usar los [controladores de Microsoft para PHP para SQL Server][download-drivers]. Los ejemplos de este artículo usarán estos controladores. Los controladores de Microsoft para PHP para SQL Server solo funcionan en Windows.

##<a id="Setup"></a>Direccionamiento del del entorno

La forma recomendada de configurar el entorno de desarrollo es usar el [instalador de plataforma web de Microsoft][wpi-installer]. El instalador de plataforma web le permitirá seleccionar elementos de la plataforma de desarrollo web e instalarlos y configurarlos automáticamente. Si descarga el instalador de plataforma web y selecciona instalar WebMatrix, PHP para WebMatrix y SQL Server Express, se configurará un entorno de desarrollo completo.

También puede configurar el entorno manualmente:

* Instalación de PHP y configuración de IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Descarga e instalación de SQL Server Express: [http://www.microsoft.com/es-es/download/details.aspx?id=29062][install-sql-express]
* Descarga e instalación de los controladores de Microsoft para PHP para SQL Server: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a>Direccionamiento del de una Base de datos SQL

Siga estos pasos para crear una base de datos SQL de Azure:

1. Inicie sesión en el [Portal de administración][management-portal].
2. haga clic en **Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure Web Site][new-website]

3. haga clic en **SERVICIOS DE DATOS**, **BASE DE DATOS SQL** y en **CREACIÓN RÁPIDA**. Especifique un nombre para la base de datos, si se va a usar un servidor de base de datos existente o uno nuevo, una región y un nombre de administrador y contraseña.

	![Custom Create a new SQL Database][quick-create]


Para ver la información del servidor y la base de datos, haga clic en **Bases de datos SQL** en el Portal de administración. A continuación, puede hacer clic en **BASES DE DATOS** o **SERVIDORES** para ver la información pertinente.

![View server and database information][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Direccionamiento del de información de conexión de la Base de datos SQL

Para obtener información de conexión de Base de datos SQL, haga clic en la opción **BASES DE DATOS SQL** del portal y, después, en el nombre de la base de datos.

![View database information][go-to-db-info]

A continuación, haga clic en **Consultar las cadenas de conexión de las base de datos SQL para ADO.NET, ODBC, PHP y JDBC**.

![Show connection strings][show-connection-string]

En la sección PHP de la ventana que aparece, anote los valores de **SERVIDOR**, **BASE DE DATOS** y **NOMBRE DE USUARIO**. La contraseña será la misma que se utilizó al crear la Base de datos SQL.

##<a id="Connect"></a>Direccionamiento del a una instancia de Base de datos SQL

Los siguientes ejemplos muestran cómo usar las extensiones **SQLSRV** y **PDO_SQLSRV** para conectarse a una Base de datos SQL denominada `testdb`. Necesitará la información obtenida en la sección anterior. Reemplace `SERVER_ID` por el ID de servidor de 10 dígitos (que son los 10 primeros caracteres del valor de SERVIDOR obtenido en la sección anterior) y asigne los valores correctos (nombre de usuario y contraseña) a las variables `$user` y `$pwd`.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	try{
		$conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
		$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
	}
	catch(Exception $e){
		die(print_r($e));
	}


##<a id="NextSteps"></a>Pasos siguientes
Como se ha mencionado anteriormente, el uso de Base de datos SQL es muy parecido al de SQL Server. Una vez que haya establecido una conexión a una Base de datos SQL (como se ha mostrado anteriormente), puede usar las API **SQLSRV** o **PDO\_SQLSRV** para insertar, recuperar, actualizar y eliminar datos. Para obtener información sobre las API **SQLSRV** y **PDO\_SQLSRV**, consulte la [documentación de controladores de Microsoft para PHP para SQL Server][driver-docs]. Sin embargo, existen algunas diferencias entre Base de datos SQL y SQL Server que podrían afectar a su aplicación. Para obtener más información, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure][limitations].

En <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure> hay un ejemplo que indica cómo usar Base de datos SQL con PHP en Azure.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/es-es/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/es-es/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use-sql-database/create-new-sql.png
