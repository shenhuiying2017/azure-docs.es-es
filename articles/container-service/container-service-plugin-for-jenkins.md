---
title: Complemento Azure Container Service para Jenkins | Microsoft Docs
description: "Implemente un contenedor de docker en un servicio de clúster de Azure Container Service mediante el complemento Azure Container Service para Jenkins."
services: container-service
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
tags: azure-container-service, jenkins
keywords: Azure, contenedores, Jenkins
ms.assetid: dd719bda-6d1f-452b-a918-9a3aa9302107
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 435262867e8ead23d1453e8a2171f1c215fc5205


---
# <a name="azure-container-service-plugin-for-jenkins"></a>Complemento Azure Container Service para Jenkins
El complemento Azure Container Service para Jenkins permite simplificar el proceso de implementación de contenedores [Docker] mediante [Marathon] para un clúster de contenedor de Azure que ejecuta un sistema de procesamiento distribuido, como el [sistema operativo de centro de datos de Mesosphere (DC/OS)][mesosphere] o [Apache Mesos][mesos].

Este tutorial muestra cómo instalar el complemento Azure Container Service para Jenkins y cómo configurar un proyecto de Jenkins que cree una instancia de Azure Container Service con Marathon y un orquestador de DC/OS, y usar Marathon para implementar un contenedor de Docker en el clúster. Si el clúster de Azure Container Service no existe, Jenkins creará el clúster de contenedor y usará Marathon para implementar el contenedor de docker en el clúster cuando esté creado; de lo contrario, Jenkins implementará el contenedor de docker en el clúster de Azure Container Service existente mediante Marathon.

Para más información sobre Azure Container Service, consulte el artículo [Presentación de Azure Container Service][acs-intro].

### <a name="prerequisites"></a>Requisitos previos
Antes de comenzar con los pasos descritos en este artículo, tendrá que registrar y autorizar la aplicación cliente y, a continuación, recuperar el identificador y el secreto del cliente, que se enviarán a Azure Active Directory durante la autenticación. Para más información sobre estos requisitos previos, consulte los siguientes artículos:

* [Integración de aplicaciones con Azure Active Directory][integrate-apps-with-AAD]
* [Registro de una aplicación de cliente][register-client-app]

Además, será preciso que descargue el archivo **azure-acs-plugin.hpi** de la siguiente dirección URL:

