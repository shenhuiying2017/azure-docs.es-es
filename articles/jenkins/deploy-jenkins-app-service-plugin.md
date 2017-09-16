---
title: "Implementación en Azure App Service con el complemento de Jenkins | Microsoft Docs"
description: "Aprenda a usar el complemento de Jenkins de Azure App Service para implementar una aplicación web de Java para Azure en Jenkins"
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
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b2035d6bc0d323f2497a1db9b88d3ed015235b16
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Implementación en Azure App Service con el complemento de Jenkins 
Para implementar una aplicación web de Java en Azure, puede utilizar la CLI de Azure en una [canalización de Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) o utilizar el [complemento de Jenkins de Azure App Service](https://plugins.jenkins.io/azure-app-service). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar Jenkins para implementar en Azure App Service a través de FTP 
> * Configurar Jenkins para implementar en Azure App Service en Linux con Docker 

## <a name="create-and-configure-jenkins-instance"></a>Creación y configuración de una instancia de Jenkins
Si aún no tiene un servidor maestro de Jenkins, comience con la [Solution Template](install-jenkins-solution-template.md) (Plantilla de la solución), que incluye JDK8 y los siguientes complementos necesarios:

* [Complemento de cliente de Git de Jenkins](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Complemento Docker Commons](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Credenciales de Azure](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) v.0.1

Puede usar el complemento de App Service para implementar aplicaciones web en todos los lenguajes (por ejemplo, C#, PHP, Java y node.js, etc.) admitidos por Azure App Service. En este tutorial, estamos utilizando la aplicación de Java de ejemplo, [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar el repositorio en su propia cuenta de GitHub, haga clic en el botón **Fork** (Bifurcar) de la esquina superior derecha.  

Son necesarios Java JDK y Maven para compilar el proyecto de Java. Asegúrese de instalar los componentes en el servidor maestro de Jenkins o en el agente de la máquina virtual si usa uno para la integración continua. 

Para la instalación, inicie sesión en la instancia de Jenkins con SSH y ejecute los siguientes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para la implementación en App Service en Linux, también debe instalar Docker en el servidor maestro de Jenkins o en el agente de la máquina virtual que se usa para la compilación. Consulte este artículo para instalar Docker: https://docs.docker.com/engine/installation/linux/ubuntu/.

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adición de un nombre de entidad de servicio de Azure a la credencial de Jenkins

Se necesita una entidad de servicio de Azure para implementar en Azure. 

<ol>
<li>Use la [CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) o [Azure Portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal) para crear una entidad de servicio de Azure</li>
<li>En el panel de Jenkins, haga clic en **Credenciales -> System ->**(Credenciales -> Sistema). Haga clic en **Global credentials(unrestricted)**  [Credenciales (sin restricción) globales].</li>
<li>Haga clic en **Add Credentials** (Agregar credenciales) para agregar una entidad de servicio de Microsoft Azure rellenando los datos correspondientes a Subscription ID (Identificador de suscripción), Client ID (Identificador de cliente), Client Secret (Secreto de cliente) y OAuth 2.0 Token Endpoint (Punto de conexión de token OAuth 2.0). Proporcione un identificador, **mySp**, para su uso en los siguientes pasos.</li>
</ol>

## <a name="azure-app-service-plugin"></a>Complemento de Azure App Service

El complemento de Azure App Service v1.0 admite la implementación continua de una aplicación web en Azure a través de:

* Git y FTP
* Docker para aplicaciones web en Linux

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Configuración de Jenkins para implementar una aplicación web a través de FTP mediante el panel de Jenkins

Para implementar el proyecto de aplicación web de Azure, puede cargar los artefactos de compilación (por ejemplo, el archivo .war en Java) usando Git o FTP.

Antes de configurar el trabajo en Jenkins, necesitará un plan de Azure App Service y una aplicación web para ejecutar la aplicación de Java.


1. Cree un plan de Azure App Service con el plan de tarifa **GRATIS** mediante el comando [az appservice plan create](/cli/azure/appservice/plan#create) de la CLI. Un plan de servicio de aplicaciones define los recursos físicos que se usan para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de servicio de aplicaciones comparten los recursos, lo que permite ahorrar costos al hospedar varias aplicaciones.
2. Creación de una aplicación web. Puede utilizar [Azure Portal](/azure/app-service-web/web-sites-configure) o bien el siguiente comando de la CLI de Azure:
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. Asegúrese de establecer la configuración en tiempo de ejecución de Java que necesita la aplicación. El siguiente comando de la CLI de Azure configura la aplicación web para que se ejecute en una versión reciente de Java 8 JDK y en [Apache Tomcat](http://tomcat.apache.org/) 8.0.
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Configuración del trabajo de Jenkins


1. Creación de un nuevo proyecto **freestyle** (estilo libre) en el panel de Jenkins
2. Configure **Source Code Management** (administración de código fuente) para usar la bifurcación local de [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) proporcionando la **dirección URL del repositorio**. Por ejemplo: http://github.com/&lt;su_ID>/javawebappsample.
3. Agregue un paso de compilación para compilar el proyecto con Maven. Para ello, agregue un paso **Execute shell** (ejecutar shell). En este ejemplo, es necesario un paso adicional para cambiar el nombre del archivo *.war en la carpeta de destino a ROOT.war.   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. Agregue una acción posterior a la compilación seleccionando **Publish an Azure Web App** (publicar una aplicación web de Azure).
5. Escriba "mySp", la entidad de servicio de Azure que se guardó en un paso anterior.
6. En la sección **App Configuration** (configuración de la aplicación), elija la aplicación web y el grupo de recursos de la suscripción. El complemento detecta automáticamente si la aplicación web está basada en Windows o Linux. Para una aplicación web basada en Windows, se presenta la opción "Publish Files" (publicar archivos).
7. Rellene los archivos que desea implementar (por ejemplo, un paquete war si usa Java.) Source Directory (directorio de origen) y Target Directory (directorio de destino) son opcionales. Los parámetros permiten especificar las carpetas de origen y destino al cargar archivos. La aplicación web de Java en Azure se ejecuta en un servidor de Tomcat. Por lo tanto, cargue el paquete war a la carpeta webapps. En este ejemplo, establezca **Source Directory** (directorio de origen) en "target" y **Target Directory** (directorio de destino) en "webapps".
8. Si desea implementar en una ranura que no sea de producción, también puede establecer un nombre para **Slot** (ranura).
9. Guarde y compile el proyecto. La aplicación web se implementa en Azure cuando finaliza la compilación.

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Implementación de la aplicación web a través de FTP mediante la canalización de Jenkins

El complemento está preparado para la canalización. Puede hacer referencia a un ejemplo en el repositorio de GitHub.

1. En la interfaz de usuario web de GitHub, abra el archivo **Jenkinsfile_ftp_plugin**. Haga clic en el icono de lápiz para editar este archivo a fin de actualizar el grupo de recursos y el nombre de la aplicación web en las líneas 11 y 12, respectivamente.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Cambie la línea 14 para actualizar el identificador de la credencial en la instancia de Jenkins.    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Creación de una canalización de Jenkins

1. Abra Jenkins en un explorador web, haga clic en **New Item** (Nuevo elemento).
2. Proporcione un nombre para el trabajo y seleccione **Pipeline** (Canalización). Haga clic en **Aceptar**.
3. Haga clic en la pestaña **Pipeline** (Canalización) a continuación.
4. En **Definition** (Definición), seleccione **Pipeline script from SCM** (Script de canalización del SCM).
5. En **SCM**, seleccione **Git**. Escriba la dirección URL de GitHub para el repositorio bifurcado: https:&lt;su repositorio bifurcado>.git
6. Actualice **Script Path** (ruta de acceso del script) con el valor "Jenkinsfile_ftp_plugin"
7. Haga clic en **Save** (guardar) y ejecute el trabajo.

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Configuración de Jenkins para implementar una aplicación web en Linux con Docker

Además de con Git y FTP, la aplicación web en Linux admite la implementación con Docker. Para la implementación con Docker, debe proporcionar un archivo de Docker que empaquete la aplicación web con el servicio en tiempo de ejecución en una imagen de Docker. Después, el complemento compila la imagen, la inserta en un registro de Docker y la implementa en la aplicación web.

La aplicación web en Linux también admite los métodos tradicionales como Git y FTP, pero solo para los lenguajes integrados (.NET Core, Node.js, PHP y Ruby). Para otros lenguajes, debe empaquetar el código de la aplicación y el servicio en tiempo de ejecución juntos en una imagen de Docker y utilizar Docker para la implementación.

Antes de configurar el trabajo en Jenkins, necesita una instancia de Azure App Service en Linux. También es necesario un registro de contenedor para almacenar y administrar las imágenes del contenedor de Docker privado. Puede usar DockerHub; en este ejemplo, usamos Azure Container Registry.

* Siga los pasos descritos [aquí](../app-service/containers/quickstart-nodejs.md) para crear una aplicación web en Linux 
* Azure Container Registry es un servicio administrado de [Docker registry] (https://docs.docker.com/registry/) basado en Docker Registry 2.0 de código abierto. Siga los pasos descritos [aquí] (/ azure/container-registry/container-registry-get-started-azure-cli) para obtener instrucciones sobre cómo hacerlo. También puede usar DockerHub.

### <a name="to-deploy-using-docker"></a>Para implementar con Docker:

1. Cree un nuevo proyecto freestyle (estilo libre) en el panel de Jenkins.
2. Configure **Source Code Management** (administración de código fuente) para usar la bifurcación local de [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) proporcionando la **dirección URL del repositorio**. Por ejemplo: http://github.com/&lt;su_ID>/javawebappsample.
Agregue un paso de compilación para compilar el proyecto con Maven. Para hacerlo, agregue un paso **Execute shell** (ejecutar shell) y agregue la siguiente línea en **Command** (comando):    
```bash
mvn clean package
```

3. Agregue una acción posterior a la compilación seleccionando **Publish an Azure Web App** (publicar una aplicación web de Azure).
4. Escriba **mySp**, la entidad de servicio de Azure que guardó en un paso anterior como credenciales de Azure.
5. En la sección **App Configuration** (configuración de la aplicación), elija la aplicación web en Linux y el grupo de recursos de la suscripción.
6. Elija Publish via Docker (publicar a través de Docker).
7. Rellene la ruta de acceso de **Dockerfile** (archivo de Docker). Puede mantener el valor predeterminado "/Dockerfile" En **Docker registry URL** (Dirección URL del registro de Docker), utilice el formato https://&lt;myRegistry>.azurecr.io si usa Azure Container Registry. Déjelo en blanco si utiliza DockerHub.
8. En **Registry credentials** (credenciales del registro), agregue las credenciales de Azure Container Registry. Puede obtener el identificador de usuario y la contraseña mediante la ejecución de los siguientes comandos de la CLI de Azure. El primer comando habilita la cuenta de administrador.    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. El nombre de la imagen de Docker y la etiqueta en la pestaña **Advanced** (Avanzado) son opcionales. De forma predeterminada, el nombre de la imagen se obtiene del nombre de imagen que configuró en Azure Portal (en la configuración del contenedor de Docker.) La etiqueta se genera a partir de $BUILD_NUMBER. Asegúrese de especificar el nombre de la imagen en Azure Portal o bien proporcione un valor para **Docker Image** (imagen de Docker) en la pestaña **Advanced** (Avanzado). En este ejemplo, escriba "&lt;su_registro>.azurecr.io/calculator" para **Docker image** (imagen de Docker) y deje **Docker Image Tag** (etiqueta de imagen de Docker) en blanco.
10. Se produce un error en la implementación si utiliza la configuración de imagen de Docker integrada. Asegúrese de que cambia la configuración de Docker para usar la imagen personalizada en la Configuración del contenedor de Docker en Azure Portal. Para la imagen integrada, utilice el enfoque de carga de archivos para la implementación.
11. De forma similar al enfoque de carga de archivos, puede elegir una ranura diferente que no sea de producción.
12. Guarde y compile el proyecto. Verá que la imagen del contenedor se inserta en el registro y se implementa la aplicación web.

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Implementación de la aplicación web en Linux a través de Docker mediante la canalización de Jenkins

1. En la interfaz de usuario web de GitHub, abra el archivo **Jenkinsfile_container_plugin**. Haga clic en el icono de lápiz para editar este archivo a fin de actualizar el grupo de recursos y el nombre de la aplicación web en las líneas 11 y 12, respectivamente.    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. Cambie en la línea 13 su servidor de registro de contenedor    
```java
def registryServer = '<registryURL>'
```    

3. Cambie la línea 16 para actualizar el identificador de la credencial en la instancia de Jenkins    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Creación de una canalización de Jenkins    

1. Abra Jenkins en un explorador web, haga clic en **New Item** (Nuevo elemento).
2. Proporcione un nombre para el trabajo y seleccione **Pipeline** (Canalización). Haga clic en **Aceptar**.
3. Haga clic en la pestaña **Pipeline** (Canalización) a continuación.
4. En **Definition** (Definición), seleccione **Pipeline script from SCM** (Script de canalización del SCM).
5. En **SCM**, seleccione **Git**.
6. Escriba la dirección URL de GitHub para el repositorio bifurcado: https:&lt;su repositorio bifurcado>.git</li>
7, Actualice **Script Path** (ruta de acceso del script) con el valor "Jenkinsfile_container_plugin"
8. Haga clic en **Save** (guardar) y ejecute el trabajo.

## <a name="verify-your-web-app"></a>Comprobación de la aplicación web

1. Para comprobar que el archivo WAR se ha implementado correctamente en la aplicación web: Abra un explorador web.
2. Cuando vaya a la dirección http://&lt;nombre_aplicación>.azurewebsites.net/api/calculator/ping podrá ver:    
     Welcome to Java Web App!!! (Bienvenido a la aplicación web de Java) Se ha actualizado.
   Sun Jun 17 16:39:10 UTC 2017
3. Vaya a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sustituya &lt;x> e &lt;y> con cualquier número) para obtener la suma de x e y        
    ![Calculadora: suma](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>Para App Service en Linux

* Para comprobarlo, en la CLI de Azure, ejecute:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Obtiene el siguiente resultado:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Vaya a http://&lt;nombre_aplicación>.azurewebsites.net/api/calculator/ping. Verá el mensaje: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Vaya a http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (sustituya &lt;x> e &lt;y> con cualquier número) para obtener la suma de x e y
    
## <a name="next-steps"></a>Pasos siguientes

En este tutorial se utiliza el complemento de Azure App Service para implementar en Azure.

Ha aprendido a:

> [!div class="checklist"]
> * Configurar Jenkins para implementar en Azure App Service a través de FTP 
> * Configurar Jenkins para implementar en Azure App Service en Linux con Docker 
