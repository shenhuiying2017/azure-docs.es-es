---
title: "Implementación en Azure App Service mediante el complemento de Jenkins | Microsoft Docs"
description: "Aprenda a usar el complemento de Jenkins de Azure App Service para implementar una aplicación web de Java para Azure en Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 9b79e3b498e51e626e7e9a87d2bb1a66366acff5
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Implementación en Azure App Service mediante el complemento de Jenkins 

Para implementar una aplicación web de Java en Azure, puede utilizar la CLI de Azure en una [canalización de Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) o utilizar el [complemento de Jenkins de Azure App Service](https://plugins.jenkins.io/azure-app-service). La versión 1.0 del complemento de Jenkins es compatible con la implementación continua mediante la característica Web Apps de Azure App Service a través de lo siguiente:
* Git o FTP.
* Docker para Web Apps en Linux

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Configurar Jenkins para implementar Web Apps mediante Git o FTP.
> * Configurar Jenkins para implementar Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Creación y configuración de una instancia de Jenkins

Si aún no tiene un servidor maestro de Jenkins, comience con la [plantilla de la solución](install-jenkins-solution-template.md), que incluye la versión 8 de Java Development Kit (JDK) y los siguientes complementos de Jenkins necesarios:

* [Complemento de cliente de Git de Jenkins](https://plugins.jenkins.io/git-client), versión 2.4.6 
* [Complemento Docker Commons](https://plugins.jenkins.io/docker-commons), versión 1.4.0
* [Credenciales de Azure](https://plugins.jenkins.io/azure-credentials), versión 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server), versión 0.1

Puede usar el complemento de Jenkins para implementar una aplicación web en cualquier lenguaje que sea compatible con Web Apps, como C#, PHP, Java y Node.js. En este tutorial, usamos una [sencilla aplicación web de Java para Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar el repositorio en su propia cuenta de GitHub, seleccione el botón **Bifurcar** de la esquina superior derecha de la interfaz de GitHub.  
> [!NOTE]
> Son necesarios Java JDK y Maven para compilar el proyecto de Java. Instale estos componentes en el servidor maestro de Jenkins o en el agente de la máquina virtual si usa el agente para la integración continua. 

Para instalar los componentes, inicie sesión en la instancia de Jenkins con SSH y ejecute los siguientes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para implementar en Web App for Containers, instale Docker en el servidor maestro de Jenkins o en el agente de VM que se usa para la compilación. Para obtener las instrucciones de instalación, consulte [Install Docker on Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/) (Instalación de Docker en Ubuntu).

##<a name="service-principal"></a> Adición de una entidad de servicio de Azure a las credenciales de Jenkins

Se necesita una entidad de servicio de Azure para implementar en Azure. 


1. Use la [CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) o [Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal) para crear una entidad de servicio de Azure.
2. En el panel de Jenkins, seleccione **Credentials** (Credenciales) > **System**(Sistema). A continuación, haga clic en **Global credentials(unrestricted)** (Credenciales globales [sin restricción]).
3. Para agregar una entidad de servicio de Microsoft Azure, seleccione **Add Credentials** (Agregar credenciales). Proporcione valores en los campos **Subscription ID** (Id. de suscripción), **Client ID** (Id. de cliente), **Client Secret** (Secreto de cliente) y **OAuth 2.0 Token Endpoint** (Punto de conexión de token de OAuth 2.0). Configure el campo **ID** (Identificador) con el valor **mySp**. Utilizaremos este identificador en pasos posteriores de este artículo.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Configuración de Jenkins para implementar Web Apps mediante la carga de archivos

Para implementar el proyecto en Web Apps, puede cargar los artefactos de compilación (por ejemplo, el archivo WAR en Java) usando Git o FTP.

Antes de configurar el trabajo en Jenkins, necesitará un plan de Azure App Service y una aplicación web para ejecutar la aplicación de Java.


1. Cree un plan de Azure App Service con el plan de tarifa **GRATIS** mediante el `az appservice plan create` [comando de la CLI de Azure](/cli/azure/appservice/plan#create). Un plan de App Service define los recursos físicos que se usan para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten estos recursos. Los recursos compartidos le ayudan a ahorrar costos al hospedar varias aplicaciones.
2. Cree una aplicación web. Puede usar [Azure Portal](/azure/app-service-web/web-sites-configure) o el siguiente `az`comando de la CLI de Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Establezca la configuración en tiempo de ejecución de Java que necesita la aplicación. El siguiente comando de la CLI de Azure configura la aplicación web para que se ejecute en una versión reciente de JDK 8 y en [Apache Tomcat](http://tomcat.apache.org/) 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Configuración del trabajo de Jenkins

1. Cree un nuevo proyecto **freestyle** (estilo libre) en el panel de Jenkins.
2. Configure **Source Code Management** (Administración de código fuente) para usar la bifurcación local de la [sencilla aplicación web de Java para Azure](https://github.com/azure-devops/javawebappsample). Proporcione el valor de **Repository URL** (Dirección URL de repositorio). Por ejemplo: http://github.com/&lt;su_ID>/javawebappsample.
3. Agregue un paso para compilar el proyecto mediante el uso de Maven agregando el comando **Execute shell**. En este ejemplo, es necesario un comando adicional para cambiar el nombre del archivo \*.war en la carpeta de destino a **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Agregue una acción posterior a la compilación seleccionando **Publish an Azure Web App** (publicar una aplicación web de Azure).
5. Indique **mySp** como entidad de servicio de Azure. Esta entidad se almacenó como [Credenciales de Azure](#service-principal) en un paso anterior.
6. En la sección **App Configuration** (Configuración de la aplicación), elija la aplicación web y el grupo de recursos de la suscripción. El complemento de Jenkins detecta automáticamente si la aplicación web está basada en Windows o Linux. Para una aplicación web basada en Windows, se presenta la opción **Publish Files** (Publicar archivos).
7. Complete los archivos que se van a implementar. Por ejemplo, especifique el paquete WAR si utiliza Java. Utilice los parámetros opcionales **Source Directory** (Directorio de origen) y **Target Directory** (Directorio de destino) para especificar las carpetas de origen y de destino que se usarán para cargar el archivo. La aplicación web de Java en Azure se ejecuta en un servidor de Tomcat. Así pues, para Java, cargue el paquete WAR en la carpeta de aplicaciones web. En este ejemplo, establezca el valor de **Source Directory** (Directorio de origen) en **target** (Destino) y el valor de **Target Directory** (Directorio de destino) en **webapps**.
8. Si desea implementar en una ranura que no sea de producción, también puede establecer un nombre para **Slot** (ranura).
9. Guarde y compile el proyecto. La aplicación web se implementa en Azure cuando finaliza la compilación.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Implementación de Web Apps mediante la carga de archivos con la canalización de Jenkins

El complemento de Jenkins de Azure App Service es compatible con la canalización. Puede consultar el siguiente ejemplo en el repositorio de GitHub.

1. En la interfaz de GitHub, abra el archivo **Jenkinsfile_ftp_plugin**. Para editar el archivo, seleccione el icono de lápiz. Actualice las definiciones de **resourceGroup** y **webAppName** para la aplicación web en las líneas 11 y 12, respectivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Establezca la definición de **withCredentials** en la línea 14 con el identificador de la credencial de la instancia de Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Creación de una canalización de Jenkins

1. Abra Jenkins en un explorador web. Seleccione **Nuevo elemento**.
2. Proporcione un nombre para el trabajo y seleccione **Pipeline** (Canalización). Seleccione **Aceptar**.
3. Seleccione la pestaña **Pipeline** (Canalización).
4. En el valor **Definition** (Definición), seleccione **Pipeline script from SCM** (Script de canalización del SCM).
5. Para el valor de **SCM**, seleccione **Git**. Escriba la dirección URL de GitHub para el repositorio bifurcado. Por ejemplo: https://&lt;su_repositorio_bifurcado>.git.
6. Actualice el valor **Script Path** (Ruta de acceso del script) con **Jenkinsfile_ftp_plugin**.
7. Haga clic en **Save** (guardar) y ejecute el trabajo.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configuración de Jenkins para implementar Web App for Containers

Web Apps en Linux admite la implementación mediante el uso de Docker. Para implementar su aplicación web con Docker, debe proporcionar un archivo de Docker que empaquete la aplicación web con el servicio en tiempo de ejecución en una imagen de Docker. Después, el complemento de Jenkins compila la imagen, la inserta en un registro de Docker y la implementa en la aplicación web.

Web Apps en Linux también admite los métodos de implementación tradicionales, como Git y FTP, pero solo para los lenguajes integrados (.NET Core, Node.js, PHP y Ruby). Para otros lenguajes, debe empaquetar el código de la aplicación y el servicio en tiempo de ejecución juntos en una imagen de Docker y utilizar Docker para la implementación.

Antes de configurar el trabajo en Jenkins, necesita una aplicación web en Linux. También es necesario un registro de contenedor para almacenar y administrar las imágenes del contenedor de Docker privado. Puede usar DockerHub para crear el registro de contenedor. En este ejemplo, se utiliza Azure Container Registry.

* [Cree la aplicación web en Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry es un servicio administrado de [Docker Registry](https://docs.docker.com/registry/) basado en la versión 2.0 de Docker Registry de código abierto. [Cree una instancia de Azure Container Registry](/azure/container-registry/container-registry-get-started-azure-cli). También puede usar DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Configuración del trabajo de Jenkins para Docker

1. Cree un nuevo proyecto **freestyle** (estilo libre) en el panel de Jenkins.
2. Configure **Source Code Management** (Administración de código fuente) para usar la bifurcación local de la [sencilla aplicación web de Java para Azure](https://github.com/azure-devops/javawebappsample). Proporcione el valor de **Repository URL** (Dirección URL de repositorio). Por ejemplo: http://github.com/&lt;su_ID>/javawebappsample.
3. Agregue un paso para compilar el proyecto mediante el uso de Maven agregando un comando **Execute shell**. En el comando, incluya la línea siguiente:
    ```bash
    mvn clean package
    ```

4. Agregue una acción posterior a la compilación seleccionando **Publish an Azure Web App** (publicar una aplicación web de Azure).
5. Indique **mySp** como entidad de servicio de Azure. Esta entidad se almacenó como [Credenciales de Azure](#service-principal) en un paso anterior.
6. En la sección **App Configuration** (Configuración de la aplicación), elija la aplicación web en Linux y el grupo de recursos de la suscripción.
7. Elija **Publish via Docker** (Publicar a través de Docker).
8. Rellene el valor de la ruta de acceso de **Dockerfile**. Puede mantener el valor predeterminado /Dockerfile.
Para el valor de **Docker registry URL** (Dirección URL de registro del Docker), proporcione la dirección URL usando el formato https://&lt;suRegistro >. azurecr.io si utiliza Azure Container Registry. Si utiliza DockerHub, deje el valor en blanco.
9. En el valor **Registry credentials** (Credenciales del registro), agregue las credenciales del registro del contenedor. Puede obtener el identificador de usuario y la contraseña mediante la ejecución de los siguientes comandos en la CLI de Azure. El primer comando habilita la cuenta de administrador:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. El nombre de la imagen de Docker y el valor de la etiqueta en la pestaña **Advanced** (Avanzado) son opcionales. De forma predeterminada, el nombre de la imagen se obtiene del nombre de imagen que configuró en Azure Portal, en el ajuste **Contenedor de Docker**. La etiqueta se genera a partir de $BUILD_NUMBER.
    > [!NOTE]
    > Asegúrese de especificar el nombre de la imagen en Azure Portal o bien proporcione un valor para **Docker Image** (Imagen de Docker) en la pestaña **Advanced** (Avanzado). En este ejemplo, establezca el valor **Docker Image** (Imagen de Docker) en &lt;su_Registro>.azurecr.io/calculator y deje el valor **Docker Image Tag** (Etiqueta de imagen de Docker) en blanco.

11. Se produce un error en la implementación si utiliza la configuración de imagen de Docker integrada. Cambie la configuración de Docker para usar la imagen personalizada en el ajuste **Docker Container** (Contenedor de Docker) de Azure Portal. Para la imagen integrada, utilice el enfoque de carga de archivos para la implementación.
12. De forma similar al enfoque de carga de archivos, puede elegir un valor en **Slot** (Ranura) diferente de **Production** (Producción).
13. Guarde y compile el proyecto. Verá que la imagen del contenedor se inserta en el registro y se implementa la aplicación web.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Implementación de Web App for Containers mediante la canalización de Jenkins

1. En la interfaz de GitHub, abra el archivo **Jenkinsfile_container_plugin**. Para editar el archivo, seleccione el icono de lápiz. Actualice las definiciones de **resourceGroup** y **webAppName** para la aplicación web en las líneas 11 y 12, respectivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Cambie en la línea 13 su servidor de registro de contenedor:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Cambie la línea 16 para utilizar el identificador de la credencial en la instancia de Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Creación de una canalización de Jenkins    

1. Abra Jenkins en un explorador web. Seleccione **Nuevo elemento**.
2. Proporcione un nombre para el trabajo y seleccione **Pipeline** (Canalización). Seleccione **Aceptar**.
3. Seleccione la pestaña **Pipeline** (Canalización).
4. En el valor **Definition** (Definición), seleccione **Pipeline script from SCM** (Script de canalización del SCM).
5. Para el valor de **SCM**, seleccione **Git**. Escriba la dirección URL de GitHub para el repositorio bifurcado. Por ejemplo: https://&lt;su_repositorio_bifurcado>.git.
7. Actualice el valor de **Script Path** (Ruta de acceso del script) a **Jenkinsfile_container_plugin**.
8. Haga clic en **Save** (guardar) y ejecute el trabajo.

## <a name="verify-your-web-app"></a>Comprobación de la aplicación web

1. Para comprobar que el archivo WAR se ha implementado correctamente en la aplicación web, abra un explorador web.
2. Vaya a http://&lt;nombre_de_su_aplicación>.azurewebsites.net/api/calculator/ping. Sustituya &lt;nombre_de_su_aplicación > por el nombre de la aplicación web. Verá el mensaje:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Vaya a http://&lt;nombre_de_su_aplicación>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Reemplace &lt;x> e &lt;y> por cualquier número para obtener la suma de x+y. La calculadora muestra la suma: ![Calculadora: sumar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Para Azure App Service en Linux

1. Para comprobar la aplicación web, ejecute el siguiente comando en la CLI de Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Se muestra el mensaje siguiente:
    ```CLI
    ["calculator"]
    ```
    
2. Vaya a http://&lt;nombre_de_su_aplicación>.azurewebsites.net/api/calculator/ping. Sustituya &lt;nombre_de_su_aplicación > por el nombre de la aplicación web. Verá el mensaje: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Vaya a http://&lt;nombre_de_su_aplicación>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Reemplace &lt;x> e &lt;y> por cualquier número para obtener la suma de x+y.
    
## <a name="next-steps"></a>pasos siguientes

En este tutorial se utiliza el complemento de Jenkins de Azure App Service para implementar en Azure.

Ha aprendido a:

> [!div class="checklist"]
> * Configurar Jenkins para implementar en Azure App Service a través de FTP 
> * Configuración de Jenkins para implementar Web App for Containers 
