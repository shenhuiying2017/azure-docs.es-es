<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Sitio web PHP con Base de datos SQL y Git - Tutorial de Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Creación de un sitio web PHP con una Base de datos SQL e implementación con Git

En este tutorial se muestra cómo crear un sitio web de Azure PHP con una Base de datos SQL de Azure y cómo implementarlo mediante Git. Este tutorial supone que tiene [PHP][install-php], [SQL Server Express][install-SQLExpress], los [controladores de Microsoft para SQL Server para PHP][install-drivers], un servidor web y Git [Git][install-git] instalado en su equipo. Una vez completada esta guía, tendrá un sitio web PHP-Base de datos SQL que se ejecutará en Azure.

> [WACOM.NOTE]
> Puede instalar y configurar PHP, SQL Server Express, los controladores de Microsoft para SQL Server para PHP e Internet Information Services (IIS) mediante el <a href="http://www.microsoft.com/web/downloads/platform.aspx">instalador de plataforma web de Microsoft</a>.

Aprenderá a:

* Creación de un Sitio web Azure y una base de datos SQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Publicar y volver a publicar la aplicación en Azure con Git.
 
Mediante este tutorial, se compilará una aplicación web de registro sencilla en PHP que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación completada:

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> para completar este tutorial, deberá tener una cuenta de Azure. Puede <a href="http://azure.microsoft.com/es-es/pricing/member-offers/msdn-benefits-details/">activar las ventajas de suscriptor de MSDN</a> o <a href="http://azure.microsoft.com/es-es/pricing/free-trial/">inscribirse para una evaluación gratuita</a>.
> 
> Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Creación de un sitio web de Azure y configuración de la publicación Git

Siga estos pasos para crear un sitio web de Azure y una base de datos SQL:

1. Inicie sesión en el [Portal de administración de Azure][management-portal].
2. Haga clic en el icono **Nuevo**, situado en la parte inferior izquierda del portal.
![Create New Azure Web Site][new-website]

3. Haga clic en **Sitio web** y, a continuación, en **Creación personalizada**.

	![Custom Create a new Web Site][custom-create]

	Especifique un valor para **URL**, seleccione **Crear una nueva base de datos SQL** en el menú desplegable **Database** y seleccione **Publicar desde control de código fuente**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in web site details][website-details-sqlazure]

4. Escriba un valor para el **Nombre** de la base de datos, seleccione **NUEVO servidor de bases de datos SQL**, proporcione las credenciales de inicio de sesión y seleccione una región. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in SQL Database settings][database-settings]

5. Seleccione **Repositorio de Git local** para el código de control de código fuente.

	![where is your source code][where-is-code]

	Si no ha configurado antes un repositorio de Git, debe facilitar un nombre de usuario y una contraseña.

6. Después de haber creado el sitio web, abra el panel del sitio y seleccione **Ver implementaciones**.

	![Web site dashboard][go-to-dashboard]

9. Aparecerán instrucciones para publicar los archivos de aplicación en el repositorio. Tome nota de estas instrucciones, las necesitará más adelante.

	![Git instructions][git-instructions]

##Obtención de información de conexión de la Base de datos SQL

Para conectarse a la instancia de Base de datos SQL que se ejecuta en Sitios web Azure, se requerirá la siguiente información de conexión. Si desea obtener la información de conexión de Base de datos SQL, siga estos pasos:

1. En el Portal de administración de Azure, haga clic en **Recursos vinculados** y, a continuación, haga clic en el nombre de la base de datos.

	![Linked Resources][linked-resources]

2. Haga clic en **Ver cadenas de conexión**.

	![Connection string][connection-string]
	
3. En la sección **PHP** del cuadro de diálogo que aparece, anote los valores de "SERVER", "DATABASE" y "USERNAME".

##Compilación y comprobación de la aplicación localmente

