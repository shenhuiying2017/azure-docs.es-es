---
title: "Creación de una aplicación web en Azure App Service | Microsoft Docs"
description: "Obtenga información sobre cómo crear una nueva aplicación web de Azure para un blog de WordPress mediante el Portal de Azure."
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 193ae094-0d7c-4749-a09b-ff4b1240149e
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: hero-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 83ef925a8268873d09c4a1611a1f2341acd92ba7


---
# <a name="create-a-wordpress-web-app-in-azure-app-service"></a>Creación de una aplicación web de WordPress en el Servicio de aplicaciones de Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial le muestra cómo implementar un blog de WordPress desde Azure Marketplace.

Una vez terminado el tutorial, tendrá su propio sitio de blog de WordPress en marcha en la nube.

![Sitio de WordPress](./media/web-sites-php-web-site-gallery/wpdashboard.png)

Aprenderá a realizar los siguientes procedimientos:

* Cómo buscar una plantilla de aplicación en Azure Marketplace.
* Cómo crear una aplicación web basada en la plantilla en el Servicio de aplicaciones de Azure.
* Cómo configurar el Servicio de aplicaciones de Azure para la nueva aplicación web y la base de datos.

Azure Marketplace pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web se basan en una amplia variedad de marcos populares, como [PHP](/develop/nodejs/) en este ejemplo de WordPress, [.NET](/develop/net/), [Node.js](/develop/nodejs/), [Java](/develop/java/) y [Python](/develop/python/), por nombrar algunos. Para crear una aplicación web desde Azure Marketplace, el único software que necesita es el explorador que usa para el [Portal de Azure](https://portal.azure.com/). 

El sitio de WordPress que se implementa en este tutorial utiliza MySQL para la base de datos. Si en vez de esta desea utilizar la base de datos SQL como base de datos, consulte el [Proyecto Nami](http://projectnami.org/). **Proyecto Nami** también está disponible a través de Marketplace.

> [!NOTE]
> Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o bien [registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a la [prueba gratuita del Servicio de aplicaciones](https://azure.microsoft.com/try/app-service/). Ahí puede crear de forma inmediata una aplicación web de corta duración para iniciarse en Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.
> 
> 

## <a name="select-wordpress-and-configure-for-azure-app-service"></a>Selección de WordPress y configuración para el Servicio de aplicaciones de Azure
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Nuevo**.
   
    ![Crear nuevo][5]
3. Busque **WordPress** y haga clic en el icono **WordPress**. Si desea utilizar la base de datos SQL en lugar de MySQL, busque **Proyecto Nami**.
   
    ![WordPress desde la lista][7]
4. Después de leer la descripción de la aplicación de WordPress, seleccione **Crear**.
   
    ![Crear](./media/web-sites-php-web-site-gallery/create.png)
5. Escriba un nombre para la aplicación web en el cuadro **Aplicación web** .
   
    Este nombre debe ser único en el dominio azurewebsites.net porque la dirección URL de la aplicación web será {nombre}.azurewebsites.net. Si el nombre especificado no es único, se muestra un signo de exclamación rojo en el cuadro de texto.
6. Si tiene más de una suscripción, elija la que quiere usar. 
7. Seleccione un **Grupo de recursos** o cree uno nuevo.
   
    Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
8. Seleccione un **Plan de servicio de aplicaciones/Ubicación** o cree uno nuevo.
   
    Para obtener más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)    
9. Haga clic en **Base de datos** y, en la hoja **Nueva base de datos MySQL**, especifique los valores requeridos para configurar una base de datos MySQL.
   
    a. Escriba un nombre nuevo o deje el nombre predeterminado.
   
    b. Deje el valor de **Tipo de base de datos** establecido en **Compartido**.
   
    c. Elija la misma ubicación que la que eligió para la aplicación web.
   
    d. Elija un plan de tarifa. Para este tutorial sirve Mercurio (gratis con un mínimo de conexiones y espacio en disco).
10. En la hoja **Nueva base de datos MySQL**, haga clic en **Aceptar**. 
11. En la hoja **WordPress**, acepte los términos legales y haga clic en **Crear**. 
    
     ![Configuración de la aplicación web](./media/web-sites-php-web-site-gallery/configure.png)
    
     El Servicio de aplicaciones de Azure crea la aplicación web, normalmente en menos de un minuto. Para ver el progreso, haga clic en el icono de campana en la parte superior de la página del portal.
    
     ![Indicador de progreso](./media/web-sites-php-web-site-gallery/progress.png)

## <a name="launch-and-manage-your-wordpress-web-app"></a>Inicio y administración de la aplicación web de WordPress
1. Cuando finalice la creación de aplicaciones web, en el Portal de Azure, vaya al grupo de recursos en el que creó la aplicación y podrá ver la aplicación web y la base de datos.
   
    El recurso adicional con el icono de bombilla es [Application Insights](/services/application-insights/), que proporciona servicios de supervisión para la aplicación web.
2. En la hoja **Grupo de recursos** , haga clic en la línea de la aplicación web.
   
    ![Configuración de la aplicación web](./media/web-sites-php-web-site-gallery/resourcegroup.png)
3. En la hoja de la aplicación web, haga clic en **Examinar**.
   
    ![URL del sitio][browse]
4. En la página **principal** de WordPress, escriba la información de configuración que WordPress requiere y, a continuación, haga clic en **Instalar WordPress**.
   
    ![Configuración de WordPress](./media/web-sites-php-web-site-gallery/wpconfigure.png)
5. Inicie sesión con las credenciales que creó en la página **principal** .  
6. Se abre la página Panel del sitio.    
   
    ![Sitio de WordPress](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## <a name="next-steps"></a>Pasos siguientes
Ya sabe cómo crear e implementar una aplicación web PHP desde la galería. Para obtener más información sobre cómo usar Java en Azure, consulte el [Centro para desarrolladores de Java](/develop/php/).

Para obtener más información sobre cómo trabajar con las aplicaciones web del Servicio de aplicaciones, consulte los vínculos que se muestran en el lado izquierdo de la página (para ventanas de explorador anchas) o en la parte superior de la página (para ventanas de explorador estrechas). 

## <a name="whats-changed"></a>Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web al Servicio de aplicaciones, consulte [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[browse]: ./media/web-sites-php-web-site-gallery/browse-web.png



<!--HONumber=Feb17_HO3-->


