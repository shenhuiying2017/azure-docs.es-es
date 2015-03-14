La galería pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde la galería no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al Portal de administración de Azure. 

En este tutorial, aprenderá a:

- Crear un nuevo sitio mediante la galería.

- Implementar el sitio mediante el Portal de Azure.
 
Va a crear un blog de WordPress que utiliza una plantilla predeterminada. La siguiente ilustración muestra la aplicación completada:


![Wordpress blog][13]

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank").

## Creación de un sitio web en el portal

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. Haga clic en el icono **Nuevo** situado en la parte inferior izquierda del panel.
	
	![Create New][5]

3. Haga clic en el icono **Sitio web** y en **De la galería**.
	
	![Create From Gallery][6]

4. Busque en el icono WordPress en la lista, haga clic en él y, a continuación, haga clic en **Siguiente**.
	
	![WordPress from list][7]

5. En la página **Configurar la aplicación**, escriba o seleccione los valores de todos los campos:
	
- Escriba un nombre de URL de su elección.	
- Deje seleccionado **Crear una nueva base de datos MySQL** en el campo **Base de datos**.
- Seleccione la región más cercana a la suya.

	![configure your app][8]

6. A continuación, haga clic en **Siguiente**.

7. En la página **Crear nueva base de datos**, puede especificar un nombre para su nueva base de datos MySQL o usar el nombre predeterminado. Seleccione la región más cercana a la suya como ubicación del host. Active la casilla situada en la parte inferior de la pantalla para aceptar las condiciones de uso de ClearDB con su base de datos MySQL hospedada. A continuación, haga clic en el signo de verificación para completar la creación del sitio. 
	
	![create database][9]

Después de hacer clic en **Completar**, Azure iniciará las operaciones de compilación e implementación. Durante la compilación y la implementación del sitio web, se muestra el estado de estas operaciones en la parte inferior de la página de sitios web. Una vez realizadas todas las operaciones, se mostrará un mensaje de estado final para informar de que el sitio se ha implementado correctamente.

## Inicio y administración de su sitio WordPress

1. Haga clic en su nuevo sitio desde la página **Sitios web** para abrir el panel del sitio.

	![launch dashboard][10]

2. En la página de administración del **Panel**, desplácese hacia abajo y haga clic en el vínculo situado en el lado izquierdo, bajo **URL de sitio** para abrir la página de bienvenida del sitio.

	![site URL][11] 

3. Escriba la información de configuración apropiada que solicite WordPress y haga clic en **Instalar WordPress** para finalizar la configuración y abrir la página de inicio de sesión del sitio web.

	![login to WordPress][12]

4. Inicie sesión en el nuevo sitio web de WordPress escribiendo el nombre de usuario y la contraseña que ha especificado en la página de **bienvenida**.

5. Tendrá un nuevo sitio WordPress con un aspecto similar al del sitio que se muestra a continuación.  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[11]: ./media/website-from-gallery/wordpressgallery-07.png
[12]: ./media/website-from-gallery/wordpressgallery-08.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png





<!--HONumber=42-->
