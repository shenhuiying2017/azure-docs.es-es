---
title: "Implementación de una aplicación de WordPress en Azure Portal en cinco minutos | Microsoft Docs"
description: "Aprenda lo fácil que es ejecutar aplicaciones web en App Service mediante la implementación de una aplicación de WordPress. Vea los resultados inmediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>Implementación de una aplicación de WordPress en Azure Portal en cinco minutos

En este tutorial se muestra cómo implementar su primera aplicación web de [WordPress](https://wordpress.org/) en [Azure App Service](../app-service/app-service-value-prop-what-is.md) en cuestión de minutos.

![Sitio de WordPress](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Requisitos previos
Necesita una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> También puede [probar App Service](https://azure.microsoft.com/try/app-service/) sin una cuenta de Azure. Cree una aplicación de inicio y juegue con ella durante una hora como máximo; no se requiere ninguna tarjeta de crédito ni ningún compromiso.
> 
> 

## <a name="deploy-the-wordpress-app"></a>Implementación de la aplicación de WordPress
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress).

    Este vínculo es un acceso directo para configurar inmediatamente una nueva aplicación de WordPress en Azure Portal.

3. En **Nombre de la aplicación**, escriba un nombre de aplicación web. Verá una marca de verificación verde en el cuadro si el nombre es único en el dominio `azurewebsites.net`.
   
5. En **Grupo de recursos**, haga clic en **Create new** (Crear nuevo) para crear un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) y, luego, asígnele un nombre.

6. En **Proveedor de base de datos**, seleccione **CleaDB**.

7. Haga clic en **Plan de App Service/Ubicación** > **Create New** (Crear nuevo). Configure el [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) tal como se muestra:

    - En **Plan de App Service**, escriba el nombre deseado.
    - En **Ubicación**, elija una ubicación para hospedar el plan.
    - Haga clic en **Plan de tarifa** y luego seleccione **F1 Free** (F1 Gratis) u otro plan que se adapta a sus necesidades y, seguidamente, haga clic en **Seleccionar**.
    - Haga clic en **Aceptar**.

8. Haga clic en **Base de datos** > **Create New** (Crear nueva). Configure la base de datos SQL tal y como se muestra:

    - En **Nombre de la base de datos**, escriba un nombre para la base de datos. 
    - En **Ubicación**, elija la misma ubicación que el plan de App Service.
    - Haga clic en **Plan de tarifa** y luego seleccione **Mercury** u otro plan que se adapta a sus necesidades y, seguidamente, haga clic en **Seleccionar**.
    - Haga clic en **Condiciones legales** y luego en **Compra**.
    - Haga clic en **Aceptar**.

9. Haga clic en **Crear**.

    Ahora, Azure crea la aplicación de WordPress según su configuración. Verá la notificación **Implementación iniciada...**

    ![Implementación iniciada: primera aplicación de WordPress en Azure App Service](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>Inicio y administración de la aplicación web de WordPress

Cuando Azure finalice la implementación de aplicación, verá otra notificación.

![La implementación finalizó correctamente: primera aplicación de WordPress en Azure App Service](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Haga clic en la notificación. Si se la ha perdido, siempre puede acceder a ella haciendo clic en la campana de notificación (![Notificación siguiente: primera aplicación de WordPress en Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Ahora verá la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) de administración de la aplicación web (*hoja*: una página del portal que se abre horizontalmente).

3. En la parte superior de la página de introducción, haga clic en **Examinar**.
   
    ![Examinar: primera aplicación de WordPress en Azure App Service](./media/app-service-web-get-started-php-portal/browse.png)

    Ahora puede ver la página de **bienvenida** de WordPress. Configure la instalación de WordPress y ya puede comenzar a jugar.

    ![Configuración de WordPress: primera aplicación de WordPress en Azure App Service](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Pasos siguientes
* [Creación, configuración e implementación de una aplicación web de Laravel en Azure](app-service-web-php-get-started.md): conozca las habilidades básicas que necesita para ejecutar una aplicación web de PHP en Azure, como por ejemplo:

    * Crear y configurar aplicaciones en Azure desde PowerShell/Bash.
    * Establecer la versión de PHP.
    * Usar un archivo de inicio que no está en el directorio raíz de la aplicación.
    * Habilitar la automatización de Composer.
    * Acceder a variables de entorno específico.
    * Solucionar errores comunes.

* [Implementación del código en Azure App Service](web-sites-deploy.md): aprenda a implementar desde FTP o desde repositorios de control del código fuente.
* [Incorporación de funcionalidad a su primera aplicación web](app-service-web-get-started-2.md): lleve su aplicación de Azure al siguiente nivel. Autentique los usuarios. Escálela según la demanda. Configure algunas alertas de rendimiento. Todo ello con unos cuantos clics.

