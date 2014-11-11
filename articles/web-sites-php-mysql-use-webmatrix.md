<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="PHP website with MySQL and WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />





# Creación e implementación de un sitio web Azure de PHP-MySQL mediante WebMatrix

En este tutorial se muestra cómo usar WebMatrix para desarrollar e implementar una aplicación PHP-MySQL en un sitio web de Azure. WebMatrix es una herramienta gratuita de desarrollo web de Microsoft que incluye todo lo que necesita para el desarrollo del sitio web. WebMatrix es compatible con PHP e incluye intelliSense para el desarrollo de PHP.

En este tutorial se presupone que ha instalado [MySQL][MySQL] en el equipo para poder probar una aplicación localmente. Sin embargo, es posible completar el tutorial sin tener MySQL instalado. En ese caso, puede implementar la aplicación directamente en Sitios web Azure.

Una vez completada esta guía, tendrá un sitio web PHP-MySQL ejecutándose en Azure.

Aprenderá a:

-   Creación de un Sitio web Azure y una base de datos MySQL a través del Portal de administración de Azure. Dado que PHP está habilitado en Sitios web Azure de forma predeterminada, no existen requisitos especiales para ejecutar el código PHP.
-   Desarrollar una aplicación PHP con WebMatrix.
-   Publicar y volver a publicar la aplicación en Azure con WebMatrix.

Mediante este tutorial, se compilará una aplicación web Tasklist sencilla en PHP que se hospedará en un sitio web de Azure. A continuación se muestra una captura de pantalla de la aplicación en ejecución:

![Sitio web PHP de Azure][Sitio web PHP de Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Requisitos previos

1.  [Descargue][Descargue] los archivos de la aplicación Tasklist. La aplicación Tasklist es una aplicación PHP simple que le permite agregar, marcar como completo y eliminar los elementos de una lista de tareas. Los elementos de la lista de tareas se almacenan en una base de datos MySQL. La aplicación consta de estos archivos:

    -   **additem.php**: Agrega un elemento a la lista.
    -   **createtable.php**: Crea la tabla MySQL para la aplicación. Se llamará a este archivo solo una vez.
    -   **deleteitem.php**: Elimina un elemento.
    -   **getitems.php**: Obtiene todos los elementos de la base de datos.
    -   **index.php**: Muestra las tareas y proporciona un formulario para agregar un elemento a la lista.
    -   **markitemcomplete.php**: Cambia el estado de un elemento a completo.
    -   **taskmodel.php**: Contiene funciones que agregan, obtienen, actualizan y eliminan elementos de la base de datos.

2.  Cree una base de datos MySQL local que se llame `tasklist`. Puede hacerlo desde el área de trabajo Base de datos en WebMatrix (una vez que se haya instalado más adelante en el tutorial) o desde el símbolo del sistema MySQL con este comando:

        mysql> create database tasklist;

    Este paso solo es necesario si desea realizar una prueba local de la aplicación.

## <span id="CreateWebsite"></span></a>Creación de un sitio web de Azure y base de datos MySQL

1.  Inicie sesión en el [Portal de administración][Portal de administración].
2.  Haga clic en el icono **+ New**, situado en la parte inferior izquierda del portal.

    ![Crear un sitio web de Azure][Crear un sitio web de Azure]

3.  Haga clic en **SITIO WEB** y, a continuación, en **CREACIÓN PERSONALIZADA**.

    ![Crear un sitio web personalizado][Crear un sitio web personalizado]

    > [WACOM.NOTE]
    > No puede crear una base de datos MySQL para un sitio web después de crear dicho sitio web. Debe crear un sitio web y una base de datos MySQL como se describe en los pasos siguientes.

4.  Especifique un valor para **URL**, seleccione **Crear una nueva base de datos MySQL** en la lista desplegable **BASE DE DATOS** y seleccione el centro de datos del sitio web en la lista desplegable **REGIÓN**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo.

    ![Rellenar detalles del sitio web][Rellenar detalles del sitio web]

5.  Escriba un valor para **NAME** para la base de datos, seleccione el centro de datos de esta en la lista desplegable **REGION** y active la casilla que indica que acepta las condiciones legales. Haga clic en la marca de verificación de la parte inferior del cuadro de diálogo.

    ![Crear una base de datos MySQL][Crear una base de datos MySQL]

    Una vez creado el sitio web, se mostrará el texto **Creación correcta del sitio web [NOMBRE DEL SITIO]**.

    A continuación, tendrá que obtener la información de conexión de MySQL.

6.  Haga clic en el nombre del sitio web que se muestra en la lista de sitios web para abrir la página de inicio rápido del sitio web.

    ![Abrir el panel del sitio web][Abrir el panel del sitio web]

7.  Haga clic en la pestaña **CONFIGURAR**:

    ![Configure tab][Configure tab]

8.  Desplácese a la sección **cadenas de conexión**. Los valores de `Database`, `Data Source`, `User Id` y `Password` son (respectivamente) el nombre de la base de datos, el nombre del servidor, el nombre de usuario y la contraseña de usuario. Tome nota de la información de conexión de la base de datos, la necesitará más adelante.

    ![Cadena de conexión][Cadena de conexión]

## Instalación de WebMatrix y desarrollo de la aplicación

Puede instalar WebMatrix desde el [Portal de administración][Portal de administración].

1.  Una vez que haya iniciado sesión, diríjase a la página de inicio rápido del sitio web y haga clic en el icono de WebMatrix en la parte inferior de la misma:

    ![Instalar WebMatrix][Instalar WebMatrix]

    Siga las indicaciones para instalar WebMatrix.

2.  Una vez que se haya instalado WebMatrix, intentará abrir su sitio como proyecto de WebMatrix. Puede seleccionar editar el sitio activo directamente o descargar una copia local. Para este tutorial, seleccione la opción de edición de una copia local.

3.  Cuando se le solicite descargar el sitio, seleccione **Yes, install from the Template Gallery**.

    ![Descargar el sitio web][Descargar el sitio web]

4.  En las plantillas disponibles, seleccione **PHP**.

    ![Sitio desde la plantilla][Sitio desde la plantilla]

5.  Seleccione la plantilla **Empty Site**. Proporcione un nombre para el sitio y haga clic en **NEXT**.

    ![Proporcionar un nombre para el sitio][Proporcionar un nombre para el sitio]

    El sitio se abrirá en WebMatrix con algunos archivos predeterminados.

    En los siguientes pasos desarrollará la aplicación Tasklist mediante la adición de archivos que descargó anteriormente y la realización de algunas modificaciones. Sin embargo, puede agregar sus propios archivos existentes o crear archivos nuevos.

6.  Agregue los archivos de su aplicación haciendo clic en **Agregar existente**:

    ![WebMatrix: agregar archivos existentes][WebMatrix: agregar archivos existentes]

    En el cuadro de diálogo que aparece, diríjase a los archivos que descargó antes, selecciónelos y haga clic en Open. Cuando se le solicite, elija reemplazar el archivo `index.php`.

7.  A continuación, debe agregar la información de conexión de la base de datos MySQL local al archivo `taskmodel.php`. Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función `connect`. (**Nota**: diríjase a [Publicación de la aplicación][Publicación de la aplicación] si no desea probar localmente su aplicación y desea publicar directamente en Sitios web Azure).

        // DB connection info
        $host = "localhost";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Guarde el archivo `taskmodel.php`.

8.  Para que la aplicación se ejecute, tiene que crearse la tabla `items`. Haga clic con el botón secundario en el archivo `createtable.php` y seleccione **Iniciar en el explorador**. De esta forma, se iniciará `createtable.php` en el explorador y se ejecutará el código que crea la tabla `items` en la base de datos `tasklist`.

    ![WebMatrix: inicio de createtable.php en el explorador][WebMatrix: inicio de createtable.php en el explorador]

9.  Ahora puede realizar la prueba de la aplicación localmente. Haga clic con el botón secundario en el archivo `index.php` y seleccione **Iniciar en el explorador**. Realice la prueba de la aplicación agregando elementos, marcándolos como completos y eliminándolos.

## <span id="Publish"></span></a>Publicación de la aplicación

Antes de publicar la aplicación en Sitios web Azure, la información de conexión de la base de datos en `taskmodel.php` tiene que actualizarse con la información de conexión obtenida anteriormente (en la sección [Creación de un sitio web de Azure y una base de datos MySQL][Creación de un sitio web de Azure y una base de datos MySQL]).

1.  Abra el archivo `taskmodel.php` haciendo doble clic en él y actualice la información de conexión de la base de datos en la función `connect`.

        // DB connection info
        $host = "value of Data Source";
        $user = "value of User Id";
        $pwd = "value of Password";
        $db = "value of Database";

    Guarde el archivo `taskmodel.php`.

2.  Haga clic en **Publish** en WebMatrix y, a continuación, haga clic en **Continue** en el cuadro de diálogo **Publish Preview**.

    ![WebMatrix: publicar][WebMatrix: publicar]

3.  Diríjase a http://[nombre de su sitio web].azurewebsites.net/createtable.php para crear la tabla `items`.

4.  Finalmente, diríjase a http://[nombre de su sitio web].azurewebsites.net/index.php para iniciar la aplicación.

## Modificación y nueva publicación de la aplicación

Puede modificar fácilmente la aplicación si edita la copia local del sitio descargado anteriormente y vuelve a publicarla, o puede realizar la edición directamente en el modo Remote. En este caso, realizará un cambio simple en el título del archivo `index.php` y lo guardará directamente en el sitio activo.

1.  Haga clic en la pestaña Remote del sitio en WebMatrix y seleccione **Open Remote View**. Se abrirá directamente el sitio remoto para la edición.
     ![WebMatrix: abrir la vista remota][WebMatrix: abrir la vista remota]

2.  Abra el archivo `index.php`; para ello, haga doble clic en él.
    ![WebMatrix - Open index file][WebMatrix - Open index file]

3.  Cambie **My ToDo List** por **My Task List** en las etiquetas **title** y **h1**, y guarde el archivo.

4.  Cuando el proceso de guardado haya finalizado, haga clic en el botón Run para ver los cambios en el sitio activo.
    ![WebMatrix - Launch site in Remote][WebMatrix - Launch site in Remote]

# Pasos siguientes

Ha aprendido a crear e implementar un sitio web de WebMatrix en Azure. Para obtener más información sobre WebMatrix, consulte estos recursos:

-   [WebMatrix for Azure][WebMatrix for Azure]

-   [Sitio web de WebMatrix (en inglés)][Sitio web de WebMatrix (en inglés)]

  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Sitio web PHP de Azure]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Descargue]: http://go.microsoft.com/fwlink/?LinkId=252506
  [Portal de administración]: https://manage.windowsazure.com
  [Crear un sitio web de Azure]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
  [Crear un sitio web personalizado]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
  [Rellenar detalles del sitio web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
  [Crear una base de datos MySQL]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
  [Abrir el panel del sitio web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
  [Configure tab]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
  [Cadena de conexión]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
  [Instalar WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
  [Descargar el sitio web]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
  [Sitio desde la plantilla]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
  [Proporcionar un nombre para el sitio]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
  [WebMatrix: agregar archivos existentes]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
  [Publicación de la aplicación]: #Publish
  [WebMatrix: inicio de createtable.php en el explorador]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
  [Creación de un sitio web de Azure y una base de datos MySQL]: #CreateWebsite
  [WebMatrix: publicar]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
  [WebMatrix: abrir la vista remota]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
  [WebMatrix - Open index file]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
  [WebMatrix - Launch site in Remote]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png
  [WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Sitio web de WebMatrix (en inglés)]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