Registration es una sencilla aplicación PHP que permite registrarse en un evento con solo proporcionar el nombre y la dirección de correo electrónico. La información de los usuarios inscritos anteriormente se muestra en una tabla. La información de registro se almacena en una instancia de Base de datos SQL. La aplicación se compone de dos archivos (código para copiar y pegar disponible más abajo):

* **index.php**: muestra un formulario de registro y una tabla que contiene la información de los usuarios inscritos.
* **createtable.php**: Crea la tabla Base de datos SQL para la aplicación. Este archivo solo se usará una vez.

Para ejecutar la aplicación localmente, realice los pasos siguientes. Tenga en cuenta que en estos pasos se presupone que tiene PHP, SQL Server Express y un servidor web configurado en la máquina local, además de tener habilitada la [extensión PDO para SQL Server][pdo-sqlsrv].

1. Cree una base de datos SQL Server denominada "registration". Puede realizar este procedimiento desde el símbolo del sistema "sqlcmd" con estos comandos:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. En el directorio raíz del servidor web, cree una carpeta denominada "registration" y dos archivos: uno llamado "createtable.php" y el otro "index.php".

3. Abra el archivo "createtable.php" en un editor de texto o IDE y agregue el código siguiente. Este código se usará para crear la tabla "registration_tbl" en la base de datos "registration".

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	Tenga en cuenta que tendrá que actualizar los valores de <code>$user</code> y <code>$pwd</code> con el nombre de usuario y la contraseña de SQL Server local.

4. Abra un explorador web y vaya a **http://localhost/registration/createtable.php**. De esta forma, se creará la tabla "registration_tbl" en la base de datos.

5. Abra el archivo **index.php** en un editor de texto o IDE y agregue el código CSS y HTML básicos para la página (el código PHP se agregará en pasos posteriores).

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. En las etiquetas de PHP, agregue el código de PHP para la conexión a la base de datos.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Una vez más, tendrá que actualizar los valores de <code>$user</code> y <code>$pwd</code> con el nombre de usuario y la contraseña de MySQL local.

7. Después del código de conexión a la base de datos, agregue el código para la inserción de información de registro en la base de datos.

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. Finalmente, después del código anterior, agregue el código para recuperar datos de la base de datos.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

Ahora puede dirigirse a **http://localhost/registration/index.php** para probar la aplicación.

##Publicación de la aplicación

Una vez que se haya probado la aplicación localmente, esta puede publicarse en el sitio web de Azure mediante Git. Sin embargo, antes es necesario actualizar la información de la conexión a la base de datos en la aplicación. Use la información de la conexión a la base de datos obtenida anteriormente (en la sección **Obtención de información de conexión de Base de datos SQL** y actualice la siguiente información en los **dos** archivos "createdatabase.php" y "index.php" con los valores adecuados:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> En <code>$host</code>, el valor de SERVIDOR debe ir precedido de <code>tcp:</code>y el valor de <code>$user</code> es la concatenación del valor de NOMBRE DE USUARIO, "@", y el identificador del servidor. El identificador del servidor se corresponde con los 10 primeros caracteres del valor del SERVIDOR.


Ahora, está listo para configurar la publicación de Git y publicar la aplicación.

> [WACOM.NOTE]
> Estos son los mismos pasos que se indican al final de la sección Creación de un sitio web de Azure y configuración de la publicación Git, más arriba.


1. Abra GitBash (o un terminal, si Git está en su "PATH"), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

2. Vaya a **http://[nombre del sitio].azurewebsites.net/createtable.php** para crear la tabla de MySQL para la aplicación.
3. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para comenzar a utilizar la aplicación.

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos. 

##Publicación de cambios de la aplicación

Para publicar los cambios de la aplicación, siga estos pasos:

1. Realice los cambios en la aplicación localmente.
2. Abra GitBash (o un terminal, si Git está en su "PATH"), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

3. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para ver los cambios.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/en-us/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35.1-->
