<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="Sitio web PHP con MySQL y FTP - Tutorial de Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#Creación de un sitio web Azure de PHP-MySQL e implementación mediante FTP

En este tutorial se muestra cómo crear un sitio web PHP-MySQL de Azure y cómo implementarlo mediante FTP. En este tutorial se presupone que dispone de [PHP][install-php], [MySQL][install-mysql], un servidor web y un cliente FTP instalado en el equipo. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo, incluidos Windows, Mac y Linux. Una vez completada esta guía, tendrá un sitio web PHP/MySQL ejecutándose en Azure.
 
Aprenderá a:

* Creación de un Sitio web Azure y una base de datos MySQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Publicar la aplicación en Azure mediante FTP.
 
Mediante este tutorial, se compilará una aplicación web de registro sencilla en PHP que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación completada:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Creación de un sitio web de Azure y configuración de la publicación FTP

Siga estos pasos para crear un sitio web de Azure y una base de datos MySQL:

1. Inicie sesión en el [Portal de administración de Azure][management-portal].
2. Haga clic en el icono **+ Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure Web Site][new-website]

3. Haga clic en **SITIO WEB** y, a continuación, en **CREACIÓN PERSONALIZADA**.

	![Custom Create a new Web Site][custom-create]
	
	Especifique un valor para **URL**, seleccione **Crear una nueva base de datos MySQL** en la lista desplegable **BASE DE DATOS** y seleccione el centro de datos del sitio web en la lista desplegable **REGIÓN**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in Web Site details][website-details]

4. Escriba un valor para **NOMBRE** para la base de datos, seleccione el centro de datos de esta en la lista desplegable **REGIÓN** y active la casilla que indica que acepta las condiciones legales. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.

	![Create new MySQL database][new-mysql-db]

	Una vez creado el sitio web, se mostrará el texto **La creación del sitio web "[NOMBRE DEL SITIO]" se ha realizado correctamente**. Ahora puede habilitarse la publicación FTP.

5. Haga clic en el nombre del sitio web que se muestra en la lista para abrir el panel **INICIO RÁPIDO** del sitio web.

	![Open web site dashboard][go-to-dashboard]


6. En la parte inferior de la página **INICIO RÁPIDO**, haga clic en **Restablecer credenciales de implementación**. 

	![Reset deployment credentials][reset-deployment-credentials]

7. Para habilitar la publicación FTP, debe proporcionar un nombre de usuario y una contraseña. Tome nota de ambos.

	![Create publishing credentials][portal-git-username-password]

##Compilación y comprobación de la aplicación localmente

Registration es una sencilla aplicación PHP que permite registrarse en un evento con solo proporcionar el nombre y la dirección de correo electrónico. La información de los usuarios inscritos anteriormente se muestra en una tabla. La información de registro se almacena en una base de datos MySQL. La aplicación consta de dos archivos:

* **index.php**: muestra un formulario de registro y una tabla que contiene la información de los usuarios inscritos.
* **createtable.php**: Crea la tabla MySQL para la aplicación. Este archivo solo se usará una vez.

Para compilar y ejecutar la aplicación localmente, realice los pasos siguientes. Tenga en cuenta que en estos pasos se presupone que tiene PHP, MySQL y un servidor web configurado en la máquina local, además de tener habilitada la [extensión PDO para MySQL][pdo-mysql].

1. Cree una base de datos MySQL denominada "registration". Puede realizar este procedimiento desde el símbolo del sistema de MySQL con este comando:

		mysql> create database registration;

2. En el directorio raíz del servidor web, cree una carpeta denominada "registration" y dos archivos: uno llamado "createtable.php" y el otro "index.php".

3. Abra el archivo "createtable.php" en un editor de texto o IDE y agregue el código siguiente. Este código se usará para crear la tabla "registration_tbl" en la base de datos "registration".

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [WACOM.NOTE] 
	> Tendrá que actualizar de nuevo los valores para <code>$user</code> y <code>$pwd</code> con el nombre de usuario y contraseña de MySQL locales.

4. Abra un explorador web y vaya a [http://localhost/registration/createtable.php][localhost-createtable]. De esta forma, se creará la tabla "registration_tbl" en la base de datos.

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

6. En las etiquetas PHP, add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> Tendrá que actualizar de nuevo los valores para <code>$user</code> y <code>$pwd</code> con el nombre de usuario y contraseña de MySQL locales.

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

Ahora puede dirigirse a [http://localhost/registration/index.php][localhost-index] para probar la aplicación.

##Obtención de información de conexión de FTP y MySQL

Para conectarse a la base de datos MySQL que se ejecuta en Sitios web Azure, se requerirá la información de conexión. Si desea obtener la información de conexión de MySQL, siga estos pasos:

1. Desde el panel del sitio web, haga clic en el vínculo **Ver cadenas de conexión** que aparece a la derecha de la página:

	![Get database connection information][connection-string-info]
	
2. Anote los valores de "Base de datos", "Origen de datos", "Id. de usuario" y "Contraseña".

3. Desde el panel del sitio web, haga clic en el vínculo **Descargar perfil de publicación** en la esquina inferior derecha de la página:

	![Download publish profile][download-publish-profile]

4. Abra el archivo ".publishsettings" en un editor XML. 

3. Busque el elemento "<publishProfile >" con `publishMethod="FTP"` que tenga un aspecto similar a este:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Anote los atributos "publishUrl", "userName" y "userPWD".

##Publicar la aplicación

Una vez que se haya probado la aplicación localmente, esta puede publicarse en el sitio web de Azure mediante FTP. Sin embargo, antes es necesario actualizar la información de la conexión a la base de datos en la aplicación. Use la información de la conexión a la base de datos obtenida anteriormente (en la sección **Obtención de información de conexión de FTP y MySQL**), y actualice la siguiente información en los **dos** archivos "createdatabase.php" y "index.php" con los valores adecuados:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Ahora está preparado para publicar la aplicación con FTP.

1. Abra el cliente FTP que desee.

2. Especifique la parte del nombre de host del atributo "publishUrl" que anotó anteriormente en el cliente FTP.

3. Especifique exactamente los mismos atributos "userName" y "userPWD" que anotó anteriormente en el cliente FTP.

4. Establezca una conexión.

Una vez que haya conseguido conectarse, podrá cargar y descargar archivos según sea necesario. Asegúrese de que carga archivos en el directorio raíz, que es "/site/wwwroot".

Cuando haya cargado "index.php" y "createtable.php", vaya a **http://[nombre del sitio].azurewebsites.net/createtable.php** para crear una tabla MySQL para la aplicación y, a continuación, diríjase a **http://[nombre del sitio].azurewebsites.net/index.php** para comenzar a usar la aplicación.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->
