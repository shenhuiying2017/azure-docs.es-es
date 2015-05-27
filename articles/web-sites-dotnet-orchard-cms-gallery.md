<properties 
	pageTitle="Creación de una aplicación web Orchard CMS desde Azure Marketplace" 
	description="Un tutorial que le enseña a crear una nueva aplicación web en Azure. Obtenga también información sobre cómo iniciar y administrar la aplicación web con el Portal de Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Creación de una aplicación web Orchard CMS desde Azure Marketplace

## Información general

Marketplace pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde Marketplace no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obtener más información sobre las aplicaciones web de Marketplace, consulte [Azure Marketplace](/marketplace/).

En este tutorial, aprenderá a:

- Creación de una nueva aplicación web desde Marketplace

- Inicio y administración de la aplicación web desde el Portal de Azure
 
Va a crear una aplicación web de Orchard CMS que utiliza una plantilla predeterminada. [Orchard](http://www.orchardproject.net/) es una aplicación CMS, basada en .NET, gratuita y de código abierto que le permite crear aplicaciones web personalizadas controladas por contenido. Orchard CMS incluye un marco de extensibilidad mediante el cual puede [descargar módulos y temas adicionales](http://gallery.orchardproject.net/) para personalizar la aplicación web. La ilustración siguiente muestra la aplicación web de Orchard CMS que va a crear en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

![Blog de Orchard][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de una aplicación web de Orchard CMS desde Marketplace

1. Inicie sesión en el [Portal de vista previa de Azure](http://portal.azure.com).

2. Elija **Nuevo** > **Web + Móvil** > **Azure Marketplace**.
	
	![Crear nuevo][1]

3. Haga clic en **Aplicaciones web** > **Orchard CMS**. En la siguiente hoja, haga clic en **Crear**.
	
	![Creación desde Marketplace][2]

4. Configure la dirección URL, el plan de servicio de la aplicación, el grupo de recursos y la ubicación de la aplicación web. Cuando haya terminado, haga clic en **Crear**.
	
	![Configuración de la aplicación][3]

	Una vez creada la aplicación web, el botón **Notificaciones** mostrará un botón verde que indicará el estado de "CORRECTO" y aparecerá la hoja de la aplicación web.

## Inicio y administración de la aplicación web de Orchard

1. En la hoja de la aplicación web, haga clic en **Examinar** para abrir la página de bienvenida de la aplicación web.

	![botón examinar][12]

2. Escriba la información de configuración que solicite Orchard y haga clic en **Finalizar configuración** para finalizar la configuración y abrir la página de inicio de sesión de la aplicación web.

	![iniciar sesión en Orchard][7]

	Tendrá una nueva aplicación web de Orchard con un aspecto similar al de la captura de pantalla que se muestra a continuación.

	![su aplicación web de Orchard][13]

3. Siga los detalles de la [documentación de Orchard](http://docs.orchardproject.net/) para obtener más información sobre Orchard y configurar la aplicación web.

## Paso siguiente

* [Desarrollo e implementación de una aplicación web con Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) - Obtenga información sobre cómo editar una aplicación web del Servicio de aplicaciones en WebMatrix. 
* [Creación de una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementación en el Servicio de aplicaciones de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) - Obtenga información sobre cómo crear una nueva aplicación web en el Servicio de aplicaciones de Azure desde Visual Studio.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal al de vista previa, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54-->