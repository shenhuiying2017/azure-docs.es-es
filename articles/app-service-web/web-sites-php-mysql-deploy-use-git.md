---
title: "Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git"
description: "Tutorial en el que se muestra cómo crear una aplicación web PHP que almacena datos en MySQL y usar la implementación de Git en Azure."
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
tags: mysql
ms.assetid: 7454475f-e275-4bc7-9f09-1ef07382e5da
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1b1da346a253443c0b5eaf6f8a9a5a399923e760


---
# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Creación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git
En este tutorial se muestra cómo crear una aplicación web PHP-MySQL y cómo implementarla en el [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) mediante Git. Usará [PHP][install-php], la herramienta de línea de comandos MySQL (parte de [MySQL][install-mysql]) y [Git][install-git] instalados en el equipo. Las instrucciones de este tutorial se pueden seguir en cualquier sistema operativo, incluidos Windows, Mac y Linux. Una vez completada esta guía, tendrá una aplicación web PHP/MySQL que se ejecutará en Azure.

Aprenderá a:

* Crear una aplicación web y una base de datos MySQL con [Azure Portal][management-portal]. Dado que PHP está habilitado en [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Publicar y volver a publicar la aplicación en Azure con Git.
* Habilitar la extensión de Composer para automatizar las tareas de Composer en cada `git push`.

Mediante este tutorial, se compilará una aplicación web de registro sencilla en PHP. La aplicación se hospedará en aplicaciones web. A continuación se muestra una captura de pantalla de la aplicación completada:

![Sitio web PHP de Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configuración del entorno de desarrollo
En este tutorial se supone que tiene [PHP][install-php], la herramienta de línea de comandos MySQL (parte de [MySQL][install-mysql]) y [Git][install-git] instalados en el equipo.

<a id="create-web-site-and-set-up-git"></a>

## <a name="create-a-web-app-and-set-up-git-publishing"></a>Creación de una aplicación web y configuración de la publicación Git
Siga estos pasos para crear una aplicación web y una base de datos MySQL:

1. Inicie sesión en [Azure Portal][management-portal].
2. Haga clic en el icono **Nuevo** .
3. Haga clic en **Ver todo** junto a **Marketplace**. 
4. Haga clic en **Web + móvil** y, a continuación, en **Aplicación web + MySQL**. A continuación, haga clic en **Crear**.
5. Escriba un nombre válido para el grupo de recursos.
   
    ![Establecer nombre de grupo de recurso][resource-group]
6. Escriba valores para la nueva aplicación web.
   
    ![Crear una aplicación web][new-web-app]
7. Especifique los valores para la nueva base de datos, incluida la aceptación de los términos legales.
   
    ![Crear una base de datos MySQL][new-mysql-db]
8. Una vez creada la aplicación web, verá la nueva hoja de aplicación web.
9. En **Configuración**, haga clic en **Implementación continua** y, a continuación, en *Configurar los valores obligatorios*.
   
    ![Configuración de la publicación Git][setup-publishing]
10. Seleccione **Repositorio de Git local** para el origen.
    
     ![Configuración de un repositorio Git][setup-repository]
11. Para habilitar la publicación Git, debe proporcionar un nombre de usuario y una contraseña. Tome nota de ambos. (Si ya ha configurado un repositorio Git anteriormente, este paso se omitirá).
    
     ![Creación de credenciales de publicación][credentials]

## <a name="get-remote-mysql-connection-information"></a>Obtención de información de la conexión MySQL remota
Para conectarse a la base de datos MySQL que se ejecuta en aplicaciones web, necesita información de conexión. Si desea obtener la información de conexión de MySQL, siga estos pasos:

1. En el grupo de recursos, haga clic en la base de datos:
   
    ![Seleccionar base de datos][select-database]
2. En la **Configuración** de la base de datos, seleccione **Propiedades**.
   
    ![Seleccionar propiedades][select-properties]
3. Anote los valores de `Database`, `Host`, `User Id` y `Password`.
   
    ![Anotar propiedades][note-properties]

## <a name="build-and-test-your-app-locally"></a>Compilación y prueba local de la aplicación
Ahora que ha creado una aplicación web, puede desarrollarla localmente e implementarla después de las pruebas.

Registration es una sencilla aplicación PHP que permite registrarse en un evento con solo proporcionar el nombre y la dirección de correo electrónico. La información de los usuarios inscritos anteriormente se muestra en una tabla. La información de registro se almacena en una base de datos MySQL. La aplicación se compone de un archivo (código para copiar y pegar disponible más abajo).

* **index.php**: muestra un formulario de registro y una tabla que contiene la información de los usuarios inscritos.

Para compilar y ejecutar la aplicación localmente, realice los pasos siguientes. Tenga en cuenta que en estos pasos se supone que tiene PHP y la herramienta de línea de comandos MySQL (parte de MySQL) configurados en la máquina local, además de tener habilitada la [extensión PDO para MySQL][pdo-mysql].

1. Conéctese al servidor MySQL remoto usando los valores de `Data Source`, `User Id`, `Password` y `Database` recuperados anteriormente:
   
        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]
2. Aparecerá el símbolo del sistema MySQL:
   
        mysql>
3. Pegue el comando siguiente `CREATE TABLE` para crear la tabla `registration_tbl` en la base de datos:
   
        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);
