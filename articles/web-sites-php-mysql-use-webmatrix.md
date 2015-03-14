<properties 
	pageTitle="Sitio web PHP con MySQL y WebMatrix - Tutorial de Azure" 
	description="Un tutorial en el que se muestra cómo utilizar el IDE de WebMatrix gratuito para crear e implementar un sitio web de PHP que almacena datos en MySQL." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>





#Creación e implementación de un sitio web Azure de PHP-MySQL mediante WebMatrix

En este tutorial se muestra cómo usar WebMatrix para desarrollar e implementar una aplicación PHP-MySQL en un sitio web de Azure. WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo del sitio web. WebMatrix es compatible con PHP e incluye intelliSense para el desarrollo de PHP.

En este tutorial se presupone que ha instalado[MySQL][install-mysql] en el equipo para poder probar una aplicación localmente. Sin embargo, es posible completar el tutorial sin tener MySQL instalado. En ese caso, puede implementar la aplicación directamente en Sitios web Azure.

Una vez completada esta guía, tendrá un sitio web PHP-MySQL ejecutándose en Azure.
 
Aprenderá a:

* Creación de un Sitio web Azure y una base de datos MySQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Desarrollar una aplicación PHP con WebMatrix.
* Publicar y volver a publicar la aplicación en Azure con WebMatrix.
 
Mediante este tutorial, se compilará una aplicación web Tasklist sencilla en PHP que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación en ejecución:

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> para completar este tutorial, deberá tener una cuenta de Azure. Puede <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">activar sus beneficios de suscriptor a MSDN</a> o <a href="http://azure.microsoft.com/pricing/free-trial/">registrarse para obtener una evaluación gratuita</a>.
> 
> Si desea obtener una introducción a Sitios web Azure antes de inscribirse para abrir una cuenta, vaya a <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Sitios web Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Requisitos previos

1. [Descargue][tasklist-mysql-download] los archivos de la aplicación Tasklist. La aplicación Tasklist es una aplicación PHP simple que le permite agregar, marcar como completo y eliminar los elementos de una lista de tareas. Los elementos de la lista de tareas se almacenan en una base de datos MySQL. La aplicación consta de estos archivos:

	* **additem.php**: Agrega un elemento a la lista.
	* **createtable.php**: Crea la tabla MySQL para la aplicación. Se llamará a este archivo solo una vez.
	* **deleteitem.php**: Elimina un elemento.
	* **getitems.php**: Obtiene todos los elementos de la base de datos.
	* **index.php**: Muestra las tareas y proporciona un formulario para agregar un elemento a la lista.
	* **markitemcomplete.php**: Cambia el estado de un elemento a completo.
	* **taskmodel.php**: Contiene funciones que agregan, obtienen, actualizan y eliminan elementos de la base de datos.

1. Cree una base de datos MySQL local que se llame  `tasklist`. Puede hacerlo desde el área de trabajo Base de datos en WebMatrix (una vez que se haya instalado más adelante en el tutorial) o desde el símbolo del sistema MySQL con este comando:

		mysql> create database tasklist;

	Este paso solo es necesario si desea realizar una prueba local de la aplicación.

<h2><a id="CreateWebsite"></a>Creación de un sitio web de Azure y base de datos MySQL</h2>

1. Inicie sesión en el [Portal de administración][preview-portal].
1. Haga clic en el icono **+ Nuevo** en la parte inferior izquierda del portal.

	![Create New Azure Web Site][NewWebSite1]

1. Haga clic en **SITIO WEB** y, a continuación, en **CREACIÓN PERSONALIZADA**.

	![Custom Create a new Web Site][NewWebSite2]

	> [AZURE.NOTE]
	> No puede crear una base de datos MySQL para un sitio web después de crear dicho sitio web. Debe crear un sitio web y una base de datos MySQL como se describe en los pasos siguientes.

1. Especifique un valor para **URL**, seleccione **Crear una nueva base de datos MySQL** en la lista desplegable **BASE DE DATOS** y seleccione el centro de datos del sitio web en la lista desplegable **REGIÓN**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

	![Fill in web site details][NewWebSite3]

