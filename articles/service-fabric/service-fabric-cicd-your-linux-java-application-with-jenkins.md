---
title: "Compilación e implementación continua de una aplicación de Java para Linux con Jenkins | Microsoft Docs"
description: "Compilación e implementación continua de una aplicación de Java para Linux con Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: raunakp
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: sayantancs
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d3c64e3ed78a5379592b5e4276c83d26f04d2e1c
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Compilación e implementación de una aplicación de Java para Linux con Jenkins
Jenkins es una herramienta popular para integraciones e implementaciones continuas. Este documento nos guiará por la creación y la implementación de la aplicación de Service Fabric con Jenkins.

## <a name="general-prerequisites"></a>Requisitos previos generales
1. Debe tener git instalado localmente. Puede instalar la versión de git apropiada desde [aquí](https://git-scm.com/downloads) según su sistema operativo. Si aún no conoce git, puede seguir los pasos indicados en esta [documentación](https://git-scm.com/docs) para familiarizarse con git.
2. Debe tener a mano el complemento de Jenkins para Service Fabric. Descargue el complemento de Jenkins para Service Fabric desde [aquí](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configuración de Jenkins dentro de un clúster de Service Fabric

### <a name="prerequisites"></a>Requisitos previos
1. Tenga preparado un clúster Linux de Service Fabric. Los clústeres de Service Fabric creados desde Azure Portal ya tienen Docker instalado. Si se ejecuta el clúster local, compruebe si Docker está instalado o no con el comando ``docker info``; si no está instalado, instálelo usando:
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
2. Implemente la aplicación de contenedor de Service Fabric en el clúster ``http://PublicIPorFQDN:19080``. Siga estos pasos:
```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker && cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080 # Azure CLI cluster connect command
bash Scripts/install.sh
```
  El contenedor de Jenkins se instalará en el clúster al que se conectó. Puede supervisar la aplicación de contenedor con Service Fabric Explorer.

### <a name="steps"></a>Pasos
1. Vaya a la dirección URL ``http://PublicIPorFQDN:8081`` desde el explorador. Le dará la ruta de acceso a la contraseña de administrador inicial necesaria para iniciar sesión. Puede seguir usando Jenkins como administrador o puede crear y cambiar el usuario, después de iniciar sesión con la cuenta de administrador inicial.
> [!NOTE]
> Debe asegurarse de que el puerto 8081 esté especificado como puerto de punto de conexión de la aplicación al crear el clúster.
>

2. Obtenga el identificador de instancia de contenedor mediante ``docker ps -a``.
3. Inicie sesión SSH en el contenedor y pegue la ruta de acceso que se mostró en el portal de Jenkins. Por ejemplo, si el portal muestra la ruta de acceso `PATH_TO_INITIAL_ADMIN_PASSWORD`, puede hacer lo siguiente:  
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash # This takes you inside docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configure GitHub para trabajar con Jenkins usando [esto](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Use las instrucciones proporcionadas en GitHub para generar la clave SSH y agregue la clave ssh a la cuenta de GitHub que aloja (o alojará) el repositorio.
    * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Jenkins-Docker (y no en el host).
    * Para iniciar sesión en el shell de Jenkins desde el host, debe hacer lo siguiente:
  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configuración de Jenkins fuera de un clúster de Service Fabric

### <a name="prerequisites"></a>Requisitos previos
1. Debe tener Docker instalado. Si no lo tiene, puede escribir los siguientes comandos en el terminal para instalar Docker.
```sh
sudo apt-get install wget
wget -qO- https://get.docker.io/ | sh
```
Ahora, cuando ejecute ``docker info`` en el terminal, verá en la salida que el servicio Docker se está ejecutando.

### <a name="steps"></a>Pasos
  1. Extraiga el contenedor de Jenkins de Service Fabric: ``docker pull IMAGE_NAME ``
  2. Ejecute la imagen del contenedor: ``docker run -itd -p 8080:8080 IMAGE_NAME``
  3. Obtenga el identificador del contenedor de Docker que tiene la imagen de Jenkins (que acaba de instalar). Puede ver la lista de todos los contenedores de Docker con el comando ``docker ps –a``
  4. Obtenga la contraseña de administrador para la cuenta de Jenkins. Para ello, haga lo siguiente:
    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Si el identificador del contenedor es 2d24a73b5964, solo necesita insertar 2d24.
    * Esta contraseña será necesaria para iniciar sesión en el panel de Jenkins desde portal, que es ``http://<HOST-IP>:8080``
    * Cuando inicie sesión por primera vez, puede crear su propia cuenta de usuario y utilizarla en el futuro, o bien puede seguir usando la cuenta de administrador. Una vez creado el nuevo usuario, debe continuar con él.
  5. Configure GitHub para trabajar con Jenkins usando [esto](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Use las instrucciones proporcionadas en GitHub para generar la clave SSH y agregue la clave ssh a la cuenta de GitHub que aloja (o alojará) el repositorio.
      * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Jenkins-Docker (y no en el host).
      * Para iniciar sesión en el shell de Jenkins desde el host, debe hacer lo siguiente:
    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

> [!NOTE]
> Asegúrese de que el clúster o la máquina donde la imagen de contenedor de Jenkins está hospedada tengan una IP pública, de manera que la instancia de Jenkins reciba las notificaciones de GitHub.
>

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Instalación del complemento de Jenkins para Service Fabric desde el portal

1. Vaya a ``http://PublicIPorFQDN:8081``.
2. En el panel de Jenkins, haga clic en ``Manage Jenkins`` -> ``Manage Plugins`` -> ``Advanced``.
Aquí puede cargar un complemento. Seleccione la opción ``Choose file`` y, después, seleccione el archivo serviceFabric.hpi que descargó en los requisitos previos. Después de seleccionar la carga, Jenkins instalará automáticamente el complemento. Si solicita un reinicio, permítalo.

## <a name="creating-and-configuring-a-jenkins-job"></a>Creación y configuración de un trabajo de Jenkins

1. Crear ``new item`` desde el panel.
2. Escriba un nuevo nombre de elemento, por ejemplo, ``MyJob``, seleccione el proyecto de estilo libre y haga clic en Aceptar.
3. Después, vaya a la página del trabajo y haga clic en ``Configure`` -
  * En la sección general, en ``Github project``, especifique la dirección URL del proyecto de GitHub que hospeda la aplicación de Java para Service Fabric que desea integrar en el flujo CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins``).
  * En la sección ``Source Code Management``, seleccione ``Git``. Especifique la dirección URL del repositorio que hospeda la aplicación de Java para Service Fabric que desea integrar en el flujo CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins.git``). También puede especificar aquí la rama que debe crear, por ejemplo, ``*/master``.
4. Siga estos pasos para configurar su instancia de *Github* (es decir, la que hospeda el repositorio) para que sea capaz de comunicarse con Jenkins:
  - Vaya a la página del repositorio de GitHub. Vaya a ``Settings`` -> ``Integrations and Services``.
  - Seleccione ``Add Service``, escriba Jenkins y seleccione ``Jenkins-Github plugin``.
  - Escriba la dirección URL del webhook de Jenkins (de forma predeterminada, será ``http://<PublicIPorFQDN>:8081/github-webhook/``). Haga clic en Agregar o actualizar el servicio.
  - Se enviará un evento de prueba a la instancia de Jenkins. Debería ver una marca de verificación verde junto al webhook en GitHub y el proyecto estará compilado.
  - En la sección ``Build Triggers``, seleccione qué opción de compilación desea. En este caso, tenemos previsto desencadenar una compilación cada vez que se produzca alguna inserción en el repositorio, por lo que la opción correspondiente sería ``GitHub hook trigger for GITScm polling`` (anteriormente era 'Build when a change is pushed to GitHub' [Compilar cuando se inserte un cambio en GitHub]).
  - En ``Build section``, en la lista desplegable ``Add build step``, seleccione la opción ``Invoke Gradle Script``. En el widget que aparece, especifique la ruta de acceso a ``Root build script`` para la aplicación. Toma el archivo build.gradle de la ruta de acceso especificada y trabaja en consecuencia. Tenga en cuenta que, si crea un proyecto llamado ``MyActor`` (mediante el complemento Eclipse o el generador Yeoman), el script de compilación raíz debe contener ``${WORKSPACE}/MyActor``. Por ejemplo, el aspecto de esta sección es básicamente este:

    ![Acción de compilación de Jenkins en Service Fabric][build-step]
  - En la lista desplegable ``Post-Build Actions``, seleccione ``Deploy Service Fabric Project``. Aquí deberá proporcionar los detalles del clúster donde se implementará la aplicación de Service Fabric compilada por Jenkins, así como otra información adicional de la aplicación utilizada para implementarla. La captura de pantalla siguiente puede utilizarse como referencia:

    ![Acción de compilación de Jenkins en Service Fabric][post-build-step]

 >[!Note]
 > Aquí el clúster puede ser el mismo que el que aloja la aplicación de contenedor de Jenkins en caso de que use Service Fabric para implementar la imagen del contenedor de Jenkins.
 >

### <a name="end-to-end-scenario"></a>Escenario de principio a fin
Ahora, sus instancias de GitHub y Jenkins están configuradas y puede realizar algunos cambios en el proyecto ``MyActor`` en el repositorio, por ejemplo, https://github.com/sayantancs/SFJenkins, e insertar los cambios en la rama remota de ``master`` (o alguna de las ramas que ha configurado). Esto desencadenará el trabajo de Jenkins ``MyJob`` que ha configurado. Lo que hace es, básicamente, capturar los cambios de GitHub, compilarlos e implementar la aplicación en el punto de conexión del clúster que especificó en las acciones posteriores a la compilación.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

