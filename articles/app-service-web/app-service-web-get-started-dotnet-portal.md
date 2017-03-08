---
title: "Implementación de una aplicación web de Umbraco en Azure Portal en cinco minutos | Microsoft Docs"
description: "Aprenda lo fácil que es ejecutar aplicaciones web en App Service mediante la implementación de una aplicación ASP.NET de ejemplo. Vea los resultados inmediatamente."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: fa3f31cdd708729071876ffad707bea70567da83
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Implementación de una aplicación web de Umbraco en Azure Portal en cinco minutos

Este tutorial le ayuda a implementar una aplicación web de [Umbraco](https://our.umbraco.org/) en [Azure App Service](../app-service/app-service-value-prop-what-is.md) en cuestión de minutos.

![Aplicación de Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Requisitos previos
Necesita una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> También puede [probar App Service](https://azure.microsoft.com/try/app-service/) sin una cuenta de Azure. Cree una aplicación de inicio y juegue con ella durante una hora como máximo; no se requiere ninguna tarjeta de crédito ni ningún compromiso.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Implementación de la aplicación ASP.NET
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Abra [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Este vínculo es un acceso directo para configurar inmediatamente una nueva aplicación de Umbraco en Azure Portal.

3. En **Nombre de la aplicación**, escriba un nombre de aplicación web. Verá una marca de verificación verde en el cuadro si el nombre es único en el dominio `azurewebsites.net`.
   
5. En **Grupo de recursos**, haga clic en **Create new** (Crear nuevo) para crear un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) y, luego, asígnele un nombre.

7. Haga clic en **Plan de App Service/Ubicación** > **Create New** (Crear nuevo). Configure el [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) tal como se muestra:

    - En **Plan de App Service**, escriba el nombre deseado.
    - En **Ubicación**, elija una ubicación para hospedar el plan.
    - Haga clic en **Plan de tarifa** y luego seleccione **F1 Free** (F1 Gratis) u otro plan que se adapta a sus necesidades y, seguidamente, haga clic en **Seleccionar**.
    - Haga clic en **Aceptar**.

    La configuración de CMS de Umbraco debe parecerse a la captura de pantalla siguiente:

    ![Configuración en curso: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Haga clic en **SQL Database** > **Crear una base de datos nueva**. Configure la base de datos SQL tal y como se muestra:

    - En **Nombre**, escriba un nombre, como **myBD**.
    - Haga clic en **Plan de tarifa** y luego seleccione **F1 Free** (F1 Gratis) u otro plan que se adapta a sus necesidades y, seguidamente, haga clic en **Seleccionar**.
    - Haga clic en **Servidor de destino** > **Crear un servidor nuevo**. Configure el servidor de base de datos tal y como se muestra:

        - En **Nombre del servidor**, escriba un nombre para el servidor. Verá una marca de verificación verde en el cuadro si el nombre es único en el dominio `.database.windows.net`.
        - En **Inicio de sesión del administrador del servidor**, escriba el nombre de usuario de administrador deseado.
        - En **Contraseña** y **Confirmar contraseña**, escriba la contraseña deseada.
        - En Ubicación, seleccione la misma ubicación que usa para la aplicación web.
        - Asegúrese de que la opción **Permitir que los servicios de Azure accedan al servidor** esté seleccionada.
        - Haga clic en **Seleccionar**.
    
    - Haga clic en **Seleccionar**.

13. Haga clic en **Configuración de aplicación web**, especifique el nombre de usuario y la contraseña de la base de datos y haga clic en **Aceptar**.

    La configuración de CMS de Umbraco debe parecerse a la captura de pantalla siguiente:

    ![Configuración finalizada: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Haga clic en **Crear**.
    
    Ahora, Azure crea la aplicación de Umbraco según su configuración. Verá la notificación **Implementación iniciada...**

    ![La implementación finalizó correctamente: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Inicio y administración de la aplicación web de Umbraco

Cuando Azure finalice la implementación de aplicación, verá otra notificación.

![La implementación finalizó correctamente: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Haga clic en la notificación. Si se la ha perdido, siempre puede acceder a ella haciendo clic en la campana de notificación (![Notificación siguiente: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Ahora verá la [hoja](../azure-resource-manager/resource-group-portal.md#manage-resources) de administración de la aplicación web (*hoja*: una página del portal que se abre horizontalmente).

3. En la parte superior de la página de introducción, haga clic en **Examinar**.
   
    ![Examinar: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Ahora puede ver la página de **bienvenida** de Umbraco. Configure la instalación de Umbraco y ya puede comenzar a jugar.

    ![Configuración de Umbraco: primera aplicación de Umbraco en Azure App Service](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Pasos siguientes
* [Implementación de una aplicación web creada con ASP.NET en Azure App Service mediante Visual Studio](web-sites-dotnet-get-started.md): aprenda a crear una nueva aplicación web de Azure desde Visual Studio, con cualquiera de las plantillas de aplicación que se incluyen.
* [Implementación del código en Azure App Service](web-sites-deploy.md): aprenda a implementar desde FTP o desde repositorios de control del código fuente.
* [Incorporación de funcionalidad a su primera aplicación web](app-service-web-get-started-2.md): lleve su aplicación de Azure al siguiente nivel. Autentique los usuarios. Escálela según la demanda. Configure algunas alertas de rendimiento. Todo ello con unos cuantos clics.

