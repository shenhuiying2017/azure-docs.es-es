---
title: "Compilación e integración continua de la aplicación de Java para Linux de Azure Service Fabric mediante Jenkins | Microsoft Docs"
description: "Compilación e integración continua de la aplicación de Java para Linux de Azure Service Fabric mediante Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 32d39e2c19348bc4a1ba218cfc411a70f9f212e3
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Uso de Jenkins para compilar e implementar una aplicación de Java para Linux
Jenkins es una herramienta popular para la integración e implementación continuas de aplicaciones. Así es como se compila e implementa una aplicación de Azure Service Fabric mediante Jenkins.

## <a name="general-prerequisites"></a>Requisitos previos generales
- Git debe estar instalado localmente. La versión adecuada de Git se puede instalar desde [la página de descargas de la Git](https://git-scm.com/downloads), en función de su sistema operativo. Si no conoce Git, encontrará más información al respecto en la [documentación de Git](https://git-scm.com/docs).
- Tener a mano el complemento de Jenkins para Service Fabric. Puede descargarlo de las [descargas de Service Fabric](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configuración de Jenkins en un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo configurarlo dentro de un clúster mientras se usa una cuenta de Azure Storage para guardar el estado de la instancia de contenedor.

### <a name="prerequisites"></a>Requisitos previos
1. Tenga preparado un clúster Linux de Service Fabric. En los clústeres de Service Fabric creados desde Azure Portal ya está Docker instalado. Si el clúster se ejecuta localmente, para comprobar si Docker está instalado, use el comando ``docker info``. Si no está instalado, instálelo con los siguientes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Implemente la aplicación de contenedor de Service Fabric en el clúster, para lo que debe seguir estos pasos:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
cd service-fabric-java-getting-started/Services/JenkinsDocker/
```

3. Necesita los detalles de la opción de conexión del recurso compartido de archivos de Azure Storage donde quiere guardar el estado de la instancia del contenedor Jenkins. Si está usando Microsoft Azure Portal para lo mismo, siga los pasos de Crear una cuenta de almacenamiento de Azure, por ejemplo ``sfjenkinsstorage1``. Cree un **recurso compartido de archivos** en esa cuenta de almacenamiento, por ejemplo ``sfjenkins``. Haga clic en **Conectar** para el recurso compartido de archivos y anote los valores que se muestran bajo **Conectando desde Linux**, que por ejemplo tendría el siguiente aspecto:
```sh
sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
```

4. Actualice los valores de marcador de posición en el script ```setupentrypoint.sh``` con los detalles de almacenamiento de Azure correspondientes.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
Reemplace ``[REMOTE_FILE_SHARE_LOCATION]`` con el valor ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` de la salida de la conexión en el punto 3 anterior.
Reemplace ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` con el valor ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` del punto 3 anterior.

5. Conéctese al clúster e instale la aplicación contenedora.
```azurecli
sfctl cluster select --endpoint http://PublicIPorFQDN:19080   # cluster connect command
bash Scripts/install.sh
```
Así se instala un contenedor de Jenkins en el clúster y se puede supervisar mediante Service Fabric Explorer.

### <a name="steps"></a>Pasos
1. En el explorador, vaya a ``http://PublicIPorFQDN:8081``. Se proporciona la ruta de acceso de la contraseña de administrador inicial necesaria para iniciar sesión. Puede seguir usando Jenkins como usuario administrador. O bien puede crear y cambiar el usuario una vez que inicie sesión con la cuenta de administrador inicial.

   > [!NOTE]
   > Asegúrese de que el puerto 8081 se especifica como puerto de punto de conexión de la aplicación al crear el clúster.
   >

2. Obtenga el identificador de la instancia del contenedor mediante ``docker ps -a``.
3. Inicie una sesión de Secure Shell (SSH) en el contenedor y pegue la ruta de acceso que se mostró en el portal de Jenkins. Por ejemplo, si en el portal se muestra la ruta `PATH_TO_INITIAL_ADMIN_PASSWORD`, ejecute la siguiente línea:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configure GitHub para que funcione con Jenkins, para lo que debe seguir los pasos que se indican en [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generación de una clave SSH nueva y su adición al agente de SSH).
    * Use las instrucciones que proporciona GitHub para generar la clave SSH y agregar la clave SSH a la cuenta de GitHub que hospeda el repositorio.
    * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Docker para Jenkins (no en el host).
    * Para iniciar sesión en el shell de Jenkins desde un host, use el siguiente comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configuración de Jenkins fuera de un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo se configura fuera de un clúster.

### <a name="prerequisites"></a>Requisitos previos
Debe tener instalado Docker. Los siguientes comandos se pueden usar para instalar Docker desde el terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Ahora, cuando ejecute ``docker info`` en el terminal, verá en la salida que el servicio Docker se está ejecutando.

### <a name="steps"></a>Pasos
  1. Extraiga la imagen del contenedor de Jenkins de Service Fabric: ``docker pull raunakpandya/jenkins:v1``
  2. Ejecute la imagen del contenedor: ``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Obtenga el identificador de la instancia de la imagen de contenedor. Puede enumerar todos los contenedores de Docker con el comando ``docker ps –a``.
  4. Inicie sesión en el portal de Jenkins, para lo que debe seguir estos pasos:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Si el identificador del contenedor es 2d24a73b5964, use 2d24.
    * Esta contraseña es necesaria para iniciar sesión en el panel de Jenkins desde el portal, que es ``http://<HOST-IP>:8080``
    * La primera vez que inicie sesión puede crear su propia cuenta de usuario y usarla en el futuro, o bien puede seguir usando la cuenta de administrador. Después de que cree un usuario, debe continuar con él.
  5. Configure GitHub para que funcione con Jenkins, para lo que debe seguir los pasos que se indican en [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generación de una clave SSH nueva y su adición al agente de SSH).
        * Use las instrucciones que proporciona GitHub para generar la clave SSH y agregar la clave SSH a la cuenta de GitHub que hospeda el repositorio.
        * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Docker para Jenkins (no en el host).
      * Para iniciar sesión en el shell de Jenkins desde un host, use los siguientes comandos:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Asegúrese de que el clúster o el equipo donde se hospeda la imagen del contenedor de Jenkins o tienen una dirección IP pública. Esto permite a la instancia de Jenkins recibir notificaciones de GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Instalación del complemento de Jenkins para Service Fabric desde el portal

1. Vaya a ``http://PublicIPorFQDN:8081``.
2. En el panel de Jenkins, seleccione **Manage Jenkins** >  (Administrar Jenkins) **Manage Plugins** >  (Administrar complementos) **Advanced** (Configuración avanzada).
Aquí puede cargar un complemento. Seleccione **Choose file** (Elegir archivo) y luego seleccione el archivo **serviceFabric.hpi**, que descargó en los requisitos previos. Al seleccionar **Upload** (Cargar), Jenkins instala automáticamente el complemento. Permita un reinicio si se solicita.

## <a name="create-and-configure-a-jenkins-job"></a>Creación y configuración de trabajos de Jenkins

1. Cree un **nuevo elemento** desde el panel.
2. Escriba un nombre para dicho elemento (por ejemplo, **MyJob**). Seleccione **free-style project** (proyecto de estilo libre) y haga clic en **OK** (Aceptar).
3. Vaya a la página del trabajo y haga clic en **Configure** (Configurar).

   a. En la sección general, en **GitHub project** (proyecto de GitHub), especifique la dirección URL del proyecto de GitHub. Dicha URL hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de integración continua e implementación continua (CI/CD) de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins``).

   b. En la sección **Source Code Management** (Administración del código fuente), seleccione **Git**. Especifique la dirección URL del repositorio que hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins.git``). También puede especificar la rama que se va a compilar (por ejemplo, **/master**).
