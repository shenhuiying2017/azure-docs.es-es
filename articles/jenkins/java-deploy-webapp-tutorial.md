---
title: Uso de Jenkins para implementar aplicaciones web en Azure | Microsoft Docs
description: "Configure la integración continua desde GitHub en Azure App Service para las aplicaciones web de Java con Jenkins y Docker."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 40d7e822b586e6f6b4addcd7d4e107eda9f4ab11
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Configuración de la integración continua y la implementación en Azure App Service con Jenkins

En este tutorial se configura la integración continua y la implementación (CI/CD) de una aplicación web de Java de ejemplo con el marco [Spring Boot](http://projects.spring.io/spring-boot/) en [Azure App Service Web App en Linux](/azure/app-service/containers/app-service-linux-intro) con Jenkins.

En este tutorial, realizará las siguientes tareas:

> [!div class="checklist"]
> * Instalar los complementos de Jenkins necesarios para realizar la integración en Azure App Service
> * Definir un trabajo Jenkins para crear imágenes de Docker desde un repositorio de GitHub cuando se envíe un nueva confirmación
> * Definir una nueva aplicación web de Azure para Linux y configurarla para implementar imágenes de Docker que se envían al registro de contenedor de Azure
> * Configurar el complemento de Jenkins de Azure App Service.
> * Implementar la aplicación de ejemplo en Azure App Service con una compilación manual.
> * Desencadenar una compilación de Jenkins y actualizar la aplicación web enviando cambios a GitHub.

## <a name="before-you-begin"></a>Antes de empezar

Para completar este tutorial, necesita:

* [Jenkins](https://jenkins.io/) con las herramientas JDK y Maven configuradas. Si no tiene un sistema Jenkins, cree uno ahora en Azure desde la [plantilla de solución de Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Una cuenta de [GitHub](https://github.com).
* La [CLI de Azure 2.0](/cli/azure/overview), ya sea desde la línea de comandos local o desde [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalación de los complementos de Jenkins

1. Abra un explorador web en la consola web de Jenkins y seleccione **Manage Jenkins** (Administrar Jenkins) en el menú izquierdo. Después, haga clic en **Manage Plugins** (Administrar complementos).
2. Seleccione la pestaña **Available** (Disponible).
3. Busque y marque la casilla de verificación situada junto a los complementos siguientes:   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service) (Complemento de Azure App Service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source) (Complemento de origen de bifurcación de GitHub)

    Si no aparecen, asegúrese de que no estén ya instalados consultando la pestaña **Installed** (Instalados).

1. Seleccione **Download now and install after restart** (Descargar ahora e instalar después del reinicio) para habilitar los complementos en la configuración de Jenkins.

## <a name="configure-github-and-jenkins"></a>Configuración de GitHub y Jenkins

Configure Jenkins para recibir [webhooks de GitHub](https://developer.github.com/webhooks/) cuando se envíen nuevas confirmaciones a un repositorio en su cuenta.

1. Seleccione **Manage Jenkins** (Administrar Jenkins) y, luego, **Configure System** (Configurar sistema). En la sección **GitHub**, asegúrese de que la opción **Manage hooks** (Administrar enlaces) esté seleccionada; después, elija **Manage additional GitHub actions** (Administrar acciones de GitHub adicionales) y haga clic en **Convert login and password to token** (Convertir contraseña e inicio de sesión en token).
2. Seleccione el botón de radio **From login and password** (A partir del inicio de sesión y la contraseña), y escriba el nombre de usuario y la contraseña de GitHub. Seleccione **Create token credentials** (Crear credenciales de token) para crear un nuevo [token de acceso personal de GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Creación de un PAT de GitHub a partir del inicio de sesión y la contraseña](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Seleccione el token recién creado en la lista desplegable **Credentials** (Credenciales) de la configuración de servidores de GitHub. Seleccione **Test connection** (Probar conexión) para comprobar que funciona la autenticación.   
   ![Comprobación de la conexión a GitHub una vez que se ha configurado el PAT](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Si la cuenta de GitHub tiene habilitada la autenticación de dos factores, cree el token en GitHub y configure Jenkins para poder usarlo. Consulte la documentación del [complemento de GitHub de Jenkins](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) para obtener todos los detalles.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Bifurcación del repositorio de ejemplo y creación de un trabajo de Jenkins 

1. Abra el [repositorio de la aplicación de ejemplo de Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) y bifúrquelo a su cuenta de GitHub seleccionando **Fork** (Bifurcar) en la esquina superior derecha.   
    ![Bifurcación desde GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. En la consola de web Jenkins, seleccione **New Item** (Nuevo elemento), asígnele el nombre **MyJavaApp**, elija **Freestyle project** (Proyecto de Freestyle) y haga clic en **OK** (Aceptar).   
    ![Nuevo proyecto de Freestyle de Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. En la sección **General**, seleccione el proyecto de **GitHub** y escriba la URL del repositorio bifurcado; por ejemplo, https://github.com/raisa/gs-spring-boot-docker.
3. En la sección **Source code management** (Administración de código fuente), seleccione **Git** y escriba la URL `.git` del repositorio bifurcado; por ejemplo, https://github.com/raisa/gs-spring-boot-docker.git.
4. En la sección **Build Triggers** (Compilar desencadenadores), seleccione **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para sondeo de GITScm).
5. En la sección **Build** (Compilar), seleccione **Add build step** (Agregar paso de compilación) y elija **Invoke top-level Maven targets** (Invocar destinos de Maven de nivel superior). Escriba `package` en el campo **Goals** (Objetivos).
6. Seleccione **Guardar**. Puede probar el trabajo seleccionando **Build Now** (Compilar ahora) en la página del proyecto.

## <a name="configure-azure-app-service"></a>Configuración de Azure App Service 

1. Mediante la CLI de Azure o [Cloud Shell](/azure/cloud-shell/overview), cree una nueva [aplicación web en Linux](/azure/app-service/containers/app-service-linux-intro). El nombre de la aplicación web en este tutorial es `myJavaApp`, pero debe usar un nombre único en su propia aplicación.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Cree una instancia de [Azure Container Registry](/azure/container-registry/container-registry-intro) para almacenar las imágenes de Docker que genera Jenkins. El nombre del registro de contenedor utilizado en este tutorial es `jenkinsregistry`, pero debe usar un nombre único para su propio registro de contenedor. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configure la aplicación web para ejecutar imágenes de Docker enviadas al registro de contenedor y especifique que la aplicación que se ejecuta en el contenedor escuche las solicitudes en el puerto 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Configuración del complemento de Jenkins de Azure App Service

1. En la consola de web Jenkins, seleccione el trabajo **MyJavaApp** que ha creado y, luego, haga clic en **Configure** (Configurar) en la parte izquierda de la página.
2. Desplácese hacia abajo hasta **Post-build Actions** (Acciones posteriores a la publicación), seleccione **Add post-build action** (Agregar acción posterior a la publicación) y elija **Publish an Azure Web App** (Publicar una aplicación web de Azure).
3. En **Azure Profile Configuration** (Configuración del perfil de Azure), seleccione **Add** (Agregar) junto a **Azure Credentials** (Credenciales de Azure) y elija **Jenkins**.
4. En el cuadro de diálogo **Add Credentials** (Agregar credenciales), seleccione la opción **Microsoft Azure Service Principal** (Entidad de servicio de Microsoft Azure) del menú desplegable **Kind** (Variante).
5. Cree una entidad de servicio de Active Directory desde la CLI de Azure o [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Escriba las credenciales de la entidad de servicio en el cuadro de diálogo **Add credentials** (Agregar credenciales). Si no conoce el identificador de la suscripción de Azure, puede consultarlo desde la CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configuración de una entidad de servicio de Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Compruebe que la entidad de servicio se autentica con Azure seleccionando **Verify Service Principal** (Comprobar entidad de servicio). 
7. Seleccione **Add** (Agregar) para guardar las credenciales.
8. Seleccione la credencial de la entidad de servicio que acaba de agregar en el menú desplegable **Azure Credentials** (Credenciales de Azure) cuando se encuentre de nuevo en la configuración **Publish an Azure Web App** (Publicar una aplicación web de Azure).
9. En **App Configuration** (Configuración de la aplicación), elija el nombre del grupo de recursos y de la aplicación en la lista desplegable web.
10. Seleccione el botón de radio **Publish via Docker** (Publicar a través de Docker).
11. Escriba `complete/Dockerfile` para **ruta de acceso del archivo de Docker**.
12. Escriba `https://jenkinsregistry.azurecr.io` en el campo **Docker registry URL** (URL del registro de Docker).
13. Seleccione **Add** (Agregar) junto a **Registry Credentials** (Credenciales de registro). 
14. Escriba el nombre de usuario de administrador de Azure Container Registry que creó para el **nombre de usuario**.
15. Escriba la contraseña de Azure Container Registry en el campo **Password** (Contraseña). Puede obtener el nombre de usuario y la contraseña desde Azure Portal mediante el siguiente comando de la CLI:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Adición de las credenciales del registro de contenedor](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Seleccione **Add** (Agregar) para guardar la credencial.
16. Seleccione la credencial recién creada en la lista desplegable **Registry credentials** (Credenciales de registro) en el panel **App Configuration** (Configuración de la aplicación) de **Publish an Azure Web App** (Publicar una aplicación web de Azure). La acción posterior a la compilación finalizada debería ser similar a la siguiente imagen:   
    ![Configuración de la acción posterior a la compilación para la implementación en Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Para guardar la configuración del trabajo, haga clic en **Save** (Guardar).

## <a name="deploy-the-app-from-github"></a>Implementación de la aplicación desde GitHub

1. En el proyecto de Jenkins, seleccione **Build now** (Compilar ahora) para implementar la aplicación de ejemplo en Azure.
2. Una vez finalizada la compilación, la aplicación estará activa en Azure mediante la URL de publicación, por ejemplo, http://myjavaapp.azurewebsites.net.   
   ![Visualización de la aplicación implementada en Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Confirmación de cambios y reimplementación

1. En la bifurcación de Github, busque `complete/src/main/java/Hello/Application.java`. Seleccione el vínculo **Edit this file** (Editar este archivo) en el lado derecho de la interfaz de GitHub.
2. Realice el siguiente cambio en el método `home()` y confirmar el cambio en la bifurcación principal del repositorio.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Se iniciará una nueva compilación de Jenkins desencadenada mediante la confirmación nueva de la bifurcación `master` del repositorio. Una vez que se complete, vuelva a cargar la aplicación en Azure.     
      ![Visualización de la aplicación implementada en Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Pasos siguientes

- [Usar máquinas virtuales de Azure como agentes de compilación](/azure/jenkins/jenkins-azure-vm-agents)
- [Administrar recursos en trabajos y canalizaciones con la CLI de Azure](/azure/jenkins/execute-cli-jenkins-pipeline)
 
