<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP web site with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP web site with a SQL Database and deploy using Git" authors="waltpo" solutions="" manager="" editor="mollybos" />

Creación de un sitio web PHP con una Base de datos SQL e implementación con Git
===============================================================================

En este tutorial se muestra cómo crear un sitio web de Azure PHP con una Base de datos SQL de Azure y cómo implementarlo mediante Git. Este tutorial supone que tiene [PHP](http://www.php.net/manual/en/install.php), [SQL Server Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062), los [controladores de Microsoft para SQL Server para PHP](http://www.microsoft.com/en-us/download/details.aspx?id=20098) (en inglés), un servidor web y [Git](http://git-scm.com/) instalado en su equipo. Una vez completada esta guía, tendrá un sitio web PHP-Base de datos SQL que se ejecutará en Azure.

> [WACOM.NOTE] Puede instalar y configurar PHP, SQL Server Express, los controladores de Microsoft para SQL Server para PHP e Internet Information Services (IIS) mediante el [instalador de plataforma web de Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).

Aprenderá a:

-   Crear un sitio web de Azure y una base de datos SQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
-   Publicar y volver a publicar la aplicación en Azure con Git.

Mediante este tutorial, se compilará una aplicación web de registro sencilla en PHP que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación completada:

![Sitio web PHP de Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creación de un sitio web de Azure y configuración de la publicación Git
-----------------------------------------------------------------------

Siga estos pasos para crear un sitio web de Azure y una base de datos SQL:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en el icono **New**, situado en la parte inferior izquierda del portal.
	
	![Crear un sitio web de Azure](./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg)

3.  Haga clic en **Web Site** y, a continuación, en **Custom Create**.

    ![Crear un sitio web personalizado](./media/web-sites-php-sql-database-deploy-use-git/custom_create.png)

    Especifique un valor para **URL**, seleccione **Create a New SQL Database** en la lista desplegable **Database** y seleccione el centro de datos del sitio web en la lista desplegable **Region**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

    ![Rellenar detalles del sitio web](./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg)

4.  Escriba el valor **NAME** de su base de datos, seleccione el tipo de **Edition** [(WEB o BUSINESS)](http://msdn.microsoft.com/es-es/library/windowsazure/ee621788.aspx), el valor **Max Size** de la base de datos, la clase **Collation** y, a continuación, seleccione **NEW SQL Database server**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

    ![Rellenar configuración de la base de datos SQL](./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg)

5.  Especifique un nombre y una contraseña de administrador (y confirme la contraseña), elija la región donde se creará el nuevo servidor de base de datos SQL y active la casilla `Allow Azure Services to access the server`.

    ![Crear servidor de base de datos SQL](./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg)

    Una vez creado el sitio web, se mostrará el texto **Creation of Web Site "[NOMBRESITIO]" completed successfully**. Ahora puede habilitarse la publicación Git.

6.  Haga clic en el nombre del sitio web que se muestra en la lista para abrir el panel QuickStart del sitio web.

    ![Abrir el panel del sitio web](./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png)

7.  En la parte inferior de la página Quick Start, haga clic en **Set up deployment from source control**.

    ![Configuración de la publicación Git](./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png)

8.  Cuando se le pregunte "Where is your source code?" (¿Dónde está su código fuente?), seleccione **Local Git repository** y, a continuación, haga clic en la flecha.

    ![Dónde está su código fuente](./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png)

9.  Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Tome nota de ambos. (Si ya ha configurado un repositorio Git anteriormente, este paso se omitirá).

    ![Crear credenciales de publicación](./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png)

    Se tardará unos segundos en configurar el repositorio.

10. Una vez listo, aparecerán instrucciones para publicar los archivos de aplicación en el repositorio. Tome nota de estas instrucciones, las necesitará más adelante.

    ![Instrucciones de Git](./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png)

Obtención de información de conexión de la Base de datos SQL
------------------------------------------------------------

Para conectarse a la instancia de Base de datos SQL que se ejecuta en Sitios web Azure, se requerirá la siguiente información de conexión. Si desea obtener la información de conexión de Base de datos SQL, siga estos pasos:

1.  En el Portal de administración de Azure, haga clic en **Linked Resources** y, a continuación, en el nombre de la base de datos.

    ![Recursos vinculados](./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg)

2.  Haga clic en **View connection strings**.

    ![Cadena de conexión](./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg)

3.  En la sección **PHP** del cuadro de diálogo que aparece, anote los valores de `SERVER`, `DATABASE` y `USERNAME`.

Compilación y comprobación de la aplicación localmente
------------------------------------------------------

Registration es una sencilla aplicación PHP que permite registrarse en un evento con solo proporcionar el nombre y la dirección de correo electrónico. La información de los usuarios inscritos anteriormente se muestra en una tabla. La información de registro se almacena en una instancia de Base de datos SQL. La aplicación se compone de dos archivos (código para copiar y pegar disponible más abajo):

-   **index.php**: muestra un formulario de registro y una tabla que contiene la información de los usuarios inscritos.
-   **createtable.php**: Crea la tabla Base de datos SQL para la aplicación. Este archivo solo se usará una vez.

Para ejecutar la aplicación localmente, realice los pasos siguientes. Tenga en cuenta que en estos pasos se presupone que tiene PHP, SQL Server Express y un servidor web configurado en la máquina local, además de tener habilitada la [extensión PDO para SQL Server](http://php.net/pdo_sqlsrv).

1.  Cree una base de datos SQL Server denominada `registration`. Puede realizar este procedimiento desde el símbolo del sistema `sqlcmd` con estos comandos:

         >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
         1> create database registration
         2> GO   

2.  En el directorio raíz del servidor web, cree una carpeta denominada `registration` y dos archivos: uno llamado `createtable.php` y el otro `index.php`.

3.  Abra el archivo `createtable.php` en un editor de texto o IDE y agregue el código siguiente. Este código se usará para crear la tabla `registration_tbl` en la base de datos `registration`.

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

    Tendrá que actualizar los valores para `$user` y `$pwd` con el nombre de usuario y contraseña de SQL Server locales.

4.  Abra un explorador web y diríjase a **http://localhost/registration/createtable.php**. De esta forma, se creará la tabla `registration_tbl` en la base de datos.

5.  Abra el archivo **index.php** en un editor de texto o IDE y agregue el código CSS y HTML básicos para la página (el código PHP se agregará en pasos posteriores).

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
         <h1>Regístrese aquí</h1>
         <p>Rellene su nombre y dirección de correo electrónico y, a continuación, haga clic en <strong>Submit</strong> para registrarse.</p>
         <form method="post" action="index.php" enctype="multipart/form-data" >
               Nombre  <input type="text" name="name" id="name"/></br>
               Correo electrónico <input type="text" name="email" id="email"/></br>
               <input type="submit" name="submit" value="Submit" />
         </form>
         <
         php

         
         >
         </body>
         </html>

6.  En las etiquetas de PHP, agregue el código de PHP para la conexión a la base de datos.

         // Información de conexión de BD
         $host = "host local\sqlexpress";
         $user = "nombre de usuario";
         $pwd = "contraseña";
         $db = "registro";
         // Conectarse a la base de datos.
         try {
             $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
         }
         catch(Exception $e){
             die(var_dump($e));
         }

    Tendrá que actualizar de nuevo los valores para `$user` y `$pwd` con el nombre de usuario y contraseña de MySQL locales.

7.  Después del código de conexión a la base de datos, agregue el código para la inserción de información de registro en la base de datos.

         if(!empty($_POST)) {
         try {
             $name = $_POST['name'];
             $email = $_POST['email'];
             $date = date("Y-m-d");
             // Inserte los datos.
             $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                            VALUES (
         ,
         ,
         )";
             $stmt = $conn->prepare($sql_insert);
             $stmt->bindValue(1, $name);
             $stmt->bindValue(2, $email);
             $stmt->bindValue(3, $date);
             $stmt->execute();
         }
         catch(Exception $e){
             die(var_dump($e));
         }
         echo "<h3>Está registrado</h3>";
         }

8.  Finalmente, después del código anterior, agregue el código para recuperar datos de la base de datos.

         $sql_select = "SELECT * FROM registration_tbl";
         $stmt = $conn->query($sql_select);
         $registrants = $stmt->fetchAll(); 
         if(count($registrants) > 0) {
             echo "<h2>Personas registradas:</h2>";
             echo "<table>";
             echo "<tr><th>Nombre</th>";
             echo "<th>Correo electrónico</th>";
             echo "<th>Fecha</th></tr>";
             foreach($registrants as $registrant) {
                 echo "<tr><td>".$registrant['name']."</td>";
                 echo "<td>".$registrant['email']."</td>";
                 echo "<td>".$registrant['fecha']."</td></tr>";
             }
            echo "</table>";
         } else {
             echo "<h3>No hay nadie registrado actualmente.</h3>";
         }

Ahora puede dirigirse a **http://localhost/registration/index.php** para probar la aplicación.

Publicación de la aplicación
----------------------------

Una vez que se haya probado la aplicación localmente, esta puede publicarse en el sitio web de Azure mediante Git. Sin embargo, antes es necesario actualizar la información de la conexión a la base de datos en la aplicación. Use la información de la conexión a la base de datos obtenida anteriormente (en la sección **Obtención de información de conexión de Base de datos SQL**) y actualice la siguiente información en los **dos** archivos (`createdatabase.php` e `index.php`) con los valores adecuados:

    // Información de conexión de BD
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE] En `$host`, el valor SERVER se debe anteceder con `tcp:` y el valor `$user` es la concatenación del valor USERNAME, "@" y su identificador de servidor. Su identificador de servidor está compuesto por los primeros 10 caracteres del valor SERVER.

Ahora, está listo para configurar la publicación de Git y publicar la aplicación.

> [WACOM.NOTE] Estos son los mismos pasos que se indican al final de la sección Creación de un sitio web de Azure y configuración de la publicación Git, más arriba.

1.  Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [URL del repositorio remoto]
         git push azure master

    Se le solicitará la contraseña que ha creado anteriormente.

2.  Vaya a **http://[site name].azurewebsites.net/createtable.php** para crear la tabla de MySQL para la aplicación.
3.  Vaya a **http://[site name].azurewebsites.net/index.php** para comenzar a usar la aplicación.

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos.

Publicación de cambios de la aplicación
---------------------------------------

Para publicar los cambios de la aplicación, siga estos pasos:

1.  Realice los cambios en la aplicación localmente.
2.  Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:

         git add .
         git commit -m "comentario que describe los cambios"
         git push azure master

    Se le solicitará la contraseña que ha creado anteriormente.

3.  Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para ver los cambios.

