<properties 
	pageTitle="Sitio web PHP con Base de datos SQL y WebMatrix - Azure" 
	description="Un tutorial en el que se muestra cómo utilizar el IDE de WebMatrix gratuito para crear e implementar un sitio web de PHP que almacena datos en la base de datos SQL." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tomfitz"/>





#Creación e implementación de un sitio web PHP y una base de datos SQL con WebMatrix

Este tutorial le muestra cómo usar WebMatrix para desarrollar e implementar una aplicación PHP que usa una base de datos SQL de Azure en un sitio web de Azure. WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo del sitio web. WebMatrix es compatible con PHP e incluye intelliSense para el desarrollo de PHP. 

En este tutorial se presupone que ha instalado [SQL Server Express][install-SQLExpress] en el equipo, por lo que puede realizar una prueba local de la aplicación. Sin embargo, puede completar el tutorial sin tener SQL Server Express instalado. En ese caso, puede implementar la aplicación directamente en Sitios web Azure.

Una vez completada esta guía, tendrá un sitio web PHP-Base de datos SQL que se ejecutará en Azure.
 
Aprenderá a:

* Creación de un Sitio web Azure y una base de datos SQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Desarrollar una aplicación PHP con WebMatrix.
* Publicar y volver a publicar la aplicación en Azure con WebMatrix.
 
Mediante este tutorial, se compilará una aplicación web Tasklist sencilla en PHP Que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación en ejecución:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> para completar este tutorial, deberá tener una cuenta de Azure. Puede <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">activar sus beneficios de suscriptor a MSDN</a> o <a href="http://azure.microsoft.com/pricing/free-trial/">registrarse para obtener una evaluación gratuita</a>.
> 
> Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Requisitos previos

1. [Descargue][tasklist-sqlazure-download] los archivos de la aplicación Tasklist. La aplicación Tasklist es una aplicación PHP simple que le permite agregar, marcar como completo y eliminar los elementos de una lista de tareas. Los elementos de la lista de tareas se almacenan en una base de datos SQL (SQL Server Express para pruebas locales). La aplicación consta de estos archivos:

	* **index.php**: Muestra las tareas y proporciona un formulario para agregar un elemento a la lista.
	* **additem.php**: Agrega un elemento a la lista.
	* **getitems.php**: Obtiene todos los elementos de la base de datos.
	* **markitemcomplete.php**: Cambia el estado de un elemento a completo.
	* **deleteitem.php**: Elimina un elemento.
	* **taskmodel.php**: Contiene funciones que agregan, obtienen, actualizan y eliminan elementos de la base de datos.
	* **createtable.php**: Crea la tabla Base de datos SQL para la aplicación. Se llamará a este archivo solo una vez.

2. Cree una base de datos SQL Server denominada  `tasklist`. Puede realizar este procedimiento desde el símbolo del sistema  `sqlcmd` con estos comandos:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Este paso solo es necesario si desea realizar una prueba local de la aplicación.

## Creación de un sitio web y una base de datos SQL

1. Inicie sesión en el [Portal de administración][preview-portal].
2. Haga clic en el icono **+ Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure Web Site][NewWebSite1]

3. Haga clic en **SITIO WEB** y, a continuación, en **CREACIÓN PERSONALIZADA**.

	![Custom Create a new Web Site][NewWebSite2]

	Especifique un valor para **URL**, seleccione **Crear una nueva base de datos SQL** en la lista desplegable **BASE DE DATOS** y seleccione el centro de datos del sitio web en la lista desplegable **REGIÓN**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in web site details][NewWebSite3_SQL]

4. Especifique un valor para **NOMBRE** en la base de datos y seleccione **Nuevo servidor de bases de datos SQL**. Especifique un nombre de inicio de sesión y una contraseña (y confírmela). Seleccione la región en la que se creará el nuevo servidor de Base de datos SQL.

	![Fill in SQL Database settings][NewWebSite4_SQL]

	Una vez creado el sitio web, se mostrará el texto **Creación correcta del sitio web "[NOMBRE DEL SITIO]"**. A continuación, obtendrá información de conexión de la base de datos.

5. Haga clic en **RECURSOS VINCULADOS** y, a continuación, en el nombre de la base de datos.

	![Linked Resources][NewWebSite6_SQL]

6. Haga clic en **Ver cadenas de conexión**.

	![Connection string][NewWebSite7]
	
En la sección **PHP** del cuadro de diálogo que aparece, anote los valores de  `UID`,  `PWD`,  `Database` y `$serverName`. Usará esa información más adelante.

