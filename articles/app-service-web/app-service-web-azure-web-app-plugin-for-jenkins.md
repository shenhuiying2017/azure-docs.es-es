---
title: "Uso del complemento Aplicación web de Azure con la integración continua de Jenkins | Microsoft Docs"
description: "Describe el uso del complemento Aplicación web de Azure con la integración continua de Jenkins."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Uso del complemento Aplicación web de Azure con la integración continua de Jenkins
El complemento Aplicación web de Azure para Jenkins facilita la creación de aplicaciones web en Azure cuando se ejecutan compilaciones distribuidas y la implementación de un archivo WAR en su aplicación web.

### <a name="prerequisites"></a>Requisitos previos
Antes de comenzar con los pasos descritos en este artículo, tendrá que registrar y autorizar la aplicación cliente y, a continuación, recuperar el identificador y el secreto de cliente que se enviarán a Azure Active Directory durante la autenticación. Para más información sobre estos requisitos previos, consulte los siguientes artículos:

* [Integración de aplicaciones con Azure Active Directory][integrate-apps-with-AAD]
* [Registro de una aplicación de cliente][register-client-app]

Además, será preciso que descargue el archivo **azure-webapp-plugin.hpi** de la siguiente dirección URL:

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Instalación del complemento Aplicación web de Azure para Jenkins
1. [Descargue el archivo **azure-webapp-plugin.hpi** de GitHub][azure-webapp-plugin-install]
2. Inicie sesión en el panel de Jenkins.
3. En el panel, haga clic en **Manage Jenkins**(Administrar Jenkins).
   
    ![Administración de Jenkins][jenkins-dashboard]
4. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Manage Plugins** (Administrar complementos).
   
    ![Administración de complementos][manage-jenkins]
5. Haga clic en la pestaña **Advanced** (Opciones avanzadas) y haga clic en **Browse** (Examinar) en la sección **Upload Plugin** (Cargar complemento). Navegue hasta la ubicación en la que descargó el archivo **azure-webapp-plugin.hpi** en los **requisitos previos** y haga clic en el botón **Upload** (Cargar) una vez que haya seleccionado el archivo.
   
    ![Cargar complemento][upload-plugin]
6. Si es necesario, reinicie Jenkins.

## <a name="configure-the-azure-web-app-plugin"></a>Configuración del complemento Aplicación web de Azure
1. En el panel de Jenkins, haga clic en uno de los proyectos.
   
    ![Seleccionar proyecto][select-project]
2. Cuando aparezca la página del proyecto, haga clic en **Configure** (Configurar) en el menú de la izquierda.
   
    ![Configurar proyecto][configure-project]
3. En la sección **Post-build Actions** (Acciones posteriores a la compilación), haga clic en el menú desplegable **Add post-build action** (Agregar acción posterior a la compilación) y seleccione **Azure Webapp Configuration** (Configuración de Webapp de Azure). 
   
    ![Opciones avanzadas][advanced-options]
4. Cuando aparezca la sección **Azure Webapp Configuration** (Configuración de Webapp de Azure), especifique la información de **Subscription ID** (Id. de suscripción), **Client ID** (Id. de cliente), **Client Secret** (Secreto de cliente) y **OAuth 2.0 Token Endpoint** (Punto de conexión de token de OAuth 2.0) en **Azure Profile Configuration** (Configuración del perfil de Azure).
   
    ![Configuración del perfil de Azure][azure-profile-configuration]
5. En la sección **Webapp Configuration** (Configuración de Webapp), especifique la información de **Resource Group Name** (Nombre de grupo de recursos), **Location** (Ubicación), **Hosting Plan Name** (Nombre de plan de hosting), **Web App Name** (Nombre de aplicación web), **Sku Name** (Nombre de SKU), **Sku Capacity** (Capacidad de SKU) y **War File Path** (Ruta de acceso de archivo WAR).
   
    ![Configuración de Webapp][webapp-configuration]
6. Haga clic en **Save** (Guardar) para guardar la configuración del proyecto.
   
    ![Guardar proyecto][save-project]
7. Haga clic en **Build Now** (Compilar ahora) en el menú de la izquierda.
   
    ![Compilar proyecto][build-project]

> [!NOTE]
> Solo se creará un contenedor de aplicaciones web cuando no exista.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para obtener información adicional acerca del complemento Aplicación web de Azure para Jenkins, consulte el proyecto [Azure Web App Plugin] (Complemento Aplicación web de Azure para Jenkins) en GitHub.

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[Azure Web App Plugin]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


