<properties 
	pageTitle="Creación e implementación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure mediante WebMatrix" 
	description="Un tutorial en el que se muestra cómo utilizar el IDE de WebMatrix gratuito para crear e implementar una aplicación web PHP en el Servicio de aplicaciones de Azure que almacene datos en MySQL."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="tomfitz"/>





# Creación e implementación de una aplicación web PHP-MySQL en el Servicio de aplicaciones de Azure mediante WebMatrix

En este tutorial se muestra cómo usar WebMatrix para desarrollar e implementar una aplicación PHP-MySQL para Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo del sitio web. WebMatrix es compatible con PHP e incluye intelliSense para el desarrollo de PHP.

En este tutorial, se presupone que ha instalado [MySQL][install-mysql] en el equipo para poder probar una aplicación localmente. Sin embargo, es posible completar el tutorial sin tener MySQL instalado. En ese caso, puede implementar la aplicación directamente en Aplicaciones web del Servicio de Aplicaciones de Azure.

Una vez completada esta guía, tendrá un sitio web PHP-MySQL ejecutándose en Aplicaciones web.
 
Aprenderá a:

* Crear un sitio web en Aplicaciones web del Servicio de aplicaciones y una base de datos MySQL con el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Dado que PHP está habilitado en aplicaciones web de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
* Desarrollar una aplicación PHP con WebMatrix.
* Publicar y volver a publicar la aplicación en Aplicaciones web con WebMatrix.
 
Mediante este tutorial, se compilará una aplicación web Tasklist sencilla en PHP. La aplicación se hospedará en Aplicaciones web del Servicio de aplicaciones. A continuación se muestra una captura de pantalla de la aplicación en ejecución:

![Sitio web PHP de Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Requisitos previos

1. [Descargue][tasklist-mysql-download] los archivos de la aplicación Tasklist. La aplicación Tasklist es una aplicación PHP simple que le permite agregar, marcar como completo y eliminar los elementos de una lista de tareas. Los elementos de la lista de tareas se almacenan en una base de datos MySQL. La aplicación consta de estos archivos:

	* **additem.php**: agrega un elemento a la lista.
	* **createtable.php**: crea la tabla MySQL para la aplicación. Se llamará a este archivo solo una vez.
	* **deleteitem.php**: elimina un elemento.
	* **getitems.php**: obtiene todos los elementos de la base de datos.
	* **index.php**: muestra las tareas y proporciona un formulario para agregar un elemento a la lista.
	* **markitemcomplete.php**: cambia el estado de un elemento a "completo".
	* **taskmodel.php**: contiene funciones que agregan, obtienen, actualizan y eliminan elementos de la base de datos.

1. Cree una base de datos MySQL local que se llame `tasklist`. Puede hacerlo desde el área de trabajo Base de datos en WebMatrix (una vez que se haya instalado más adelante en el tutorial) o desde el símbolo del sistema MySQL con este comando:

		mysql> create database tasklist;

	Este paso solo es necesario si desea realizar una prueba local de la aplicación.

## Creación de una aplicación web y una base de datos MySQL

Siga estos pasos para crear una aplicación web y una base de datos MySQL:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Haga clic en el icono **Nuevo** situado en la parte inferior izquierda del portal.

	![Crear un sitio web de Azure](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Haga clic en **Web + móvil** y, a continuación, en **Aplicación web + MySQL**.

	![Crear un sitio web personalizado](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Escriba un nombre válido para el grupo de recursos.

    ![Definición de un nombre de grupo de recursos](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Escriba valores para la nueva aplicación web.

    ![Creación de una aplicación web](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Especifique los valores para la nueva base de datos, incluida la aceptación de los términos legales.

	![Crear una base de datos MySQL](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Una vez creada la aplicación web, verá el nuevo grupo de recursos.

## Obtención de información de la conexión MySQL remota

Para conectarse a la base de datos MySQL que se ejecuta en aplicaciones web, necesita información de conexión. Si desea obtener la información de conexión de MySQL, siga estos pasos:

1. En el grupo de recursos, haga clic en la base de datos:

	![Selección de una base de datos](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. En el resumen de base de datos, elija **Propiedades**.

    ![Selección de propiedades](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Anote los valores de `Database`, `Host`, `User Id` y `Password`.

    ![Propiedades de las notas](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Creación de aplicaciones en WebMatrix

En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

1. Inicie [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Si no la ha instalado todavía, hágalo ahora.
2. Si esta es la primera vez que utiliza WebMatrix 3, se le solicitará que inicie sesión en Azure. De lo contrario, puede hacer clic en el botón **Sign In** (Inicio de sesión) y elegir **Add Account** (Agregar cuenta). Elija **Sign in** (Inicio de sesión) con su cuenta Microsoft.

	![Add account](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Si se registró para obtener una cuenta de Azure, puede iniciar sesión con su cuenta de Microsoft:

	![Inicio de sesión en Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. En la pantalla de inicio, haga clic en el botón **New** (Nuevo) y elija **Template Gallery (Galería de plantillas)** para crear un sitio nuevo a partir de la Galería de plantillas:

	![Sitio nuevo desde la galería de plantillas](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. En las plantillas disponibles, elija **PHP**.

	![Sitio desde la plantilla][site-from-template]

5. Elija la plantilla **Empty Site** (Sitio vacío). Proporcione un nombre para el sitio y haga clic en **Next** (Siguiente).

	![Proporcionar un nombre para el sitio][site-from-template-2]

	El sitio se abrirá en WebMatrix con algunos archivos predeterminados.

	En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

6. Agregue los archivos de su aplicación haciendo clic en **Add Existing** (Agregar existente):

	![WebMatrix: agregar archivos existentes][edit_addexisting]

	En el cuadro de diálogo que aparece, diríjase a los archivos que descargó antes, selecciónelos y haga clic en Open. Cuando se le solicite, elija reemplazar el archivo `index.php`.

7. A continuación, debe agregar la información de conexión de la base de datos MySQL local al archivo `taskmodel.php`. Abra el archivo `taskmodel.php` haciendo doble clic en él. Actualice la información de conexión de la base de datos en la función `connect`. **Nota:** acceda a [Publish Your Application](#Publish) (Publicar su aplicación) si no desea probar su aplicación localmente, pero sí desea publicarla directamente en Aplicaciones de web de servicio de aplicación de Azure.

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Guarde el archivo `taskmodel.php`.

8. Para que la aplicación se ejecute, tiene que crearse la tabla `items`. Haga clic con el botón secundario en el archivo `createtable.php` y elija **Iniciar en el explorador**. De esta forma, se iniciará `createtable.php` en el explorador y se ejecutará el código que crea la tabla `items` en la base de datos `tasklist`.

	![WebMatrix: inicio de createtable.php en el explorador][edit_run]

9. Ahora puede realizar la prueba de la aplicación localmente. Haga clic con el botón secundario en el archivo `index.php` y elija **Iniciar en el explorador**. Realice la prueba de la aplicación agregando elementos, marcándolos como completos y eliminándolos.


## Publicación de la aplicación

Antes de publicar la aplicación en Aplicaciones web del Servicio de aplicaciones, la información de conexión de la base de datos en `taskmodel.php` tiene que actualizarse con la información de conexión obtenida anteriormente (en la sección [Creación de un sitio web de Azure y una base de datos MySQL](#CreateWebsite)).

1. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Guarde el archivo `taskmodel.php`.

2. Haga clic en **Publish** (Publicar) en WebMatrix.

	![WebMatrix: publicar][edit_publish]

3. Haga clic en **Choose an existing site from Windows Azure** (Elegir un sitio existente de Windows Azure).

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. Elija la aplicación web del Servicio de aplicaciones que creó anteriormente.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Continúe haciendo clic en **Continue** (Continuar) hasta que WebMatrix publique el sitio en Aplicaciones web del Servicio de aplicaciones de Azure.

3. Acceda al sitio web http://[your name].azurewebsites.net/createtable.php para crear la tabla `items`.

4. Por último, vaya al sitio web http://[your name].azurewebsites.net/index.php para usar la aplicación.
	
##Modificación y nueva publicación de la aplicación

Puede modificar fácilmente la aplicación si edita la copia local del sitio descargado anteriormente y vuelve a publicarla, o puede realizar la edición directamente en el modo Remote. En este caso, realizará un cambio simple en el título del archivo `index.php` y lo guardará directamente en el sitio activo.

1. Haga clic en la pestaña Remote (Remoto<9 del sitio en WebMatrix y elija **Open Remote View** (Abrir vista remota). Se abrirá el sitio remoto para editarlo directamente. ![WebMatrix: abrir la vista remota][OpenRemoteView]
 
2. Abra el archivo `index.php` haciendo doble clic en él. ![WebMatrix: abrir el archivo de índice][Remote_editIndex]

3. Cambie **My ToDo List** (Mi lista de tareas pendientes) por **My Task List** (Mi lista de tareas) en las etiquetas **title** y **h1** y guarde el archivo.


4. Cuando el proceso de guardado haya finalizado, haga clic en el botón Run (Ejecutar) para ver los cambios en el sitio activo. ![WebMatrix: inicio del sitio en modo remoto][Remote_run]


## Pasos siguientes

* [Sitio web de WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)




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














<!--HONumber=54-->