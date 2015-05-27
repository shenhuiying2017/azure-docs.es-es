<properties 
	pageTitle="Creación e implementación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure mediante WebMatrix" 
	description="Un tutorial en el que se muestra cómo utilizar el IDE de WebMatrix gratuito para crear e implementar una aplicación web PHP en el Servicio de aplicaciones de Azure para almacenar datos en Base de datos SQL." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>





# Creación e implementación de una aplicación web PHP-SQL en el Servicio de aplicaciones de Azure mediante WebMatrix

Este tutorial muestra cómo usar WebMatrix para desarrollar una aplicación PHP que usa una base de datos SQL de Azure y cómo implementarla en Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo de aplicaciones web. WebMatrix es compatible con PHP e incluye intelliSense para el desarrollo de PHP.

En este tutorial se presupone que ha instalado [SQL Server Express][install-SQLExpress] en el equipo, por lo que puede realizar una prueba local de la aplicación. Sin embargo, puede completar el tutorial sin tener SQL Server Express instalado. En ese caso, puede implementar la aplicación directamente en Aplicaciones web del Servicio de Aplicaciones de Azure.

Una vez completada esta guía, tendrá una aplicación web de base de datos PHP-SQL que se ejecutará en Azure.
 
Aprenderá a:

* Crear una aplicación web en Aplicaciones web del Servicio de aplicaciones y una base de datos SQL con el [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Dado que PHP está habilitado en aplicaciones web de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Desarrollar una aplicación PHP con WebMatrix.
* Publicar y volver a publicar la aplicación en Azure con WebMatrix.
 
Mediante este tutorial, se compilará una aplicación web Tasklist sencilla en PHP. La aplicación se hospedará en Aplicaciones web del Servicio de aplicaciones. A continuación se muestra una captura de pantalla de la aplicación en ejecución:

![Sitio web PHP de Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Requisitos previos

1. [Descargue][tasklist-sqlazure-download] los archivos de la aplicación Tasklist. La aplicación Tasklist es una aplicación PHP simple que le permite agregar, marcar como completo y eliminar los elementos de una lista de tareas. Los elementos de la lista de tareas se almacenan en una base de datos SQL (SQL Server Express para pruebas locales). La aplicación consta de estos archivos:

	* **index.php**: muestra las tareas y proporciona un formulario para agregar un elemento a la lista.
	* **additem.php**: agrega un elemento a la lista.
	* **getitems.php**: obtiene todos los elementos de la base de datos.
	* **markitemcomplete.php**: cambia el estado de un elemento a completo.
	* **deleteitem.php**: elimina un elemento.
	* **taskmodel.php**: contiene funciones que agregan, obtienen, actualizan y eliminan elementos de la base de datos.
	* **createtable.php**: crea la tabla de Base de datos SQL para la aplicación. Se llamará a este archivo solo una vez.

2. Cree una base de datos de SQL Server denominada `tasklist`. Puede realizar este procedimiento desde el símbolo del sistema `sqlcmd` con estos comandos:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Este paso solo es necesario si desea realizar una prueba local de la aplicación.

## Creación de una aplicación web y una base de datos SQL

Siga estos pasos para crear una aplicación web de Azure y una base de datos SQL:

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com).

2. Abra Azure Marketplace haciendo clic en el icono **Marketplace** o en el icono **Nuevo** situado en la parte inferior izquierda del panel, elija **Web + móvil** y, a continuación, **Azure Marketplace** en la parte inferior.
	
3. En Marketplace, elija **Aplicaciones web**.

4. Haga clic en el icono **Aplicación web + SQL**.

5. Después de leer la descripción de la aplicación web + SQL, elija **Crear**.

6. Haga clic en cada parte (**Grupo de recursos**, **Aplicación web**, **Base de datos** y **Suscripción**) y escriba o elija los valores para los campos obligatorios:
	
	- Escriba un nombre de URL de su elección.	
	- Configuración de credenciales de servidor de bases de datos
	- Seleccione la región más cercana a la suya.

	![configurar su aplicación](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. Cuando termine de definir la aplicación web, haga clic en **Crear**.

	Una vez creada la aplicación web, el botón **Notificaciones** emitirá el mensaje **Correcto** en color verde y la hoja del grupo de recursos se abrirá para mostrar la aplicación web y la base de datos SQL en el grupo.

4. Haga clic en el icono de la hoja del grupo de recursos para abrirla.

	![Grupo de recursos de la aplicación web](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##información de conexión de la Base de datos SQL

Para conectarse a la instancia de Base de datos SQL vinculada a la aplicación web, necesitará la información de conexión que especificó al crear la base de datos. Para obtener la información de conexión de Base de datos SQL, siga estos pasos:

1. En la hoja del grupo de recursos, haga clic en el icono de Base de datos SQL.

2. En la hoja de Base de datos SQL, haga clic en **Propiedades** y en **Mostrar cadenas de conexión de la base de datos**.

	![Ver las propiedades de una base de datos](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. En la sección **PHP** del cuadro de diálogo que aparece, anote los valores de `Server`, `SQL Database` y `User Name`. Usará estos valores más adelante al publicar la aplicación web PHP en el Servicio de aplicaciones de Azure.

## Creación de aplicaciones en WebMatrix

En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

1. Inicie [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Si no la ha instalado todavía, hágalo ahora.
2. Si esta es la primera vez que utiliza WebMatrix 3, se le solicitará que inicie sesión en Azure. De lo contrario, puede hacer clic en el botón **Sign In** (Inicio de sesión) y elegir **Add Account** (Agregar cuenta). Elija **Sign in** (Inicio de sesión) con su cuenta Microsoft.

	![Add account](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:

	![Inicio de sesión en Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. En la pantalla de inicio, haga clic en el botón **New** (Nuevo) y elija **Template Gallery (Galería de plantillas)** para crear un sitio nuevo a partir de la Galería de plantillas:

	![Sitio nuevo desde la galería de plantillas](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. En las plantillas disponibles, elija **PHP**.

	![Sitio desde la plantilla][site-from-template]

5. Elija la plantilla **Empty Site** (Sitio vacío). Proporcione un nombre para el sitio y haga clic en **Next** (Siguiente).

	![Proporcionar un nombre para el sitio][site-from-template-2]

3. Si inició sesión en Azure, ahora tiene la opción de crear una instancia de Aplicaciones web del Sitio de aplicaciones para su sitio local. Elija **Skip** (Omitir) por ahora.

	![Creación de sitio en Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. Cuando WebMatrix finaliza la creación del sitio local, se muestra el IDE de WebMatrix. Agregue los archivos de su aplicación haciendo clic en **Add Existing** (Agregar existente):

	![WebMatrix: agregar archivos existentes][edit_addexisting]

	En el cuadro de diálogo que aparece, diríjase a los archivos que descargó antes, selecciónelos y haga clic en Open. Cuando se le solicite, elija reemplazar el archivo `index.php`.

2. A continuación, debe agregar la información de conexión de la base de datos SQL Server local al archivo `taskmodel.php`. Abra el archivo `taskmodel.php` haciendo doble clic en él. Actualice la información de conexión de la base de datos en la función `connect`. **Nota:** acceda a [Publish Your Application](#Publish) (Publicar su aplicación) si no desea probar su aplicación localmente, sino publicarla directamente en Aplicaciones de web de servicio de aplicación de Azure.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Guarde el archivo `taskmodel.php`.

3. Para que la aplicación se ejecute, tiene que crear la tabla `items`. Haga clic con el botón secundario en el `createtable.php`archivo y elija **Iniciar en el explorador**. De esta forma, se iniciará `createtable.php` en el explorador y se ejecutará el código que crea la tabla `items` en la base de datos `tasklist`.

	![WebMatrix: inicio de createtable.php en el explorador][edit_run]

4. Ahora puede realizar la prueba de la aplicación localmente. Haga clic con el botón secundario en el archivo `index.php` y elija **Iniciar en el explorador**. Realice la prueba de la aplicación agregando elementos, marcándolos como completos y eliminándolos.

<a id="Publish"></a>
## Publicación de la aplicación

Antes de publicar la aplicación en Aplicaciones web del Servicio de aplicaciones, la información de conexión de la base de datos en `taskmodel.php` tiene que actualizarse con la información de conexión obtenida anteriormente (en la sección [Creación de un sitio web de Azure y una base de datos SQL](#CreateWebsite)).

1. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	Guarde el archivo `taskmodel.php`.

2. Haga clic en **Publish** (Publicar) en WebMatrix.

	![WebMatrix: publicar][edit_publish]

3. Haga clic en **Elegir un sitio existente de Microsoft Azure**.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. Elija la aplicación web del Servicio de aplicaciones que creó anteriormente.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Continúe haciendo clic en **Continue** (Continuar) hasta que WebMatrix publique el sitio en Aplicaciones web del Servicio de aplicaciones de Azure.

3. Acceda al sitio web http://[your name].azurewebsites.net/createtable.php para crear la tabla `items`.

4. Por último, vaya a http://[your web site name].azurewebsites.net/index.php para iniciar la aplicación.
	
##Modificación y nueva publicación de la aplicación

Puede modificar fácilmente la aplicación si edita la copia local del sitio y vuelve a publicarla, o puede realizar la edición directamente en el modo **remoto**. En este caso, realizará un cambio simple en el título del archivo `index.php` y lo guardará directamente en la aplicación web del Servicio de aplicaciones.

1. Haga clic en la pestaña **Remote** (Remoto) del sitio en WebMatrix y elija **Open Remote View** (Abrir vista remota). Se abrirán los archivos remotos (que se hospedan en Aplicaciones Web) para editarlos directamente. ![WebMatrix: abrir la vista remota][OpenRemoteView]
 
2. Abra el archivo `index.php` haciendo doble clic en él.![WebMatrix: abrir el archivo de índice][Remote_editIndex]

3. Cambie **My ToDo List** (Mi lista de tareas pendientes) por **My Task List** (Mi lista de tareas) en las etiquetas **title** y **h1** y guarde el archivo.


4. Cuando el proceso de guardado haya finalizado, haga clic en el botón Run (Ejecutar) para ver en vivo los cambios en la aplicación web del Servicio de aplicaciones.![WebMatrix: inicio del sitio en modo remoto][Remote_run]



## Pasos siguientes

Ha aprendido a crear e implementar una aplicación web desde WebMatrix en Aplicaciones web del Servicio de aplicaciones de Azure. Para obtener más información acerca de WebMatrix, visite el [sitio web de WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal al de vista previa, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
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









<!--HONumber=54-->