4. Configure su instancia de *GitHub* (la que hospeda el repositorio) para que pueda comunicarse con Jenkins. Para ello, siga los pasos que se describen a continuación:

   a. Vaya a la página del repositorio de GitHub. Vaya a **Settings** >  (Configuración) **Integrations and Services** (Integraciones y servicios).

   b. Seleccione **Add Service** (Agregar servicio), escriba **Jenkins** y seleccione el **complemento Jenkins-GitHub**.

   c. Escriba la dirección URL del webhook de Jenkins (de forma predeterminada, será ``http://<PublicIPorFQDN>:8081/github-webhook/``). Haga clic en **Add/Update Service** (Agregar o actualizar servicio).

   d. Se enviará un evento de prueba a la instancia de Jenkins. Debería ver una marca de verificación verde junto al webhook en GitHub y el proyecto se compilará.

   e. En la sección **Build Triggers** (Compilar desencadenadores), seleccione la opción de compilación que desea. Para este ejemplo, desea desencadenar una compilación cada vez que se produzca alguna inserción en el repositorio. Por tanto, selecciona **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para el sondeo de GITScm). [antes, esta opción se denominaba **Build when a change is pushed to GitHub** (Compilar cuando se inserta un cambio en GitHub)].

   f. En la **sección de compilación**, en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione la opción **Invoke Gradle Script** (Invocar script de Gradle). En el widget que aparece, especifique la ruta de acceso a **Root build script** (Script de compilación raíz) para la aplicación. Toma el archivo build.gradle de la ruta de acceso especificada y funciona en consecuencia. Si crea un proyecto llamado ``MyActor`` (mediante el complemento de Eclipse o el generador de Yeoman), el script de compilación raíz debe contener ``${WORKSPACE}/MyActor``. La siguiente captura de pantalla es un ejemplo de su aspecto:

    ![Acción de compilación de Jenkins en Service Fabric][build-step]

   g. En la lista desplegable **Post-Build Actions** (Acciones posteriores a la compilación), seleccione **Deploy Service Fabric Project** (Implementar proyecto de Service Fabric). Aquí es preciso proporcionar los detalles del clúster donde se implementará la aplicación de Service Fabric compilada por Jenkins. También se puede especificar más detalles de la aplicación usados para implementar la aplicación. La siguiente captura de pantalla es un ejemplo de su aspecto:

    ![Acción de compilación de Jenkins en Service Fabric][post-build-step]

   > [!NOTE]
   > Este clúster puede ser el mismo que el que hospeda la aplicación del contenedor de Jenkins, en caso de que se vaya a usar Service Fabric para implementar la imagen del contenedor de Jenkins.
   >

## <a name="next-steps"></a>Pasos siguientes
GitHub y Jenkins ya están configurados. Considere la posibilidad de realizar algún cambio ejemplo de cambio en el proyecto ``MyActor`` del ejemplo de repositorio, https://github.com/sayantancs/SFJenkins. Inserte los cambios en una rama ``master`` remota (o en cualquier rama que ha configurado para trabajar con ella). Esto desencadena el trabajo de Jenkins, ``MyJob``, que ha configurado. Captura los cambios de GitHub, los compila e implementa la aplicación en el punto de conexión del clúster que especificó en las acciones posteriores a la compilación.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

