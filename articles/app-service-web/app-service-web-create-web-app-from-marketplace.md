<properties
	pageTitle="Creación de una aplicación web desde Azure Marketplace | Microsoft Azure"
	description="Aprenda a crear una aplicación web de WordPress desde Azure Marketplace mediante el Portal de Azure."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# Creación de una aplicación web desde Azure Marketplace

[AZURE.INCLUDE [pestañas](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Por ejemplo, WordPress, Umbraco CMS, Drupal, etc. Estas aplicaciones web se basan en una amplia variedad de plataformas populares, como [PHP] en este ejemplo de WordPress, [.NET], [Node.js], [Java] y [Python], por nombrar algunos. Para crear una aplicación web desde Azure Marketplace, el único software que necesita es el explorador que usa para el [Portal de Azure].

En este tutorial, aprenderá a:

* Encontrar y crear una aplicación web basada en la plantilla de Azure Marketplace del Servicio de aplicaciones de Azure.
* Configurar el Servicio de aplicaciones de Azure para la nueva aplicación web.
* Iniciar y administrar la aplicación web.

En este tutorial implementará un blog de WordPress desde Azure Marketplace. Una vez terminados los pasos de este tutorial, tendrá su propio sitio de WordPress en marcha en la nube.

![Ejemplo de panel de la aplicación web de WordPress][WordPressDashboard1]

El sitio de WordPress que se implementa en este tutorial utiliza MySQL para la base de datos. Si desea usar Base de datos SQL para la base de datos, consulte [Project Nami], que también está disponible en Azure Marketplace.

> [AZURE.NOTE]
Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio][activate] o bien [registrarse para obtener una evaluación gratuita][free trial].
>
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones]. A partir de ahí puede crear de forma inmediata una aplicación web de inicio de corta duración en Servicio de aplicaciones; no se requiere ninguna tarjeta de crédito y no se establece ningún compromiso.

## Búsqueda y creación de una aplicación web en el Servicio de aplicaciones de Azure

1. Inicie sesión en el [Portal de Azure].

1. Haga clic en **Nuevo**.
	
	![Creación de un nuevo recurso de Azure][MarketplaceStart]
	
1. Busque **WordPress** y haga clic en el icono **WordPress**. Si desea utilizar Base de datos SQL en lugar de MySQL, busque **Project Nami**.

	![Búsqueda de WordPress en Marketplace][MarketplaceSearch]
	
1. Después de leer la descripción de la aplicación de WordPress, seleccione **Crear**.

	![Creación de una aplicación web de WordPress][MarketplaceCreate]

## Configuración del Servicio de aplicaciones de Azure para la nueva aplicación web

1. Una vez creada la nueva aplicación web, se muestra la hoja de configuración de WordPress, que usará para completar los pasos siguientes:

	![Configuración de las opciones de la aplicación web de WordPress][ConfigStart]

1. Escriba un nombre para la aplicación web en el cuadro **Aplicación web**.

	Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será *{nombre}*.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.

	![Configuración del nombre de la aplicación web de WordPress][ConfigAppName]

1. Si tiene más de una suscripción, elija la que quiere usar.

	![Configuración de la suscripción para la aplic. web][ConfigSubscription]

1. Seleccione un **Grupo de recursos** o cree uno nuevo.

	Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager][ResourceGroups].

	![Configuración del grupo de recursos para la aplicación web][ConfigResourceGroup]

1. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.

	Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción general sobre los planes del Servicio de aplicaciones de Azure][AzureAppServicePlans].

	![Configuración del plan de servicio para la aplicación web][ConfigServicePlan]

1. Haga clic en **Base de datos** y, en la hoja **Nueva base de datos MySQL**, proporcione los valores necesarios para configurar la base de datos MySQL.

	a. Escriba un nombre nuevo o deje el nombre predeterminado.

	b. Deje el **Tipo de base de datos** establecido en **Compartido**.

	c. Elija la misma ubicación que la que eligió para la aplicación web.

	d. Elija un plan de tarifa. **Mercurio**: para este tutorial sirve Mercurio, que es gratis y tiene un mínimo de conexiones y espacio en disco.

	e. En la hoja **Nueva base de datos MySQL**, acepte los términos legales y haga clic en **Aceptar**.

	![Configuración de las opciones de la base de datos para la aplicación web][ConfigDatabase]

1. En la hoja **WordPress**, acepte los términos legales y, después, haga clic en **Crear**.

	![Finalización de la configuración de la aplicación web y clic en Aceptar][ConfigFinished]

	El Servicio de aplicaciones de Azure crea la aplicación web, normalmente en menos de un minuto. Para ver el progreso, haga clic en el icono de campana en la parte superior de la página del portal.

	![Indicador de progreso][ConfigProgress]

## Inicio y administración de la aplicación web de WordPress
	
1. Cuando finalice la creación de aplicaciones web, en el Portal de Azure, vaya al grupo de recursos en el que creó la aplicación y podrá ver la aplicación web y la base de datos.

	El recurso adicional con el icono de bombilla es [Application Insights][ApplicationInsights], que proporciona servicios de supervisión para la aplicación web.

1. En la hoja **Grupo de recursos**, haga clic en la línea de la aplicación web.

	![Selección de la aplicación web de WordPress][WordPressSelect]

1. En la hoja de la aplicación web, haga clic en **Examinar**.

	![Desplazamiento a la aplicación web de WordPress][WordPressBrowse]

1. Si se le pide que seleccione el idioma para el blog de WordPress, seleccione el que quiera y haga clic en **Continuar**.

	![Configuración del idioma de la aplicación web de WordPress][WordPressLanguage]

1. En la página **principal** de WordPress, escriba la información de configuración que WordPress necesita y, a continuación, haga clic en **Instalar WordPress**.

	![Configuración de las opciones de la aplicación web de WordPress][WordPressConfigure]

1. Inicie sesión con las credenciales que creó en la página **principal**.

1. Se abrirá la página Panel del sitio, que mostrará la información que proporcionó.

	![Visualización del panel de WordPress][WordPressDashboard2]

## Pasos siguientes

En este tutorial, ha aprendido a crear e implementar una aplicación web de ejemplo desde Azure Marketplace.

Para obtener más información sobre cómo trabajar con las aplicaciones web del Servicio de aplicaciones, consulte los vínculos que se muestran en el lado izquierdo de la página (para ventanas de explorador anchas) o en la parte superior de la página (para ventanas de explorador estrechas).

Para más información acerca de cómo desarrollar aplicaciones web de WordPress en Azure, consulte [Desarrollo de WordPress en Servicio de aplicaciones de Azure][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Prueba del Servicio de aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Portal de Azure]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

<!---HONumber=AcomDC_0921_2016-->