##Instalar WebMatrix

Puede instalar WebMatrix desde el [Portal de administración][preview-portal]. 

1. Una vez que haya iniciado sesión, diríjase a la página de inicio rápido del sitio web y haga clic en el icono de WebMatrix en la parte inferior de la misma:

	![Install WebMatrix][InstallWebMatrix]

	Siga las indicaciones para instalar WebMatrix.

2. Una vez que se haya instalado WebMatrix, intentará abrir su sitio como proyecto de WebMatrix. Puede seleccionar editar el sitio activo directamente o descargar una copia local. Para este tutorial, seleccione 'Edit local copy'. 

3. Cuando se le solicite descargar el sitio, seleccione **Sí, instalar desde la galería de plantillas**.

	![Download web site][download-site]

4. En las plantillas disponibles, seleccione**PHP**.

	![Site from template][site-from-template]

5. Seleccione la plantilla **Sitio vacío**. Proporcione un nombre para el sitio y haga clic en **SIGUIENTE**.

	![Provide name for site][site-from-template-2]

El sitio se abrirá en WebMatrix con algunos archivos predeterminados.

##Desarrollo de la aplicación

En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

1. Con el sitio abierto en WebMatrix, agregue los archivos de la aplicación haciendo clic en **Agregar existente**:

	![WebMatrix - Add existing files][edit_addexisting]

	En el cuadro de diálogo que aparece, diríjase a los archivos que descargó antes, selecciónelos y haga clic en Open. Cuando se le solicite, elija reemplazar el archivo  `index.php`. 

2. A continuación, debe agregar la información de conexión de la base de datos SQL Server local al archivo  `taskmodel.php`. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función  `connect`. (**Nota**: vaya a [Publicación de la aplicación](#Publish) si no desea probar localmente su aplicación y desea publicar directamente en Sitios web Azure).

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Guarde el `taskmodel.php` archivo.

3. Para que la aplicación se ejecute, tiene que crearse la tabla  `items`. Haga clic con el botón secundario en el archivo  `createtable.php` y seleccione **Iniciar en el explorador**. De esta forma, se iniciará  `createtable.php` en el explorador y se ejecutará el código que crea la tabla  `items` en la base de datos  `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. Ahora puede realizar la prueba de la aplicación localmente. Haga clic con el botón secundario en el archivo  `index.php` y seleccione **Iniciar en el explorador**. Realice la prueba de la aplicación agregando elementos, marcándolos como completos y eliminándolos.   


<h2><a id="Publish"></a>Publicación de la aplicación</h2>

Antes de publicar la aplicación en Sitios web Azure, la información de conexión de la base de datos en  `taskmodel.php` tiene que actualizarse con la información de conexión obtenida anteriormente (en la sección [Creación de un sitio web de Azure y una base de datos SQL](#CreateWebsite) ).

1. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función  `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Haga clic en **Publicar** en WebMatrix y, a continuación, haga clic en **Continuar** en el cuadro de diálogo **Publicar vista previa**.

	![WebMatrix - Publish][edit_publish]

3. Vaya a http://[nombre de su sitio web].azurewebsites.net/createtable.php para crear la tabla `items`.

4. Por último, diríjase a http://[nombre de su sitio web].azurewebsites.net/index.php para iniciar la aplicación.
	
##Modificación y nueva publicación de la aplicación

Puede modificar fácilmente la aplicación si edita la copia local del sitio descargado anteriormente y vuelve a publicarla, o puede realizar la edición directamente en el modo Remote. En este caso, realizará un cambio simple en el título del archivo  `index.php` y lo guardará directamente en el sitio activo.

1. Haga clic en la pestaña Remoto del sitio en WebMatrix y seleccione **Abrir vista remota**. Se abrirá directamente el sitio remoto para la edición.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Abra el archivo `index.php` haciendo doble clic en él.
	![WebMatrix - Open index file][Remote_editIndex]

3. Cambie **Mi lista de tareas pendientes** por **Mi lista de tareas** en las etiquetas **title** y **h1** y guarde el archivo.


4. Cuando el proceso de guardado haya finalizado, haga clic en el botón Run para ver los cambios en el sitio activo.
	![WebMatrix - Launch site in Remote][Remote_run]



## Pasos siguientes

Ha aprendido a crear e implementar un sitio web de WebMatrix en Azure. Para obtener más información sobre WebMatrix, consulte estos recursos:

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sitio web de WebMatrix (en inglés)](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/es-es/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png





















[preview-portal]: https://manage.windowsazure.com












<!--HONumber=42-->
