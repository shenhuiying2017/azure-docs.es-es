<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="Sitio web PHP con MySQL y Git - Tutorial de Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />

#Creación de un sitio web Azure de PHP-MySQL e implementación mediante Git

En este tutorial se muestra cómo crear un sitio web PHP-MySQL de Azure y cómo implementarlo mediante Git. Usará [PHP][install-php], la herramienta de línea de comandos MySQL (parte de [MySQL][install-mysql]), un servidor web y [Git][install-git] instalados en el equipo. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo, incluidos Windows, Mac y Linux. Una vez completada esta guía, tendrá un sitio web PHP/MySQL ejecutándose en Azure.
 
Aprenderá a:

* Creación de un Sitio web Azure y una base de datos MySQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Publicar y volver a publicar la aplicación en Azure con Git.
 
Mediante este tutorial, se compilará una aplicación web de registro sencilla en PHP Que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación completada:

![Azure PHP web site][running-app]

> [WACOM.NOTE]
> Para completar este tutorial, necesita una cuenta de Azure que tenga habilitada la característica Sitios web Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener información, consulte: <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Evaluación gratuita de Azure</a>.
> 
> Si desea obtener una introducción a Sitios web Azure antes de suscribirse a una cuenta, visite <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Configuración del entorno de desarrollo

En este tutorial se supone que tiene instalados en el equipo [PHP][install-php], la herramienta de línea de comandos MySQL (parte de [MySQL][install-mysql]), un servidor web y Git [Git][install-git].

> [WACOM.NOTE]
> Si el tutorial se está realizando en Windows, puede configurarse la máquina para PHP y configurar IIS (el servidor web integrado en Windows) automáticamente mediante la instalación del <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">SDK de Azure para PHP.</a>.

##<a id="create-web-site-and-set-up-git"></a>Creación de un sitio web de Azure y configuración de la publicación Git

Siga estos pasos para crear un sitio web de Azure y una base de datos MySQL:

1. Inicie sesión en el [Portal de administración de Azure][management-portal].
2. Haga clic en el icono **Nuevo**, situado en la parte inferior izquierda del portal.

	![Create New Azure web site][new-website]

3. Haga clic en **Sitio web** y, a continuación, en **Creación personalizada**.

	![Custom Create a new web site][custom-create]
	
	Especifique un valor para **URL**, seleccione **Crear una nueva base de datos MySQL** en la lista desplegable **Base de datos** y seleccione el centro de datos del sitio web en la lista desplegable **Región**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in web site details][website-details]

4. Escriba un valor para **Nombre** para la base de datos, seleccione el centro de datos de esta en la lista desplegable **Región** y active la casilla que indica que acepta las condiciones legales. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.

	![Create new MySQL database][new-mysql-db]

	Una vez creado el sitio web, se mostrará el texto **La creación del sitio web "[NOMBRE DEL SITIO]" se ha realizado correctamente**. Ahora puede habilitarse la publicación Git.

6. Haga clic en el nombre del sitio web que se muestra en la lista para abrir el panel **Inicio rápido**.

	![Open web site dashboard][go-to-dashboard]


7. En la parte inferior de la página **Inicio rápido**, haga clic en **Configuración de la publicación Git**. 

	![Set up Git publishing][setup-git-publishing]

8. Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Tome nota de ambos. (Si ya ha configurado un repositorio Git anteriormente, este paso se omitirá).

	![Create publishing credentials][credentials]

	Se tardará unos segundos en configurar el repositorio.

9. Una vez listo, aparecerán instrucciones para publicar los archivos de aplicación en el repositorio. Tome nota de estas instrucciones, las necesitará más adelante.

	![Git instructions][git-instructions]

##Obtención de información de la conexión MySQL remota

Para conectarse a la base de datos MySQL que se ejecuta en Sitios web Azure, se requerirá la información de conexión. Si desea obtener la información de conexión de MySQL, siga estos pasos:

1. Desde el panel del sitio web, haga clic en el vínculo **Ver cadenas de conexión** que aparece a la derecha de la página:

	![Get database connection information][connection-string-info]
	
2. Anote los valores de "Base de datos", "Origen de datos", "Id. de usuario" y "Contraseña".

##Compilación y comprobación de la aplicación localmente

Ahora que ha creado un sitio web de Azure, puede desarrollar la aplicación localmente e implementarla a continuación, una vez realizadas las pruebas correspondientes. 

Registration es una sencilla aplicación PHP que permite registrarse en un evento con solo proporcionar el nombre y la dirección de correo electrónico. La información de los usuarios inscritos anteriormente se muestra en una tabla. La información de registro se almacena en una base de datos MySQL. La aplicación se compone de un archivo (código para copiar y pegar disponible más abajo).

* **index.php**: muestra un formulario de registro y una tabla que contiene la información de los usuarios inscritos.

Para compilar y ejecutar la aplicación localmente, realice los pasos siguientes. Tenga en cuenta que en estos pasos se supone que tiene PHP, la herramienta de línea de comandos MySQL (parte de MySQL) y un servidor web configurado en la máquina local, además de tener habilitada la [extensión PDO para MySQL][pdo-mysql].

1. Conéctese al servidor MySQL remoto usando los valores de "Origen de datos", "Id. de usuario", "Contraseña" y "Base de datos" recuperados anteriormente:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Aparecerá el símbolo del sistema MySQL:

		mysql>

3. Pegue el comando "CREATE TABLE" siguiente para crear la tabla "registration_tbl" en la base de datos:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. En el directorio raíz del servidor web, cree una carpeta denominada "registration" y un archivo llamado "index.php".

5. Abra el archivo **index.php** en un editor de texto o entorno de desarrollo integrado y agregue el código siguiente. A continuación, realice los cambios necesarios marcados con comentarios "//TODO:".


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

Ahora puede dirigirse a **http://localhost/registration/index.php** para probar la aplicación.


##Publicación de la aplicación

Una vez que se haya probado la aplicación localmente, esta puede publicarse en el sitio web de Azure mediante Git. Será necesario inicializar el repositorio Git local y publicar la aplicación.

> [WACOM.NOTE]
> Estos son los mismos pasos que se muestran en el portal al final de la sección Creación de un sitio web de Azure y configuración de la publicación Git, más arriba.

1. (Opcional) Si ha olvidado la dirección URL del repositorio Git remoto o no lo encuentra, vaya a la pestaña Implementaciones del portal.
	
	![Get Git URL][git-instructions]

1. Abra GitBash (o un terminal, si Git está en su "PATH"), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

	![Initial Push to Azure via Git][git-initial-push]

2. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para empezar a usar la aplicación (esta información se almacenará en el panel de la cuenta):

	![Azure PHP web site][running-app]

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos. 

##Publicación de cambios de la aplicación

Para publicar los cambios de la aplicación, siga estos pasos:

1. Realice los cambios en la aplicación localmente.
2. Abra GitBash (o un terminal, si Git está en su "PATH"), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Se le solicitará la contraseña que ha creado anteriormente.

	![Pushing site changes to Azure via Git][git-change-push]

3. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para ver la aplicación y cualquier cambio realizado en esta:

	![Azure PHP web site][running-app]

4. También podrá ver la nueva implementación en la pestaña "Implementaciones" del Portal de administración de Azure:

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/en-us/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx

<!--HONumber=35.1-->
