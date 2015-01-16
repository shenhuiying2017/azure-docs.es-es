<properties title="Create an Umbraco website from the gallery in Microsoft Azure" pageTitle="Creación de un sitio web de Umbraco desde la galería en Microsoft Azure" description="requerido" metaKeywords="Azure, gallery, Umbraco, web site, website" services="web-sites" solutions="web" documentationCenter="" authors="tomfitz" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Creación de un sitio web de Umbraco desde la galería en Microsoft Azure#

CMS de Umbraco es un sistema de administración de contenido de código fuente abierto repleto de características que se puede usar para crear una amplia variedad de aplicaciones, tanto pequeñas como complejas. La galería de aplicaciones de Sitios web Azure pone a disposición del usuario una gran variedad de conocidas aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. La galería le permite crear un sitio CMS de Umbraco en pocos minutos aplicando los kits de inicio o integrando su propio diseño. 

En este artículo se muestra el nuevo Portal de vista previa de Azure, que simplifica considerablemente la administración de recursos. El nuevo portal de Azure está diseñado para acelerar el proceso de entrega de software colocando herramientas, tecnologías y servicios entre plataformas de Microsoft y sus asociados en una sola área de trabajo. En lugar de utilizar recursos autónomos como Sitios Web Azure, proyectos de Visual Studio o bases de datos, puede crear, administrar y analizar toda la aplicación como un solo grupo de recursos. 

En este tutorial, aprenderá a:

- Crear un nuevo sitio mediante la galería usando el nuevo Portal de vista previa de Azure
- Crear un sitio web de blogs mediante CMS de Umbraco 

##Creación de un sitio web desde la galería en el portal de Azure

1. Inicie sesión en el [Portal de administración de Microsoft Azure](https://portal.azure.com/).

2. Elija el icono **Galería de Azure**.
	
	![Choose Web Gallery][01Startboard]
	
3. En la **Galería**, seleccione la pestaña **Web** y, a continuación, seleccione **CMS de Umbraco**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Para crear un nuevo sitio web CMS de Umbraco, haga clic en **Crear**.
	
	![Click Create][03UmbracoCMS]
	
5. El siguiente paso es configurar todos los recursos asociados con CMS de Umbraco. En este caso, los recursos son un sitio web y una base de datos de SQL Server. En primer lugar, seleccione **Sitio web** para definir la configuración del sitio web: **Dirección URL**, **Plan de hospedaje web**, **Configuración de aplicación web** y **Ubicación**. 
	
	![Configure resources][04AppSettings]
	
6. Configure ahora la base de datos. Seleccione **Base de datos** y elija **Crear una nueva base de datos**. En este ejemplo se crea un servidor SQL para la base de datos en Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Ahora que el sitio web y la base de datos están configurados, puede comenzar a implementar la aplicación haciendo clic en **Crear** en la parte inferior del primer cuadro **CMS de Umbraco** que aparece en la imagen anterior.
	
	![Click Create][06UmbracoCMSGroup]
	
Una vez completada la implementación, el panel de inicio del portal muestra que se ha creado el grupo de recursos para CMS de Umbraco, en este caso **UmbracoCMSgroup**. En la sección **Resumen**, haga clic en el nombre del sitio web (en este caso **umbracocmsgroup**) para ver las propiedades del mismo. Asimismo, en la sección **Resumen**, puede seleccionar el recurso de base de datos para ver las propiedades de la base de datos asociada.
	
![][07UmbracoCMSGroupBlade]

## Inicio y configuración del sitio web de CMS de Umbraco ##

1. En el cuadro de detalles del sitio web, haga clic en **Examinar** para buscar el sitio (en este caso umbracocmsgroup.azurewebsites.net.)
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Cuando examine el sitio web, CMS de Umbraco iniciará el asistente del instalador. Escriba la información solicitada y haga clic en **Personalizar**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Escriba los detalles de conexión y autenticación para la base de datos que usará Umbraco. Seleccione **Microsoft SQL Azure** para el tipo de base de datos.  Puede obtener la cadena de conexión para la base de datos en la sección **Configuración del sitio** del sitio web.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Si es nuevo en CMS de Umbraco, puede seleccionar un kit de sitio web de inicio. De lo contrario, haga clic en **No gracias, no quiero instalar un sitio web de inicio**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. El instalador de Umbraco completará la instalación para la aplicación. Una vez configurada la aplicación, se le redirigirá al panel administrativo de CMS de Umbraco.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Ahora creará una página de texto de ejemplo que publicará. Seleccione **Contenido**, **Desbordamiento** y **Página de texto**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Escriba un título y algo de contenido para la página de texto tal y como se muestra a continuación. Cuando finalice, haga clic en **Guardar y publicar**.
	
	![Enter a title and some content][16EnterAName]
	
8. Espere mientras la página se publica. Cuando la publicación se complete, recibirá una pequeña alerta en la parte inferior derecha de la pantalla. Ahora puede buscar el nuevo contenido en el sitio web. 
	
	![Published web site page][17MyPage]
	

¡Ya está! Ha creado correctamente un sitio web de blogs usando CMS de Umbraco en unos pocos minutos. 

##Recursos adicionales

[Documentación de Umbraco](http://our.umbraco.org/documentation)

[Tutoriales de vídeo de Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Introducción al Portal de vista previa de Microsoft Azure](http://azure.microsoft.com/es-es/overview/preview-portal/)

[Documentación del Portal de vista previa de Microsoft Azure](http://azure.microsoft.com/es-es/documentation/preview-portal/)

[Portal de vista previa de Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Documentación de Sitios web Microsoft Azure](http://azure.microsoft.com/es-es/documentation/services/web-sites/)


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG
