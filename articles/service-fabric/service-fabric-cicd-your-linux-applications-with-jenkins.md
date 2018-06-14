---
title: Compilación e integración continuas de las aplicaciones para Linux de Azure Service Fabric mediante Jenkins | Microsoft Docs
description: Compilación e integración continuas de las aplicaciones para Linux de Service Fabric mediante Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 047b3d00da4f192febeeab79c9c87b67a8a0489b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207968"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Uso de Jenkins para compilar e implementar las aplicación para Linux
Jenkins es una herramienta popular para la integración e implementación continuas de aplicaciones. Así es como se compila e implementa una aplicación de Azure Service Fabric mediante Jenkins.

## <a name="topic-overview"></a>Introducción al tema
En este artículo se explican varias formas posibles de configurar el entorno de Jenkins, así como diferentes maneras de implementar la aplicación en un clúster de Service Fabric después de que se ha creado. Siga estos pasos generales para configurar correctamente Jenkins, incorporar los cambios de GitHub, compilar la aplicación e implementarla en el clúster:

1. Asegúrese de instalar los [requisitos previos](#prerequisites).
2. A continuación, siga los pasos de una de estas secciones para configurar Jenkins:
   * [Configuración de Jenkins en un clúster de Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster) 
   * [Configuración de Jenkins fuera de un clúster de Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)
   * [Instalación del complemento de Service Fabric en un entorno existente de Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
3. Después de configurar Jenkins, siga los pasos de la sección [Creación y configuración de trabajos de Jenkins](#create-and-configure-a-jenkins-job) para configurar GitHub de forma que se desencadene Jenkins cuando se realicen cambios en la aplicación y para configurar la canalización de trabajos de Jenkins mediante el paso de compilación para incorporar los cambios desde GitHub y compilar la aplicación. 
4. Por último, configure el paso posterior a la compilación de trabajos de Jenkins para implementar la aplicación en el clúster de Service Fabric. Existen dos maneras de configurar Jenkins para implementar la aplicación en un clúster:    
   * En entornos de desarrollo y pruebas, use [Configuración de la implementación mediante el punto de conexión de administración de clúster](#configure-deployment-using-cluster-management-endpoint). Este es el método de implementación más sencillo de configurar.
   * En entornos de producción, use [Configuración de la implementación mediante las credenciales de Azure](#configure-deployment-using-azure-credentials). Microsoft recomienda este método para los entornos de producción porque con las credenciales de Azure puede limitar el acceso que un trabajo de Jenkins tiene a los recursos de Azure. 

## <a name="prerequisites"></a>requisitos previos

- Asegúrese de que Git esté instalado localmente. La versión adecuada de Git se puede instalar desde [la página de descargas de la Git](https://git-scm.com/downloads), en función de su sistema operativo. Si no conoce Git, encontrará más información al respecto en la [documentación de Git](https://git-scm.com/docs).
- En este artículo se usa el *ejemplo de introducción a Service Fabric* de GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) para compilar e implementar la aplicación. Puede bifurcar este repositorio para seguir el tutorial, o bien, con algunas modificaciones en las instrucciones, usar su propio proyecto de GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalación del complemento de Service Fabric en un entorno existente de Jenkins
Si va a agregar el complemento de Service Fabric a un entorno existente de Jenkins, necesita lo siguiente:

- La [CLI de Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Asegúrese de instalar la CLI en el nivel de sistema en lugar de en el nivel de usuario, de modo que Jenkins pueda ejecutar los comandos de la CLI. 
   >

- Para implementar aplicaciones de Java, instale [Gradle y Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Para implementar aplicaciones .NET Core 2.0, instale el [SDK de .NET Core 2.0](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Después de instalar los requisitos previos necesarios para su entorno, puede buscar el complemento de Azure Service Fabric en el Marketplace de Jenkins e instalarlo.

Una vez instalado el complemento, vaya directamente a [Creación y configuración de trabajos de Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configuración de Jenkins en un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo configurarlo dentro de un clúster mientras se usa una cuenta de Azure Storage para guardar el estado de la instancia de contenedor.

### <a name="prerequisites"></a>requisitos previos
- Tiene un clúster de Linux para Service Fabric con Docker instalado. Los clústeres de Service Fabric que se ejecutan en Azure ya tienen instalado Docker. Si va a ejecutar el clúster localmente (entorno de desarrollo OneBox), compruebe si Docker está instalado en su máquina con el comando `docker info`. Si no está instalado, instálelo con los siguientes comandos:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Asegúrese de que el puerto 8081 se especifica como punto de conexión personalizado en el clúster. Si usa un clúster local, asegúrese de que el puerto 8081 esté abierto en la máquina host y que tenga una dirección IP pública.
   >

### <a name="steps"></a>Pasos
1. Clone la aplicación mediante los siguientes comandos:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. Guarde el estado del contenedor Jenkins en un recurso compartido de archivos:
   1. Cree una cuenta de almacenamiento de Azure en la **misma región** que el clúster con un nombre como `sfjenkinsstorage1`.
   2. Cree un **recurso compartido de archivos** en esa cuenta de almacenamiento con un nombre como `sfjenkins`.
   3. Haga clic en **Conectar** para el recurso compartido de archivos y anote los valores que se muestran en **Conectando desde Linux**, valores que deberían ser parecidos a estos:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Para montar recursos compartidos cifs, debe tener instalado el paquete cifs-utils en los nodos del clúster.      
   >

4. Actualice los valores de marcador de posición en el script `setupentrypoint.sh` con los detalles de almacenamiento de Azure del paso 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Reemplace `[REMOTE_FILE_SHARE_LOCATION]` por el valor `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` de la salida de la conexión en el paso 2 anterior.
   * Reemplace `[FILE_SHARE_CONNECT_OPTIONS_STRING]` por el valor `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` del paso 2 anterior.

5. **Solo clúster seguro:** 
   
   Para configurar la implementación de aplicaciones en un clúster seguro desde Jenkins, es necesario poder acceder al certificado del clúster en el contenedor de Jenkins. En el archivo *ApplicationManifest.xml*, en la etiqueta **ContainerHostPolicies**, agregue esta referencia de certificado y actualice el valor de huella digital con el del certificado del clúster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Además, agregue las líneas siguientes en la etiqueta **ApplicationManifest** (raíz) en el archivo *ApplicationManifest.xml* y actualice el valor de huella digital con el del certificado del clúster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. Conéctese al clúster e instale la aplicación contenedora.

   **Clúster seguro**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   El comando anterior toma el certificado en formato PEM. Si el certificado está en formato PFX, puede usar el siguiente comando para convertirlo. Si su archivo PFX no está protegido con contraseña, especifique el parámetro **passin** como `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
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

7. En el explorador, vaya a `http://PublicIPorFQDN:8081`. Se proporciona la ruta de acceso de la contraseña de administrador inicial necesaria para iniciar sesión. 
2. Observe Service Fabric Explorer para determinar en qué nodo se está ejecutando el contenedor Jenkins. Inicie una sesión de Secure Shell (SSH) en este nodo.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. Obtenga el identificador de la instancia del contenedor mediante `docker ps -a`.
4. Inicie una sesión de Secure Shell (SSH) en el contenedor y pegue la ruta de acceso que se mostró en el portal de Jenkins. Por ejemplo, si en el portal se muestra la ruta `PATH_TO_INITIAL_ADMIN_PASSWORD`, ejecute los siguientes comandos:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. En la página de introducción de Jenkins, elija la opción Select plugins to install (Seleccionar complementos para instalar), active la casilla **None** (Ninguno) y haga clic en Install (Instalar).
6. Cree un usuario o seleccione una opción para continuar como administrador.

Después de configurar Jenkins, vaya directamente a [Creación y configuración de trabajos de Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configuración de Jenkins fuera de un clúster de Service Fabric

Jenkins se puede configurar dentro o fuera de un clúster de Service Fabric. En las secciones siguientes se muestra cómo se configura fuera de un clúster.

### <a name="prerequisites"></a>requisitos previos
- Asegúrese de que Docker está instalado en su máquina. Los siguientes comandos se pueden usar para instalar Docker desde el terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Cuando ejecute `docker info` en el terminal, verá en la salida que el servicio Docker se está ejecutando.

### <a name="steps"></a>Pasos
1. Extraiga la imagen del contenedor de Jenkins de Service Fabric: `docker pull rapatchi/jenkins:latest` Esta imagen lleva preinstalado el complemento Jenkins de Servic Fabric.
2. Ejecute la imagen del contenedor: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. Obtenga el identificador de la instancia de la imagen de contenedor. Puede enumerar todos los contenedores de Docker con el comando `docker ps –a`.
4. Inicie sesión en el portal de Jenkins con los siguientes pasos:

   1. Inicie sesión en un shell de Jenkins desde el host. Use los cuatro primeros dígitos del identificador de contenedor. Por ejemplo, si el identificador de contenedor es `2d24a73b5964`, use `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. Desde el shell de Jenkins, obtenga la contraseña de administrador para la instancia del contenedor:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. Para iniciar sesión en el panel de Jenkins, abra la siguiente dirección URL en un explorador web: `http://<HOST-IP>:8080`. Use la contraseña del paso anterior para desbloquear Jenkins.
   4. (Opcional). La primera vez que inicie sesión puede crear su propia cuenta de usuario y usarla en los siguientes pasos, o bien puede seguir usando la cuenta de administrador. Si crea un usuario, debe continuar con él.
5. Para configurar GitHub para que funcione con Jenkins, siga los pasos que se indican en [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generación de una clave SSH nueva y su adición al agente de SSH).
   * Use las instrucciones que proporciona GitHub para generar la clave SSH y agregar la clave SSH a la cuenta de GitHub que hospeda el repositorio.
   * Ejecute los comandos que se mencionan en el vínculo anterior en el shell de Docker para Jenkins (no en el host).
   * Para iniciar sesión en el shell de Jenkins desde un host, use el siguiente comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Asegúrese de que el clúster o la máquina donde se hospeda la imagen del contenedor de Jenkins tengan una dirección IP pública. Esto permite a la instancia de Jenkins recibir notificaciones de GitHub.

Después de configurar Jenkins, continúe con la siguiente sección, [Creación y configuración de trabajos de Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Creación y configuración de trabajos de Jenkins

Los pasos descritos en esta sección muestran cómo configurar un trabajo de Jenkins para responder a los cambios en un repositorio de GitHub, capturar esos cambios y compilarlos. Al final de esta sección, se le dirige a los últimos pasos para configurar el trabajo para implementar la aplicación según si va a usar un entorno de desarrollo y pruebas o un entorno de producción. 

1. En el panel de Jenkins, haga clic en **New Item**(Nuevo elemento).
2. Escriba un nombre para dicho elemento (por ejemplo, **MyJob**). Seleccione **free-style project** (proyecto de estilo libre) y haga clic en **OK** (Aceptar).
3. Se abre la página de configuración de trabajos. (Para obtener la configuración del panel de Jenkins, haga clic en el trabajo y, a continuación, haga clic en **Configure** [Configurar]).

4. En la pestaña **General**, marque la casilla **GitHub project** (Proyecto de GitHub) y especifique la dirección URL de su proyecto de GitHub. Dicha URL hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de integración continua e implementación continua (CI/CD) de Jenkins (por ejemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

5. En la pestaña **Source Code Management** (Administración del código fuente) seleccione **Git**. Especifique la dirección URL del repositorio que hospeda la aplicación de Java para Service Fabric que desea integrar con el flujo de CI/CD de Jenkins (por ejemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). También puede especificar la rama que debe compilar (por ejemplo, `/master`).
6. Configure el repositorio de *GitHub* para que se comunique con Jenkins:

   a. En la página del repositorio de GitHub, vaya a **Settings** >  (Configuración) **Integrations and Services** (Integraciones y servicios).

   b. Seleccione **Add Service** (Agregar servicio), escriba **Jenkins** y seleccione el **complemento Jenkins-GitHub**.

   c. Escriba la dirección URL del webhook de Jenkins (de forma predeterminada, será `http://<PublicIPorFQDN>:8081/github-webhook/`). Haga clic en **Add/Update Service** (Agregar o actualizar servicio).

   d. Se enviará un evento de prueba a la instancia de Jenkins. Debería ver una marca de verificación verde junto al webhook en GitHub y el proyecto se compilará.

7. En la pestaña **Build Triggers** (Compilar desencadenadores) de Jenkins, seleccione la opción de compilación que desea. En este ejemplo, va a desencadenar una compilación cada vez que se produce un incorporación de cambios en el repositorio, así que seleccione **GitHub hook trigger for GITScm polling** (Desencadenador de enlace de GitHub para el sondeo de GITScm). [antes, esta opción se denominaba **Build when a change is pushed to GitHub** (Compilar cuando se inserta un cambio en GitHub)].
8. En la pestaña **Build** (Compilar), realice una de las siguientes acciones según si va a compilar una aplicación Java o una .NET Core:

   * **Para aplicaciones Java:** en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione **Invoke Gradle Script** (Invocar script de Gradle). Haga clic en **Avanzadas**. En el menú avanzado, especifique la ruta de acceso a **Root build script** (Script de compilación raíz) para la aplicación. Este toma el archivo build.gradle de la ruta de acceso especificada y funciona en consecuencia. Para la [aplicación ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), esto es: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Acción de compilación de Jenkins en Service Fabric][build-step]

   * **Para las aplicaciones .NET Core:** en la lista desplegable **Add build step** (Agregar paso de compilación), seleccione **Execute Shell** (Ejecutar Shell). En el cuadro de comando que aparece, primero debe cambiarse el directorio a la ruta de acceso donde se encuentra el archivo build.sh. Una vez cambiado el directorio, el script build.sh se puede ejecutar y compilará la aplicación.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     En la siguiente captura de pantalla se muestra un ejemplo de los comandos que se usan para compilar el ejemplo [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) con un nombre de trabajo de Jenkins de CounterServiceApplication.

      ![Acción de compilación de Jenkins en Service Fabric][build-step-dotnet]

9. Para configurar Jenkins para implementar la aplicación en un clúster de Service Fabric en las acciones posteriores a la compilación, necesita la ubicación del certificado de ese clúster en el contenedor de Jenkins. Elija una de las siguientes, según si el contenedor de Jenkins se ejecuta dentro o fuera de su clúster, y tome nota de ella:

   * **Si Jenkins se ejecuta dentro del clúster:** para encontrar la ruta de acceso al certificado, refleje el valor de la variable de entorno *Certificates_JenkinsOnSF_Code_MyCert_PEM* dentro del contenedor.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Si Jenkins se ejecuta fuera de su clúster:** siga estos pasos para copiar el certificado del clúster en el contenedor:
      1. El certificado debe estar en formato PEM. Si no tiene un archivo PEM, puede crear uno a partir del archivo PFX de certificado. Si su archivo PFX no está protegido con contraseña, ejecute el siguiente comando desde el host:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Si el archivo PFX está protegido con contraseña, incluya la contraseña en el parámetro `-passin`. Por ejemplo: 

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. Para obtener el identificador de contenedor para el contenedor de Jenkins, ejecute `docker ps` desde el host.
      3. Copie el archivo PEM en el contenedor con el siguiente comando de Docker:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Ya casi hemos terminado. Mantenga abierto el trabajo de Jenkins. La última tarea consiste en configurar los pasos posteriores a la compilación para implementar la aplicación en el clúster de Service Fabric:

* Para realizar la implementación en un entorno de desarrollo o de prueba, siga los pasos de la sección [Configuración de la implementación mediante el punto de conexión de administración de clúster](#configure-deployment-using-cluster-management-endpoint).
* Para realizar la implementación en un entorno de producción, siga los pasos de la sección [Configuración de la implementación mediante las credenciales de Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configuración de la implementación mediante el punto de conexión de administración de clúster
En entornos de desarrollo y pruebas, puede usar el punto de conexión de administración de clúster para implementar la aplicación. Para configurar la acción posterior a la compilación con el punto de conexión de administración de clúster para implementar la aplicación, apenas es necesario hacer nada. Si va a realizar la implementación en un entorno de producción, paso directamente a la sección [Configuración de la implementación mediante las credenciales de Azure](#configure-deployment-using-azure-credentials) para configurar una entidad de servicio de Azure Active Directory para usar durante la implementación.    

1. En el trabajo de Jenkins, haga clic en la pestaña **Post-build Actions** (Acciones posteriores a la compilación). 
2. En la lista desplegable **Post-Build Actions** (Acciones posteriores a la compilación), seleccione **Deploy Service Fabric Project** (Implementar proyecto de Service Fabric). 
3. En **Service Fabric Cluster Configuration** (Configuración del clúster de Service Fabric), seleccione el botón de radio **Fill the Service Fabric Management Endpoint** (Rellenar el punto de conexión de administración de Service Fabric).
4. En **Management Host** (Host de administración), escriba el punto de conexión de su clúster, por ejemplo `{your-cluster}.eastus.cloudapp.azure.com`.
5. En **Client Key** (Clave de cliente) y **Client Cert** (Certificado de cliente), escriba la ubicación del archivo PEM en el contenedor de Jenkins, por ejemplo `/var/jenkins_home/clustercert.pem`. (La ubicación del certificado la copió en el último paso de [Creación y configuración de trabajos de Jenkins](#create-and-configure-a-jenkins-job)).
6. En **Application Configuration** (Configuración de la aplicación), configure los campos **Application Name** (Nombre de aplicación), **Application Type** (Tipo de aplicación) y **Path to Application Manifest** (Ruta de acceso al manifiesto de la aplicación) (relativa).

   ![Jenkins en Service Fabric: punto de conexión de administración de configuración de la acción posterior a la compilación](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. Haga clic en **Verify configuration**(Comprobar configuración). Tras la correcta comprobación, haga clic en **Save** (Guardar). La canalización del trabajo de Jenkins está ahora completamente configurada. Vaya directamente a [Siguientes pasos](#next-steps) para probar su implementación.

## <a name="configure-deployment-using-azure-credentials"></a>Configuración de la implementación mediante las credenciales de Azure
En entornos de producción, es muy recomendable configurar una credencial de Azure para implementar la aplicación. En esta sección se muestra cómo configurar una entidad de servicio de Azure Active Directory para usarla en la implementación de la aplicación en la acción posterior a la compilación. Puede asignar roles a entidades de servicio de su directorio para limitar los permisos del trabajo de Jenkins. 

En entornos de desarrollo y pruebas, puede configurar las credenciales de Azure o el punto de conexión de administración de clúster para implementar la aplicación. Para más información sobre cómo configurar un punto de conexión de administración de clúster, consulte [Configuración de la implementación mediante el punto de conexión de administración de clúster](#configure-deployment-using-cluster-management-endpoint).   

1. Para crear una entidad de servicio de Azure Active Directory y asignarle permisos en las suscripciones de Azure, siga los pasos que se describen en [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Preste atención a lo siguiente:

   * Mientras sigue los pasos descritos en el tema, asegúrese de copiar y guardar los valores siguientes: *Id. de aplicación*, *Clave de aplicación*, *Id. de directorio (id. de inquilino)* e *Id. de suscripción*. Los necesita para configurar las credenciales de Azure en Jenkins.
   * Si no tiene los [permisos necesarios](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) sobre el directorio, deberá pedir al administrador que se los conceda o que le cree la entidad de servicio; o deberá configurar el punto de conexión de administración de su clúster en **Post-Build Actions** (Acciones posteriores a la compilación) para su trabajo en Jenkins.
   * En la sección [Crear una aplicación de Azure Active Directory](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application), puede escribir cualquier dirección URL con formato correcto como **dirección URL de inicio de sesión**.
   * En la sección [Asignación de aplicación a un rol](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role), puede asignar a la aplicación el rol *Lector* en el grupo de recursos del clúster.

2. De nuevo en el trabajo de Jenkins, haga clic en la pestaña **Post-build Actions** (Acciones posteriores a la compilación).
3. En la lista desplegable **Post-Build Actions** (Acciones posteriores a la compilación), seleccione **Deploy Service Fabric Project** (Implementar proyecto de Service Fabric). 
4. En **Service Fabric Cluster Configuration** (Configuración del clúster de Service Fabric), seleccione el botón de radio **Select the Service Fabric Cluster** (Seleccionar el clúster de Service Fabric). Haga clic en **Add** (Agregar) junto a **Azure Credentials** (Credenciales de Azure). Haga clic en **Jenkins** para seleccionar el proveedor de credenciales de Jenkins.
5. En el proveedor de identidades de Jenkins, seleccione **Microsoft Azure Service Principal** (Entidad de servicio de Microsoft Azure) en la lista desplegable **Kind** (Variante).
6. Use los valores que guardó al configurar la entidad de servicio en el paso 1 para configurar los campos siguientes:

   * **Client ID** (Id. de cliente): *Id. de aplicación*
   * **Client Secret** (Secreto de cliente): *Clave de aplicación*
   * **Tenant ID** (Id. de inquilino): *Id. de directorio*
   * **Subscription ID** (Id. de suscripción): *Id. de suscripción*
6. Escriba un **identificador** descriptivo que use para seleccionar la credencial de Jenkins y una breve **descripción**. A continuación, haga clic en **Verify Service Principal** (Comprobar entidad de servicio). Si la comprobación es correcta, haga clic en **Add** (Agregar).

   ![Jenkins en Service Fabric: especificación de credenciales de Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. De nuevo en **Service Fabric Cluster Configuration** (Configuración del clúster de Service Fabric), asegúrese de que en **Azure Credentials** (Credenciales de Azure), se selecciona la nueva credencial. 
8. En la lista desplegable **Resource Group** (Grupo de recursos), seleccione el grupo de recursos del clúster en el que quiere implementar la aplicación.
9. En la lista desplegable **Service Fabric**, seleccione el clúster en el que quiere implementar la aplicación.
10. En **Client Key** (Clave de cliente) y **Client Cert** (Certificado de cliente), escriba la ubicación del archivo PEM en el contenedor de Jenkins. Por ejemplo: `/var/jenkins_home/clustercert.pem`. 
11. En **Application Configuration** (Configuración de la aplicación), configure los campos **Application Name** (Nombre de aplicación), **Application Type** (Tipo de aplicación) y **Path to Application Manifest** (Ruta de acceso al manifiesto de la aplicación) (relativa).
    ![Acción posterior a la compilación de Jenkins para Service Fabric: configuración de las credenciales de Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. Haga clic en **Verify configuration**(Comprobar configuración). Tras la correcta comprobación, haga clic en **Save** (Guardar). La canalización del trabajo de Jenkins está ahora completamente configurada. Continúe con los [Siguientes pasos](#next-steps) para probar la implementación.

## <a name="next-steps"></a>Pasos siguientes
GitHub y Jenkins ya están configurados. Considere la posibilidad de realizar algunos cambios de ejemplo en el proyecto `reliable-services-actor-sample/Actors/ActorCounter` en su bifurcación del repositorio, https://github.com/Azure-Samples/service-fabric-java-getting-started. Inserte los cambios en la rama `master` remota (o en cualquier rama que ha configurado para trabajar con ella). Esto desencadena el trabajo de Jenkins, `MyJob`, que ha configurado. Los cambios se capturan desde GitHub, se compilan y la aplicación se implementa en el clúster especificado en las acciones posteriores a la compilación.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

