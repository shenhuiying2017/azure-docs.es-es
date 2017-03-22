---
title: "Compilación e integración continua de la aplicación de Java para Linux de Azure Service Fabric mediante Jenkins | Microsoft Docs"
description: "Compilación e integración continua de la aplicación de Java para Linux con Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Compilación e implementación de una aplicación de Java para Linux con Jenkins
Jenkins es una herramienta popular para integraciones e implementaciones continuas. Este documento nos guiará por la creación y la implementación de la aplicación de Service Fabric con Jenkins.

## <a name="general-prerequisites"></a>Requisitos previos generales
1. Tener GIT instalado localmente. Puede instalar la versión de git apropiada desde [aquí](https://git-scm.com/downloads) según su sistema operativo. Si aún no conoce GIT, puede seguir los pasos indicados en la [documentación](https://git-scm.com/docs) para familiarizarse con él.
2. Tener a mano el complemento de Jenkins para Service Fabric. Descargue el complemento de Jenkins para Service Fabric desde [aquí](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Configuración de Jenkins dentro de un clúster de Service Fabric

### <a name="prerequisites"></a>Requisitos previos
1. Tenga preparado un clúster Linux de Service Fabric. El clúster de Service Fabric creado desde Azure Portal ya tiene instalado Docker. Si va a ejecutar el clúster localmente, compruebe si Docker está instalado con el comando ``docker info``; si no lo está, instálelo mediante los siguientes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Implemente la aplicación de contenedor de Service Fabric en el clúster siguiendo estos pasos:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Se instala el contenedor de Jenkins en el clúster y se puede supervisar mediante Service Fabric Explorer.

### <a name="steps"></a>Pasos
1. Vaya a la dirección URL ``http://PublicIPorFQDN:8081`` desde el explorador. Se proporciona la ruta de acceso de la contraseña de administrador inicial necesaria para iniciar sesión. Puede seguir usando Jenkins como usuario administrador o puede crear y cambiar el usuario una vez que inicia sesión con la cuenta de administrador inicial.

  > [!NOTE]
  > Debe asegurarse de que el puerto 8081 esté especificado como puerto de punto de conexión de la aplicación al crear el clúster.
  >

2. Obtenga el identificador de instancia de contenedor mediante ``docker ps -a``.
3. Inicie sesión SSH en el contenedor y pegue la ruta de acceso que se mostró en el portal de Jenkins. Por ejemplo, si el portal muestra la ruta de acceso `PATH_TO_INITIAL_ADMIN_PASSWORD`, puede hacer lo siguiente:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configure GitHub para que funcione con Jenkins mediante los pasos mencionados en el [vínculo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Use las instrucciones proporcionadas en GitHub para generar la clave SSH y agregar la clave SSH a la cuenta de GitHub que hospeda su repositorio.
    * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Docker para Jenkins (y no en el host).
    * Para iniciar sesión en el shell de Jenkins desde el host, use el siguiente comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Configuración de Jenkins fuera de un clúster de Service Fabric

### <a name="prerequisites"></a>Requisitos previos
Debe tener instalado Docker. Los siguientes comandos se pueden usar para instalar Docker desde el terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Ahora, cuando ejecute ``docker info`` en el terminal, verá en la salida que el servicio Docker se está ejecutando.

### <a name="steps"></a>Pasos
  1. Extraiga la imagen del contenedor de Jenkins de Service Fabric: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Ejecute la imagen del contenedor: ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Obtenga el id. de la instancia de la imagen de contenedor. Puede enumerar todos los contenedores de Docker con el comando ``docker ps –a``.
  4. Inicie sesión en el portal de Jenkins mediante estos pasos:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Si el identificador del contenedor es 2d24a73b5964, use 2d24.
    * Esta contraseña es necesaria para iniciar sesión en el panel de Jenkins desde el portal, que es ``http://<HOST-IP>:8080``
    * Cuando inicia sesión por primera vez, puede crear su propia cuenta de usuario para su uso futuro, o bien puede seguir usando la cuenta de administrador. Cuando cree un usuario, debe continuar con él.
  5. Configure GitHub para que funcione con Jenkins mediante los pasos mencionados en el [vínculo](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Use las instrucciones proporcionadas en GitHub para generar la clave SSH y agregue la clave ssh a la cuenta de GitHub que aloja (o alojará) el repositorio.
      * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Docker para Jenkins (y no en el host).
      * Para iniciar sesión en el shell de Jenkins desde el host, use los siguientes comandos:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Asegúrese de que el clúster o la máquina donde la imagen de contenedor de Jenkins está hospedada tengan una IP pública, de manera que la instancia de Jenkins reciba las notificaciones de GitHub.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Instalación del complemento de Jenkins para Service Fabric desde el portal

1. Vaya a ``http://PublicIPorFQDN:8081``.
2. En el panel de Jenkins, seleccione **Manage Jenkins** ->  (Administrar Jenkins) **Manage Plugins** ->  (Administrar complementos) **Advanced** (Configuración avanzada).
Aquí puede cargar un complemento. Seleccione la opción **Choose file** (Elegir archivo) y luego seleccione el archivo serviceFabric.hpi, que descargó en los requisitos previos. Cuando selecciona la acción de cargar, Jenkins instala automáticamente el complemento. Permita un reinicio si se solicita.

## <a name="creating-and-configuring-a-jenkins-job"></a>Creación y configuración de un trabajo de Jenkins

1. Cree un **nuevo elemento** desde el panel
2. Escriba un nombre de elemento, por ejemplo, **MyJob**, seleccione el proyecto de estilo libre y haga clic en OK (Aceptar).
3. A continuación, vaya a la página del trabajo y haga clic en **Configure** - (Configurar).
  * En la sección general, en **Github project** (Proyecto de GitHub), especifique la dirección URL del proyecto de GitHub, que hospeda la aplicación de Java para Service Fabric que quiere integrar con el flujo CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins``).
  * En la sección **Source Code Management** (Administración del código fuente), seleccione **Git**. Especifique la dirección URL del repositorio que hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins.git``). También puede especificar aquí la rama para compilar, por ejemplo ***/master**.
4. Siga estos pasos para configurar su instancia de *Github* (es decir, la que hospeda el repositorio) para que sea capaz de comunicarse con Jenkins:
  1. Vaya a la página del repositorio de GitHub. Vaya a **Settings** ->  (Configuración) **Integrations and Services** (Integraciones y servicios).
  2. Seleccione **Add Service** (Agregar servicio), escriba Jenkins y seleccione el **complemento Jenkins-Github**.
  3. Escriba la dirección URL del webhook de Jenkins (de forma predeterminada, será ``http://<PublicIPorFQDN>:8081/github-webhook/``). Haga clic en Add/Update Service (Agregar o actualizar servicio).
  4. Se enviará un evento de prueba a la instancia de Jenkins. Debería ver una marca de verificación verde junto al webhook en GitHub y el proyecto estará compilado.
  5. En la sección **Build Triggers** (Compilar desencadenadores), seleccione la opción de compilación que desea; en este caso, queremos desencadenar una compilación cada vez que se produzca alguna inserción en el repositorio, así que la opción correspondiente sería **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para sondeo de GITScm) (anteriormente era "Build when a change is pushed to GitHub" [Compilar cuando se inserte un cambio en GitHub]).
  6. En la **sección de compilación**, en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione la opción **Invoke Gradle Script** (Invocar script de Gradle). En el widget que aparece, especifique la ruta de acceso a **Root build script** (Script de compilación raíz) para la aplicación. Toma el archivo build.gradle de la ruta de acceso especificada y trabaja en consecuencia. Si crea un proyecto llamado ``MyActor`` (mediante el complemento Eclipse o el generador Yeoman), el script de compilación raíz debe contener ``${WORKSPACE}/MyActor``. Por ejemplo, el aspecto de esta sección es básicamente este:

    ![Acción de compilación de Jenkins en Service Fabric][build-step]
  7. En la lista desplegable **Post-Build Actions** (Acciones posteriores a la compilación), seleccione **Deploy Service Fabric Project** (Implementar proyecto de Service Fabric). Aquí deberá proporcionar los detalles del clúster donde se implementará la aplicación de Service Fabric compilada por Jenkins, así como otra información adicional de la aplicación utilizada para implementarla. La captura de pantalla siguiente puede utilizarse como referencia:

    ![Acción de compilación de Jenkins en Service Fabric][post-build-step]

 > [!NOTE]
 > Aquí el clúster puede ser el mismo que el que aloja la aplicación de contenedor de Jenkins en caso de que use Service Fabric para implementar la imagen del contenedor de Jenkins.
 >

### <a name="end-to-end-scenario"></a>Escenario de principio a fin
Ahora, sus instancias de GitHub y Jenkins están configuradas y puede realizar algunos cambios en el proyecto ``MyActor`` en el repositorio, por ejemplo, https://github.com/sayantancs/SFJenkins, e insertar los cambios en la rama remota de ``master`` (o alguna de las ramas que haya configurado). Esto desencadenaría el trabajo de Jenkins ``MyJob`` que ha configurado. Lo que hace es, básicamente, capturar los cambios de GitHub, compilarlos e implementar la aplicación en el punto de conexión del clúster que especificó en las acciones posteriores a la compilación.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

