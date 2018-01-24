---
title: "Compilación e integración continuas de las aplicaciones para Linux de Azure Service Fabric mediante Jenkins | Microsoft Docs"
description: "Compilación e integración continuas de las aplicaciones para Linux de Service Fabric mediante Jenkins"
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
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: 80c52cfeab007030203b6af4bb220f1a847e9426
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Uso de Jenkins para compilar e implementar las aplicación para Linux
Jenkins es una herramienta popular para la integración e implementación continuas de aplicaciones. Así es como se compila e implementa una aplicación de Azure Service Fabric mediante Jenkins.

## <a name="general-prerequisites"></a>Requisitos previos generales
- Git debe estar instalado localmente. La versión adecuada de Git se puede instalar desde [la página de descargas de la Git](https://git-scm.com/downloads), en función de su sistema operativo. Si no conoce Git, encontrará más información al respecto en la [documentación de Git](https://git-scm.com/docs).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configuración de Jenkins en un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo configurarlo dentro de un clúster mientras se usa una cuenta de Azure Storage para guardar el estado de la instancia de contenedor.

### <a name="prerequisites"></a>requisitos previos
1. Tenga preparado un clúster Linux de Service Fabric. En los clústeres de Service Fabric creados desde Azure Portal ya está Docker instalado. Si el clúster se ejecuta localmente, para comprobar si Docker está instalado, use el comando ``docker info``. Si no está instalado, instálelo con los siguientes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Asegúrese de que el puerto 8081 se especifica como punto de conexión personalizado del clúster.
   >

2. Clone la aplicación mediante los pasos siguientes:
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. Guarde el estado del contenedor Jenkins en un recurso compartido de archivos:
  * Cree una cuenta de almacenamiento de Azure en la **misma región** que el clúster con un nombre como ``sfjenkinsstorage1``.
  * Cree un **recurso compartido de archivos** en esa cuenta de almacenamiento con un nombre como ``sfjenkins``.
  * Haga clic en **Conectar** para el recurso compartido de archivos y anote los valores que se muestran en **Conectando desde Linux**, valores que deberían ser parecidos a estos:
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Para montar recursos compartidos cifs, debe tener instalado el paquete cifs-utils en los nodos del clúster.       
  >

4. Actualice los valores de marcador de posición en el script ```setupentrypoint.sh``` con los detalles de almacenamiento de Azure del paso 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Reemplace ``[REMOTE_FILE_SHARE_LOCATION]`` con el valor ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` de la salida de la conexión en el paso 3 anterior.
  * Reemplace ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` con el valor ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` del paso 3 anterior.

5. **Solo clústeres seguros:** para configurar la implementación de aplicaciones en un clúster seguro desde Jenkins, es necesario poder acceder al certificado en el contenedor de Jenkins. En clústeres Linux, los certificados (PEM), simplemente, se copian del almacén que especifica X509StoreName al contenedor. En ApplicationManifest de ContainerHostPolicies, agregue esta referencia de certificado y actualice el valor de la huella digital. El valor de la huella digital debe ser el de un certificado que se encuentra en el nodo.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > El valor de la huella digital debe ser el mismo que el certificado usado para conectarse al clúster seguro. 
  >

6. Conéctese al clúster e instale la aplicación contenedora.

  **Clúster seguro**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **Clúster no seguro**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  Así se instala un contenedor de Jenkins en el clúster y se puede supervisar mediante Service Fabric Explorer.

    > [!NOTE]
    > La imagen Jenkins puede tardar un par de minutos en descargarse en el clúster.
    >

### <a name="steps"></a>Pasos
1. En el explorador, vaya a ``http://PublicIPorFQDN:8081``. Se proporciona la ruta de acceso de la contraseña de administrador inicial necesaria para iniciar sesión. 
2. Observe Service Fabric Explorer para determinar en qué nodo se está ejecutando el contenedor Jenkins. Inicie una sesión de Secure Shell (SSH) en este nodo.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Obtenga el identificador de la instancia del contenedor mediante ``docker ps -a``.
4. Inicie una sesión de Secure Shell (SSH) en el contenedor y pegue la ruta de acceso que se mostró en el portal de Jenkins. Por ejemplo, si en el portal se muestra la ruta `PATH_TO_INITIAL_ADMIN_PASSWORD`, ejecute la siguiente línea:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. En la página de introducción de Jenkins, elija la opción Select plugins to install (Seleccionar complementos a instalar), active la casilla **Ninguno** y haga clic en Instalar.
6. Cree un usuario o seleccione una opción para continuar como administrador.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configuración de Jenkins fuera de un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo se configura fuera de un clúster.

### <a name="prerequisites"></a>requisitos previos
Debe tener instalado Docker. Los siguientes comandos se pueden usar para instalar Docker desde el terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Ahora, cuando ejecute ``docker info`` en el terminal, verá en la salida que el servicio Docker se está ejecutando.

### <a name="steps"></a>Pasos
  1. Extraiga la imagen del contenedor de Jenkins de Service Fabric: ``docker pull rapatchi/jenkins:v10`` Esta imagen lleva preinstalado el complemento Jenkins de Servic Fabric.
  2. Ejecute la imagen del contenedor: ``docker run -itd -p 8080:8080 rapatchi/jenkins:v10``
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


## <a name="create-and-configure-a-jenkins-job"></a>Creación y configuración de trabajos de Jenkins

1. Cree un **nuevo elemento** desde el panel.
2. Escriba un nombre para dicho elemento (por ejemplo, **MyJob**). Seleccione **free-style project** (proyecto de estilo libre) y haga clic en **OK** (Aceptar).
3. Vaya a la página del trabajo y haga clic en **Configure** (Configurar).

   a. En la sección general, seleccione la casilla **GitHub project** (Proyecto de GitHub) y especifique la dirección URL del proyecto de GitHub. Dicha URL hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de integración continua e implementación continua (CI/CD) de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins``).

   b. En la sección **Source Code Management** (Administración del código fuente), seleccione **Git**. Especifique la dirección URL del repositorio que hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de CI/CD de Jenkins (por ejemplo, ``https://github.com/sayantancs/SFJenkins.git``). También puede especificar la rama que se va a compilar (por ejemplo, **/master**).
4. Configure su instancia de *GitHub* (la que hospeda el repositorio) para que pueda comunicarse con Jenkins. Para ello, siga los pasos que se describen a continuación:

   a. Vaya a la página del repositorio de GitHub. Vaya a **Settings** >  (Configuración) **Integrations and Services** (Integraciones y servicios).

   b. Seleccione **Add Service** (Agregar servicio), escriba **Jenkins** y seleccione el **complemento Jenkins-GitHub**.

   c. Escriba la dirección URL del webhook de Jenkins (de forma predeterminada, será ``http://<PublicIPorFQDN>:8081/github-webhook/``). Haga clic en **Add/Update Service** (Agregar o actualizar servicio).

   d. Se enviará un evento de prueba a la instancia de Jenkins. Debería ver una marca de verificación verde junto al webhook en GitHub y el proyecto se compilará.

   e. En la sección **Build Triggers** (Compilar desencadenadores), seleccione la opción de compilación que desea. Para este ejemplo, desea desencadenar una compilación cada vez que se produzca alguna inserción en el repositorio. Por tanto, selecciona **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para el sondeo de GITScm). [antes, esta opción se denominaba **Build when a change is pushed to GitHub** (Compilar cuando se inserta un cambio en GitHub)].

   f. **Sección de compilación de aplicaciones Java:** en la **sección Compilación**, en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione la opción **Invoke Gradle Script** (Invocar script de Gradle). En el widget que aparece, abra el menú de opciones avanzadas y especifique la ruta de acceso a **Root build script** (Script de compilación raíz) para la aplicación. Este toma el archivo build.gradle de la ruta de acceso especificada y funciona en consecuencia. Si crea un proyecto llamado ``MyActor`` (mediante el complemento de Eclipse o el generador de Yeoman), el script de compilación raíz debe contener ``${WORKSPACE}/MyActor``. La siguiente captura de pantalla es un ejemplo de su aspecto:

    ![Acción de compilación de Jenkins en Service Fabric][build-step]

   g. **Sección de compilación de aplicaciones .Net Core:** en la **sección Compilación**, en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione la opción **Execute Shell** (Ejecutar Shell). En el cuadro de comando que aparece, primero debe cambiarse el directorio a la ruta de acceso donde se encuentra el archivo build.sh. Una vez cambiado, el script build.sh se puede ejecutar y compilará la aplicación.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    A continuación, se muestra un ejemplo de los comandos que se utilizan para compilar el ejemplo [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) con un nombre de trabajo de Jenkins de CounterServiceApplication.

    ![Acción de compilación de Jenkins en Service Fabric][build-step-dotnet]
  
   h. En la lista desplegable **Post-Build Actions** (Acciones posteriores a la compilación), seleccione **Deploy Service Fabric Project** (Implementar proyecto de Service Fabric). Aquí es preciso proporcionar los detalles del clúster donde se implementará la aplicación de Service Fabric compilada por Jenkins. La ruta de acceso al certificado puede encontrarse haciendo eco del valor de la variable de entorno de eco Certificates_JenkinsOnSF_Code_MyCert_PEM desde dentro del contenedor. Esta ruta de acceso puede usarse para los campos de clave de cliente y certificado de cliente.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    También se puede especificar más detalles de la aplicación usados para implementar la aplicación. La siguiente captura de pantalla es un ejemplo de su aspecto:

    ![Acción de compilación de Jenkins en Service Fabric][post-build-step]

      > [!NOTE]
      > Este clúster puede ser el mismo que el que hospeda la aplicación del contenedor de Jenkins, en caso de que se vaya a usar Service Fabric para implementar la imagen del contenedor de Jenkins.
      >

## <a name="next-steps"></a>pasos siguientes
GitHub y Jenkins ya están configurados. Considere la posibilidad de realizar algún cambio ejemplo de cambio en el proyecto ``MyActor`` del ejemplo de repositorio, https://github.com/sayantancs/SFJenkins. Inserte los cambios en una rama ``master`` remota (o en cualquier rama que ha configurado para trabajar con ella). Esto desencadena el trabajo de Jenkins, ``MyJob``, que ha configurado. Captura los cambios de GitHub, los compila e implementa la aplicación en el punto de conexión del clúster que especificó en las acciones posteriores a la compilación.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