5. Escriba un valor para **NOMBRE** para la base de datos, seleccione el centro de datos de esta en la lista desplegable **REGIÓN** y active la casilla que indica que acepta las condiciones legales. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.

	![Create new MySQL database][NewWebSite4]

	Una vez creado el sitio web, se mostrará el texto**la creación del sitio web "[NOMBRE DEL SITIO]" se ha realizado correctamente**.

	A continuación, tendrá que obtener la información de conexión de MySQL.


6. Haga clic en el nombre del sitio web que se muestra en la lista de sitios web para abrir la página de inicio rápido del sitio web.

	![Open web site dashboard][NewWebSite5]

7. Haga clic en la pestaña **CONFIGURAR**:

	![Configure tab][NewWebSite6]

8. Desplácese hacia abajo hasta la sección **Cadenas de conexión**. Los valores de  `Database`,  `Data Source`,  `User Id` y  `Password` son (respectivamente) el nombre de la base de datos, el nombre del servidor, el nombre de usuario y la contraseña de usuario. Tome nota de la información de conexión de la base de datos, la necesitará más adelante.

	![Connection string][ConnectionString]

##Instalación de WebMatrix y desarrollo de la aplicación

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

	En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

6. Agregue los archivos de su aplicación haciendo clic en**Agregar existente**:

	![WebMatrix - Add existing files][edit_addexisting]

	En el cuadro de diálogo que aparece, diríjase a los archivos que descargó antes, selecciónelos y haga clic en Open. Cuando se le solicite, elija reemplazar el archivo  `index.php`. 

7. A continuación, debe agregar la información de conexión de la base de datos MySQL local al archivo  `taskmodel.php`. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función  `connect`. (**Nota**: vaya a [Publicación de la aplicación](#Publish) si no desea probar localmente su aplicación y desea publicar directamente en Sitios web Azure).

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Guarde el `taskmodel.php` archivo.

8. Para que la aplicación se ejecute, tiene que crearse la tabla  `items`. Haga clic con el botón secundario en el archivo  `createtable.php` y seleccione **Iniciar en el explorador**. De esta forma, se iniciará  `createtable.php` en el explorador y se ejecutará el código que crea la tabla  `items` en la base de datos  `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. Ahora puede realizar la prueba de la aplicación localmente. Haga clic con el botón secundario en el archivo  `index.php` y seleccione **Iniciar en el explorador**. Realice la prueba de la aplicación agregando elementos, marcándolos como completos y eliminándolos.  


<h2><a id="Publish"></a>Publicación de la aplicación</h2>

Antes de publicar la aplicación en Sitios web Azure, la información de conexión de la base de datos en  `taskmodel.php` tiene que actualizarse con la información de conexión obtenida anteriormente (en la sección [Creación de un sitio web de Azure y una base de datos MySQL](#CreateWebsite) ).

1. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función  `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Haga clic en **Publicar** en WebMatrix y, a continuación, haga clic en **Continuar** en el cuadro de diálogo **Publicar vista previa**.

	![WebMatrix - Publish][edit_publish]

3. Vaya a http://[nombre de su sitio web].azurewebsites.net/createtable.php para crear la tabla `items`.

4. Finalmente, diríjase a http://[nombre de su sitio web].azurewebsites.net/index.php para usar la aplicación.
	
##Modificación y nueva publicación de la aplicación

Puede modificar fácilmente la aplicación si edita la copia local del sitio descargado anteriormente y vuelve a publicarla, o puede realizar la edición directamente en el modo Remote. En este caso, realizará un cambio simple en el título del archivo  `index.php` y lo guardará directamente en el sitio activo.

1. Haga clic en la pestaña Remoto del sitio en WebMatrix y seleccione **Abrir vista remota**. Se abrirá directamente el sitio remoto para la edición.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Abra el archivo `index.php` haciendo doble clic en él.
	![WebMatrix - Open index file][Remote_editIndex]

3. Cambie **Mi lista de tareas pendientes** por **Mi lista de tareas** en las etiquetas **title** y **h1** y guarde el archivo.


4. Cuando el proceso de guardado haya finalizado, haga clic en el botón Run para ver los cambios en el sitio activo.
	![WebMatrix - Launch site in Remote][Remote_run]


# Pasos siguientes

Ha aprendido a crear e implementar un sitio web de WebMatrix en Azure. Para obtener más información sobre WebMatrix, consulte estos recursos:

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sitio web de WebMatrix (en inglés)](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com

















<!--HONumber=42-->