4. En la raíz de la carpeta de la aplicación local, cree el archivo **index.php** .
5. Abra el archivo **index.php** en un editor de texto o entorno de desarrollo integrado y agregue el código siguiente. A continuación, realice los cambios necesarios marcados con comentarios `//TODO:`.

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
            //using the values you retrieved earlier from the Azure Portal.
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

1. En un terminal, vaya a la carpeta de la aplicación y escriba el siguiente comando:
   
       php -S localhost:8000

Ahora puede ir a **http://localhost:8000/** para probar la aplicación.

## <a name="publish-your-app"></a>Publicación de la aplicación
Una vez que se haya probado la aplicación localmente, esta puede publicarse en Aplicaciones web mediante Git. Será necesario inicializar el repositorio Git local y publicar la aplicación.

> [!NOTE]
> Estos son los mismos pasos que se muestran en el Portal de Azure al final de la sección Creación de una aplicación web y configuración de la publicación Git, más arriba.
> 
> 

1. (Opcional) Si ha olvidado la dirección URL del repositorio de Git remoto o no la encuentra, vaya a las propiedades de la aplicación web de Azure Portal.
2. Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    Se le solicitará la contraseña que ha creado anteriormente.
   
    ![Inserción inicial en Azure a través de Git][git-initial-push]
3. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para empezar a usar la aplicación (esta información se almacenará en el panel de la cuenta):
   
    ![Sitio web PHP de Azure][running-app]

Una vez publicada la aplicación, podrá comenzar a realizar cambios en esta y a usar Git para publicarlos.

## <a name="publish-changes-to-your-app"></a>Publicación de cambios de la aplicación
Para publicar los cambios de la aplicación, siga estos pasos:

1. Realice los cambios en la aplicación localmente.
2. Abra GitBash (o un terminal, si Git está en su `PATH`), cambie los directorios al directorio raíz de la aplicación y ejecute los siguientes comandos:
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    Se le solicitará la contraseña que ha creado anteriormente.
   
    ![Inserción de cambios del sitio en Azure a través de Git][git-change-push]
3. Vaya a **http://[nombre del sitio].azurewebsites.net/index.php** para ver la aplicación y cualquier cambio realizado en esta:
   
    ![Sitio web PHP de Azure][running-app]

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

<a name="composer"></a>

## <a name="enable-composer-automation-with-the-composer-extension"></a>Habilitación de la automatización de Composer con la extensión Composer
De forma predeterminada, el proceso de implementación de git en el Servicio de aplicaciones no responde con composer.json, si tiene uno en el proyecto PHP. Puede habilitar el procesamiento de composer.json durante `git push` si habilita la extensión Composer.

1. En la hoja de la aplicación web de PHP, en [Azure Portal][management-portal], haga clic en **Herramientas** > **Extensiones**.
   
    ![Configuración de la extensión Compositor][composer-extension-settings]
2. Haga clic en **Agregar** y, luego, en **Compositor**.
   
    ![Adición de la extensión Compositor][composer-extension-add]
3. Haga clic en **Aceptar** para aceptar las condiciones legales. Haga clic de nuevo en **Aceptar** para agregar la extensión.
   
    La hoja **Extensiones instaladas** mostrará ahora la extensión Compositor.  
    ![Vista de la extensión Compositor][composer-extension-view]
4. Ahora, ejecute `git add`, `git commit` y `git push` como en la sección anterior. Verá que Composer está instalando las dependencias definidas en composer.json.
   
    ![Éxito de la extensión Compositor][composer-extension-success]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png



<!--HONumber=Jan17_HO3-->


