<properties urlDisplayName="Website from Gallery" pageTitle="Creación de un sitio web de Orchard CMS desde la Galería de Azure" metaKeywords ="Azure crear sitio Web, administrar sitio web Azure" description="Un tutorial que le enseña a crear un nuevo sitio web en Azure. También explica cómo iniciar y administrar el sitio con el Portal de administración." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

# Creación de un sitio web de Orchard CMS desde la galería de Azure

La galería pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde la galería no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al [Portal de administración de Azure](http://manage.windowsazure.com). Para obtener más información sobre las aplicaciones web de la galería, consulte [Windows Web App Gallery](http://www.microsoft.com/web/gallery/categories.aspx).

En este tutorial, aprenderá a:

- Crear un nuevo sitio desde la galería

- Iniciar y administrar el sitio desde el Portal de administración
 
Va a crear un sitio Orchard CMS que utiliza una plantilla predeterminada. [Orchard](http://www.orchardproject.net/) es una aplicación CMS, basada en .NET, gratuita y de código abierto que le permite crear sitios web personalizados controlados por contenido. Orchard CMS incluye un marco de extensibilidad mediante el cual puede [descargar módulos y temas adicionales](http://gallery.orchardproject.net/) para personalizar el sitio. La ilustración siguiente muestra el sitio de Orchard que va a crear.

![Orchard blog][13]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Creación de un sitio web de Orchard desde la galería</h2>

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2. Haga clic en el icono **New**, situado en la parte inferior izquierda del portal.
	
	![Create New][1]

3. Haga clic en el icono **Sitio web** y, a continuación, haga clic en **De la galería**.
	
	![Create From Gallery][2]

4. Busque en el icono **Orchard CMS** en la lista, haga clic en él y, a continuación, haga clic en la fecha para continuar.
	
	![Orchard from list][3]

5. En la página **Configure Your App**, escriba o seleccione los valores de todos los campos:
	
- Escriba un nombre de URL de su elección.	
- Seleccione la región más cercana a los usuarios. (Con esto se asegurará el mejor rendimiento).

	![configure your app][4]

6. Haga clic en la marca de verificación en la esquina inferior derecha para iniciar la implementación de su nuevo sitio web de Orchard CMS.

Azure iniciará las operaciones de compilación e implementación. Durante la compilación y la implementación del sitio web, se muestra el estado de estas operaciones en la parte inferior del Portal de administración de sitios web. Una vez realizadas todas las operaciones, se mostrará un mensaje indicando que el sitio web se ha creado.

<h2>Inicio y administración de su sitio Orchard</h2>

1. Haga clic en el nombre de su sitio web en la página **Sitios web** y, a continuación, haga clic en **Examinar** en la parte inferior del portal para abrir la página de bienvenida del sitio web.

	![launch dashboard][5]

	![browse button][12]

2. Escriba la información de configuración que solicite Orchard y haga clic en **Finalizar configuración** para finalizar la configuración y abrir la página de inicio de sesión del sitio web.

	![login to Orchard][7]

	Tendrá un nuevo sitio Orchard con un aspecto similar al de la captura de pantalla que se muestra a continuación.  

	![your Orchard site][13]

3. Siga los detalles de la [documentación de Orchard](http://docs.orchardproject.net/) (en inglés) para obtener más información sobre Orchard y configurar el sitio web.

<h2>Paso siguiente</h2>
* [Desarrollo e implementación de un sitio web con Microsoft WebMatrix](/es-es/develop/net/tutorials/website-with-webmatrix/) - Obtenga información sobre cómo editar un sitio web de Azure en WebMatrix. 
* [Implementación de una aplicación ASP.NET MVC segura con suscripción, OAuth y Base de datos SQL en un sitio web de Azure](/es-es/develop/net/tutorials/web-site-with-sql-database/) - Obtenga información acerca de cómo crear un nuevo sitio web desde Visual Studio.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


