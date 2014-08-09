<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Web Site from Gallery" pageTitle="Create an Orchard CMS web site from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new web site in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS web site from the gallery in Azure" authors="" solutions="" manager="" editor="" />

Creación de un sitio web de Orchard CMS desde la galería de Azure
=================================================================

La galería pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde la galería no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al [Portal de administración de Azure](http://manage.windowsazure.com). Para obtener más información sobre las aplicaciones web de la galería, consulte [Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx).

En este tutorial, aprenderá a:

-   Crear un nuevo sitio desde la galería

-   Iniciar y administrar el sitio desde el Portal de administración

Va a crear un sitio Orchard CMS que utiliza una plantilla predeterminada. [Orchard](http://www.orchardproject.net/) es una aplicación CMS, basada en .NET, gratuita y de código abierto que le permite crear sitios web personalizados controlados por contenido. Orchard CMS incluye un marco de extensibilidad mediante el cual puede [descargar módulos y temas adicionales](http://gallery.orchardproject.net/) para personalizar el sitio. La ilustración siguiente muestra el sitio de Orchard que va a crear.

![Blog de Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creación de un sitio web de Orchard desde la galería
----------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  Haga clic en el icono **New**, situado en la parte inferior izquierda del portal.

    ![Crear nuevo](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png)

3.  Haga clic en el icono **Sitio web** y, a continuación, haga clic en **From Gallery**.

    ![Crear desde la galería](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png)

4.  Busque en el icono **Orchard CMS** en la lista, haga clic en él y, a continuación, haga clic en la fecha para continuar.

    ![Orchard desde la lista](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png)

5.  En la página **Configure Your App**, escriba o seleccione los valores de todos los campos:

-   Escriba un nombre de URL de su elección.
-   Seleccione la región más cercana a los usuarios. (Con esto se asegurará el mejor rendimiento).

    ![configurar su aplicación](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png)

1.  Haga clic en la marca de verificación en la esquina inferior derecha para iniciar la implementación de su nuevo sitio web de Orchard CMS.

Azure iniciará las operaciones de compilación e implementación. Durante la compilación y la implementación del sitio web, se muestra el estado de estas operaciones en la parte inferior del Portal de administración de sitios web. Una vez realizadas todas las operaciones, se mostrará un mensaje indicando que el sitio web se ha creado.

Inicio y administración de su sitio Orchard
-------------------------------------------

1.  Haga clic en el nombre de su sitio web en la página **Sitios web** y, a continuación, haga clic en **Browse** en la parte inferior del portal para abrir la página de bienvenida del sitio web.

    ![panel de inicio](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png)

    ![botón examinar](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png)

2.  Escriba la información de configuración que solicite Orchard y haga clic en **Finish Setup** para finalizar la configuración y abrir la página de inicio de sesión del sitio web.

    ![iniciar sesión en Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png)

    Tendrá un nuevo sitio Orchard con un aspecto similar al de la captura de pantalla que se muestra a continuación.

    ![su sitio Orchard](./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png)

3.  Siga los detalles de la [documentación de Orchard](http://docs.orchardproject.net/) (en inglés) para obtener más información sobre Orchard y configurar el sitio web.

Pasos siguientesPaso siguiente
------------------------------

-   [Desarrollo e implementación de un sitio web con Microsoft WebMatrix](/es-es/develop/net/tutorials/website-with-webmatrix/) - Obtenga información sobre cómo editar un sitio web de Azure en WebMatrix.
-   [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](/es-es/develop/net/tutorials/web-site-with-sql-database/) - Obtenga información acerca de cómo crear un nuevo sitio web desde Visual Studio.

