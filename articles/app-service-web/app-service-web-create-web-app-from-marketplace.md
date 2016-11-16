---
title: "Creación de una aplicación web desde Azure Marketplace | Microsoft Docs"
description: "Aprenda a crear una aplicación web de WordPress desde Azure Marketplace mediante el Portal de Azure."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 73fafeff227061feae1fbc0a1c50e5056116e3ea


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Creación de una aplicación web desde Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Por ejemplo, WordPress, Umbraco CMS, Drupal, etc. Estas aplicaciones web se basan en una amplia variedad de plataformas populares, como [PHP] en este ejemplo de WordPress, [.NET], [Node.js], [Java] y [Python], por nombrar algunos. Para crear una aplicación web desde Azure Marketplace, el único software que necesita es el explorador que usa para el [Portal de Azure].

En este tutorial, aprenderá a:

* Encontrar y crear una aplicación web basada en la plantilla de Azure Marketplace del Servicio de aplicaciones de Azure.
* Configurar el Servicio de aplicaciones de Azure para la nueva aplicación web.
* Iniciar y administrar la aplicación web.

En este tutorial implementará un blog de WordPress desde Azure Marketplace. Una vez terminados los pasos de este tutorial, tendrá su propio sitio de WordPress en marcha en la nube.

![Ejemplo de panel de la aplicación web de WordPress][WordPressDashboard1]

El sitio de WordPress que se implementa en este tutorial utiliza MySQL para la base de datos. Si desea usar Base de datos SQL para la base de datos, consulte [Project Nami], que también está disponible en Azure Marketplace.

> [!NOTE]
> Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio][activar] o bien [registrarse para obtener una evaluación gratuita][evaluación gratuita].
> 
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [prueba gratuita del Servicio de aplicaciones]. A partir de ahí puede crear de forma inmediata una aplicación web de inicio de corta duración en Servicio de aplicaciones; no se requiere ninguna tarjeta de crédito y no se establece ningún compromiso.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Búsqueda y creación de una aplicación web en el Servicio de aplicaciones de Azure
1. Inicie sesión en el [Portal de Azure].
2. Haga clic en **Nuevo**.
   
    ![Creación de un nuevo recurso de Azure][MarketplaceStart]
3. Busque **WordPress** y haga clic en el icono **WordPress**. Si desea utilizar Base de datos SQL en lugar de MySQL, busque **Project Nami**.
   
    ![Búsqueda de WordPress en Marketplace][MarketplaceSearch]
4. Después de leer la descripción de la aplicación de WordPress, seleccione **Crear**.
   
    ![Creación de una aplicación web de WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Configuración del Servicio de aplicaciones de Azure para la nueva aplicación web
1. Una vez creada la nueva aplicación web, se muestra la hoja de configuración de WordPress, que usará para completar los pasos siguientes:
   
    ![Configuración de las opciones de la aplicación web de WordPress][ConfigStart]
2. Escriba un nombre para la aplicación web en el cuadro **Aplicación web** .
   
    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será *{nombre}*.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.
   
    ![Configuración del nombre de la aplicación web de WordPress][ConfigAppName]
3. Si tiene más de una suscripción, elija la que quiere usar.
   
    ![Configuración de la suscripción para la aplic. web][ConfigSubscription]
4. Seleccione un **Grupo de recursos** o cree uno nuevo.
   
    Para obtener más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager][Grupos de recursos].
   
    ![Configuración del grupo de recursos para la aplicación web][ConfigResourceGroup]
5. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.
   
    Para obtener más información sobre los planes de App Service, consulte [Información general sobre los planes de Azure App Service][Planes de Azure App Service].
   
    ![Configuración del plan de servicio para la aplicación web][ConfigServicePlan]
6. Haga clic en **Base de datos** y, en la hoja **Nueva base de datos MySQL**, especifique los valores requeridos para configurar una base de datos MySQL.
   
    a. Escriba un nombre nuevo o deje el nombre predeterminado.
   
    b. Deje el valor de **Tipo de base de datos** establecido en **Compartido**.
   
    c. Elija la misma ubicación que la que eligió para la aplicación web.
   
    d. Elija un plan de tarifa. **Mercurio** : para este tutorial sirve Mercurio, que es gratis y tiene un mínimo de conexiones y espacio en disco.
   
    e. En la hoja **Nueva base de datos MySQL**, acepte los términos legales y haga clic en **Aceptar**.
   
    ![Configuración de las opciones de la base de datos para la aplicación web][ConfigDatabase]
7. En la hoja **WordPress**, acepte los términos legales y haga clic en **Crear**.
   
    ![Finalización de la configuración de la aplicación web y clic en Aceptar][ConfigFinished]
   
    El Servicio de aplicaciones de Azure crea la aplicación web, normalmente en menos de un minuto. Para ver el progreso, haga clic en el icono de campana en la parte superior de la página del portal.
   
    ![Indicador de progreso][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Inicio y administración de la aplicación web de WordPress
1. Cuando finalice la creación de aplicaciones web, en el Portal de Azure, vaya al grupo de recursos en el que creó la aplicación y podrá ver la aplicación web y la base de datos.
   
    El recurso adicional con el icono de bombilla es [Application Insights][Application Insights], que proporciona servicios de supervisión para la aplicación web.
2. En la hoja **Grupo de recursos** , haga clic en la línea de la aplicación web.
   
    ![Selección de la aplicación web de WordPress][WordPressSelect]
3. En la hoja de la aplicación web, haga clic en **Examinar**.
   
    ![Desplazamiento a la aplicación web de WordPress][WordPressBrowse]
4. Si se le pide que seleccione el idioma para el blog de WordPress, seleccione el que quiera y haga clic en **Continuar**.
   
    ![Configuración del idioma de la aplicación web de WordPress][WordPressLanguage]
5. En la página **principal** de WordPress, escriba la información de configuración que WordPress requiere y, a continuación, haga clic en **Instalar WordPress**.
   
    ![Configuración de las opciones de la aplicación web de WordPress][WordPressConfigure]
6. Inicie sesión con las credenciales que creó en la página **principal** .  
7. Se abrirá la página Panel del sitio, que mostrará la información que proporcionó.    
   
    ![Visualización del panel de WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a crear e implementar una aplicación web de ejemplo desde Azure Marketplace.

Para obtener más información sobre cómo trabajar con las aplicaciones web del Servicio de aplicaciones, consulte los vínculos que se muestran en el lado izquierdo de la página (para ventanas de explorador anchas) o en la parte superior de la página (para ventanas de explorador estrechas).

Para más información acerca de cómo desarrollar aplicaciones web de WordPress en Azure, consulte [Desarrollo de WordPress en Azure App Service][WordPress en Azure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activar]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[evaluación gratuita]: https://azure.microsoft.com/pricing/free-trial/
[prueba gratuita del Servicio de aplicaciones]: http://go.microsoft.com/fwlink/?LinkId=523751
[Grupos de recursos]: ../azure-resource-manager/resource-group-overview.md
[Planes de Azure App Service]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Application Insights]: https://azure.microsoft.com/services/application-insights/
[Portal de Azure]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPress en Azure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[Marketplace Inicio]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[Marketplace Buscar]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[Marketplace Crear]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[Configurar inicio]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[Configurar nombre aplicación]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[Configurar suscripción]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[Configurar grupo de recursos]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[Configurar plan de servicio]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[Configurar base de datos]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[Configuración finalizada]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[Configuración en curso]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPress seleccionar]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPress examinar]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPress idioma]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPress panel 1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPress panel 2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPress configurar]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Nov16_HO2-->