* [https://github.com/Microsoft/azure-acs-plugin/tree/master/install](https://github.com/Microsoft/azure-acs-plugin/tree/master/install)

## <a name="how-to-install-the-azure-container-service-plugin-for-jenkins"></a>Instalación del complemento Azure Container Service para Jenkins
1. [Descargue el archivo **azure-acs-plugin.hpi** de GitHub][azure-acs-plugin-install].
2. Inicie sesión en el panel de Jenkins.
3. En el panel, haga clic en **Manage Jenkins**(Administrar Jenkins).
   
    ![Administración de Jenkins][jenkins-dashboard]
4. En la página **Manage Jenkins** (Administrar Jenkins), haga clic en **Manage Plugins** (Administrar complementos).
   
    ![Administración de complementos][manage-jenkins]
5. Haga clic en la pestaña **Advanced** (Opciones avanzadas) y haga clic en **Browse** (Examinar) en la sección **Upload Plugin** (Cargar complemento). Vaya a la ubicación desde la que descargó el archivo **azure-acs-plugin.hpi** en los **requisitos previos** y haga clic en el botón **Upload** (Cargar) una vez que haya seleccionado el archivo.
   
    ![Cargar complemento][upload-plugin]
6. Si es necesario, reinicie Jenkins.

## <a name="configure-the-azure-container-service-plugin"></a>Configuración del complemento Azure Container Service
1. En el panel de Jenkins, haga clic en uno de los proyectos.
   
    ![Seleccionar proyecto][select-project]
2. Cuando aparezca la página del proyecto, haga clic en **Configure** (Configurar) en el menú de la izquierda.
   
    ![Configurar proyecto][configure-project]
3. En la sección **Post-build Actions** (Acciones posteriores a la compilación), haga clic en el menú desplegable **Add post-build action** (Agregar acción posterior a la compilación) y seleccione **Azure Container Service Configuration** (Configuración de Azure Container Service). 
   
    ![Opciones avanzadas][advanced-options]
4. Cuando aparezca la sección **Azure Container Service Configuration** (Configuración de Azure Container Service), especifique la información de identificador de suscripción, identificador de cliente, secreto de cliente y punto de conexión de token de OAuth 2.0.
   
    ![Configuración de Azure Container Service][azure-container-service-config]
5. En la sección **ACS Profile Configuration** (Configuración del perfil de ACS), especifique su región, el prefijo de nombre de DNS, el número de agentes, el tamaño de la máquina virtual del agente, el nombre de usuario del administrador, el número de patrones y la clave pública RSA de SSH.
   
    ![Configuración del perfil de ACS][acs-profile-configuration]
6. En la sección **Marathon Profile Configuration** (Configuración del perfil de Marathon), escriba la ruta de acceso al archivo de configuración de Marathon, la ruta de acceso al archivo privado RSA de SSH y la contraseña del archivo privado RSA de SSH.
   
    ![Configuración de perfil de Marathon][marathon-profile-configuration]
7. Haga clic en **Save** (Guardar) para guardar la configuración del proyecto.
   
    ![Guardar proyecto][save-project]
8. Haga clic en **Build Now** (Compilar ahora) en el menú de la izquierda.
   
    ![Compilar proyecto][build-project]

Una vez finalizada la compilación, los registros estarán disponibles en los registros de la consola de compilaciones.

> [!NOTE]
> Si el clúster de Azure Container Service no existe, Jenkins creará el clúster de contenedor y usará Marathon para implementar el contenedor de docker en el clúster cuando esté creado; de lo contrario, Jenkins implementará el contenedor de docker en el clúster de Azure Container Service existente mediante Marathon.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para más información acerca de cómo trabajar con clústeres de DC/OS y Azure Container Service, consulte los artículos siguientes:

* [Presentación de Azure Container Service][acs-intro]
* [Implementación de un clúster de Azure Container Service][acs-deploy]
* [Conexión a un clúster de Azure Container Service][acs-connect]
* [Administración de contenedores a través de la interfaz de usuario web][acs-webui-management]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[azure-acs-plugin-install]: https://github.com/Microsoft/azure-acs-plugin/tree/master/install
[acs-intro]: ./container-service-intro.md
[acs-deploy]: ./container-service-deployment.md
[acs-connect]: ./container-service-connect.md
[acs-webui-management]: ./container-service-mesos-marathon-ui.md
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx

[Marathon]: https://mesosphere.github.io/marathon/
[Docker]: http://docker.io/
[mesosphere]: https://mesosphere.com/products/
[mesos]: https://mesos.apache.org/

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[jenkins-dashboard]: ./media/container-service-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]: ./media/container-service-plugin-for-jenkins/manage-jenkins.png
[search-plugins]: ./media/container-service-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/container-service-plugin-for-jenkins/install-plugin.png
[select-project]: ./media/container-service-plugin-for-jenkins/select-project.png
[configure-project]: ./media/container-service-plugin-for-jenkins/configure-project.png
[advanced-options]: ./media/container-service-plugin-for-jenkins/advanced-options.png
[azure-container-service-config]: ./media/container-service-plugin-for-jenkins/azure-container-service-configuration.png
[acs-profile-configuration]: ./media/container-service-plugin-for-jenkins/acs-profile-configuration.png
[marathon-profile-configuration]: ./media/container-service-plugin-for-jenkins/marathon-profile-configuration.png
[save-project]: ./media/container-service-plugin-for-jenkins/save-project.png
[build-project]: ./media/container-service-plugin-for-jenkins/build-project.png
[upload-plugin]: ./media/container-service-plugin-for-jenkins/upload-plugin.png



<!--HONumber=Nov16_HO3-->


