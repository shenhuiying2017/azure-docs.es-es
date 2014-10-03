La galería pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde la galería no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al Portal de administración de Azure.

En este tutorial, aprenderá a:

-   Crear un nuevo sitio mediante la galería.

-   Implementar el sitio mediante el Portal de Azure.

Va a crear un blog de WordPress que utiliza una plantilla predeterminada. La siguiente ilustración muestra la aplicación completada:

![Blog de WordPress][]

<div class="dev-callout"><strong>Nota:</strong>
<p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. Puede crear una cuenta de evaluaci&oacute;n gratuita en pocos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Creaci&oacute;n de una cuenta de Azure</a>.</p>
</div>

## Creación de un sitio web en el portal

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  Haga clic en el icono **New**, situado en la parte inferior izquierda del panel.

    ![Crear nuevo][]

3.  Haga clic en el icono **Sitio web** y, a continuación, haga clic en **De la galería**.

    ![Crear desde la galería][]

4.  Busque en el icono WordPress en la lista, haga clic en él y, a continuación, haga clic en **Next**.

    ![WordPress desde la lista][]

5.  En la página **Configure Your App**, escriba o seleccione los valores de todos los campos:

-   Escriba un nombre de URL de su elección.
-   Deje seleccionado **Create a new MySQL database** en el campo **Base de datos**.
-   Seleccione la región más cercana a la suya.

    ![configurar su aplicación][]

1.  A continuación, haga clic en **Siguiente**.

2.  En la página **Crear nueva base de datos**, puede especificar un nombre para su nueva base de datos MySQL o usar el nombre predeterminado. Seleccione la región más cercana a la suya como ubicación del host. Active la casilla situada en la parte inferior de la pantalla para aceptar las condiciones de uso de ClearDB con su base de datos MySQL hospedada. A continuación, haga clic en el signo de verificación para completar la creación del sitio.

    ![crear base de datos][]

Después de hacer clic en **Completar**, Azure iniciará las operaciones de compilación e implementación. Durante la compilación y la implementación del sitio web, se muestra el estado de estas operaciones en la parte inferior de la página de sitios web. Una vez realizadas todas las operaciones, se mostrará un mensaje de estado final para informar de que el sitio se ha implementado correctamente.

## Inicio y administración de su sitio WordPress

1.  Haga clic en su nuevo sitio desde la página **Sitios web** para abrir el panel del sitio.

    ![panel de inicio][]

2.  En la página de administración del **Panel**, desplácese hacia abajo y haga clic en el vínculo situado en el lado izquierdo, bajo **URL del sitio**, para abrir la página principal del sitio.

    ![URL del sitio][]

3.  Escriba la información de configuración apropiada que solicite WordPress y haga clic en **Install WordPress** para finalizar la configuración y abrir la página de inicio de sesión del sitio web.

    ![inicio de sesión en WordPress][]

4.  Inicie sesión en el nuevo sitio web de WordPress escribiendo el nombre de usuario y la contraseña que ha especificado en la página de **bienvenida**.

5.  Tendrá un nuevo sitio WordPress con un aspecto similar al del sitio que se muestra a continuación.

    ![su sitio de WordPress][Blog de WordPress]

  [Blog de WordPress]: ./media/website-from-gallery/wordpressgallery-09.png
  [Creación de una cuenta de Azure]: http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Crear nuevo]: ./media/website-from-gallery/wordpressgallery-01.png
  [Crear desde la galería]: ./media/website-from-gallery/wordpressgallery-02.png
  [WordPress desde la lista]: ./media/website-from-gallery/wordpressgallery-03.png
  [configurar su aplicación]: ./media/website-from-gallery/wordpressgallery-04.png
  [crear base de datos]: ./media/website-from-gallery/wordpressgallery-05.png
  [panel de inicio]: ./media/website-from-gallery/wordpressgallery-06.png
  [URL del sitio]: ./media/website-from-gallery/wordpressgallery-07.png
  [inicio de sesión en WordPress]: ./media/website-from-gallery/wordpressgallery-08.png
