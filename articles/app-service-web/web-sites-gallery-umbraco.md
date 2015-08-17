<properties 
	pageTitle="Creación de una aplicación web de Umbraco desde el Marketplace en Microsoft Azure" 
	description="Cree un sistema de administración de contenido de Umbraco e impleméntelo en Aplicaciones web del Servicio de aplicaciones de Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="tomfitz"/>

#Creación de una aplicación web de Umbraco desde el Marketplace en Microsoft Azure#

CMS de Umbraco es un sistema de administración de contenido de código fuente abierto repleto de características que se puede usar para crear una amplia variedad de aplicaciones, tanto pequeñas como complejas. Azure Marketplace proporciona una amplia gama de aplicaciones web conocidas desarrolladas por Microsoft, empresas de terceros e iniciativas de software de código abierto. La galería le permite crear una aplicación CMS de Umbraco en pocos minutos aplicando los kits de inicio o integrando su propio diseño.

En este artículo se muestra el nuevo Portal de vista previa de Azure, que simplifica considerablemente la administración de recursos. El Portal de vista previa de Azure está diseñado para acelerar el proceso de entrega de software colocando servicios, tecnologías y herramientas multiplataforma de Microsoft y sus asociados en una sola área de trabajo. En lugar de utilizar recursos autónomos, como Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), proyectos de Visual Studio o bases de datos, puede crear, administrar y analizar toda la aplicación como un solo grupo de recursos.

En este tutorial, aprenderá a:

- Crear una aplicación web nueva mediante el Marketplace usando el Portal de vista previa de Azure
- Crear un sitio web de blogs mediante CMS de Umbraco 

##Crear una aplicación web desde el Marketplace en el Portal de vista previa de Azure

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/).

2. Elija el icono **Marketplace**.
	
3. En **Marketplace**, seleccione la pestaña **Web y móvil** y seleccione **CMS de Umbraco**.
	
4. Para crear una nueva aplicación web de CMS de Umbraco, haga clic en **Crear**.
	
5. El siguiente paso es configurar todos los recursos asociados con CMS de Umbraco. En este caso, los recursos son una aplicación web y una base de datos de SQL Server. En primer lugar, seleccione **Aplicación web** para definir la configuración de la aplicación web, como **Dirección URL**, **Plan del servicio de aplicaciones**, **Configuración de aplicación web** y **Ubicación**.
	
	![Configure resources][04AppSettings]
	
6. Configure ahora la base de datos. Seleccione **Base de datos** y, a continuación, elija **Servidor**. En este ejemplo se crea un servidor SQL para la base de datos en Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Ahora que la aplicación web y la base de datos están configurados, puede comenzar a implementar la aplicación haciendo clic en **Crear** en la parte inferior de la primera hoja de **CMS de Umbraco** que aparece en la imagen anterior.
	
	![Click Create][06UmbracoCMSGroup]
	
Una vez completada la implementación, el portal mostrará la hoja correspondiente al grupo de recursos de la aplicación web CMS de Umbraco. En la sección **Resumen**, haga clic en el nombre de la aplicación web para ver las propiedades de la página web. Así mismo, en la sección **Resumen**, puede seleccionar el recurso de base de datos para ver las propiedades de la base de datos asociada.

## Inicio y configuración del sitio web de CMS de Umbraco ##

1. En la hoja de detalles de la aplicación web, haga clic en **Examinar** para buscar la aplicación web.
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Cuando examine la aplicación web, CMS de Umbraco iniciará el asistente del instalador. Escriba la información solicitada y, a continuación, haga clic en **Personalizar**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Escriba los detalles de conexión y autenticación para la base de datos que usará Umbraco. Seleccione **Microsoft SQL Azure** para el tipo de base de datos. Puede obtener la cadena de conexión para la base de datos en la sección **Configuración del sitio** de la aplicación web.
	
	![Configure your database][10ConfigureYourDatabase]
	
4. Si es nuevo en CMS de Umbraco, puede seleccionar un kit de sitio web de inicio. De lo contrario, haga clic en **No gracias, no quiero instalar un sitio web de inicio**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. El instalador de Umbraco completará la instalación para la aplicación. Una vez configurada la aplicación, se le redirigirá al panel administrativo de CMS de Umbraco.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Ahora creará una página de texto de ejemplo que publicará. Seleccione **Contenido**, **Desbordamiento** y **Página de texto**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Escriba un título y algo de contenido para la página de texto tal y como se muestra a continuación. Cuando finalice, haga clic en **Guardar y publicar**.
	
	![Enter a title and some content][16EnterAName]
	
8. Espere mientras la página se publica. Cuando la publicación se complete, recibirá una pequeña alerta en la parte inferior derecha de la pantalla. Ahora puede buscar el nuevo contenido en la aplicación web.
	
	![Published web site page][17MyPage]
	

¡Ya está! Ha creado correctamente una aplicación web de blobs usando CMS de Umbraco en unos pocos minutos.

##Recursos adicionales

[Documentación de Umbraco](http://our.umbraco.org/documentation)

[Tutoriales de vídeo de Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Documentación del Portal de Azure](../preview-portal.md)

[Portal de Azure (Channel 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Documentación de Aplicaciones web del Servicio de aplicaciones](/documentation/services/websites/)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal al de vista previa, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


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
 

<!---HONumber=August15_HO6-->