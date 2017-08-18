---
title: "Implementación de una aplicación web Spring Boot en Linux en Azure Container Service | Microsoft Docs"
description: "Este tutorial le guiará por los pasos necesarios para implementar una aplicación de Spring Boot como una aplicación web de Linux en Microsoft Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Implementación de una aplicación de Spring Boot en Linux en Azure Container Service

[Spring Framework] es una solución de código abierto que ayuda a los desarrolladores de Java a crear aplicaciones de nivel empresarial. Uno de los proyectos más populares basados en Java es [Spring Boot], que proporciona un enfoque simplificado para crear aplicaciones de Java independientes.

[cliente de Docker] es una solución de código abierto que ayuda a los desarrolladores a automatizar la implementación, el escalado y la administración de aplicaciones que se ejecutan en contenedores.

En este tutorial se explica cómo usar Docker para desarrollar e implementar una aplicación de Spring Boot en un host de Linux en [Azure Container Service].

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN] o registrarse para obtener una [cuenta de Azure gratuita].
* La [Interfaz de la línea de comandos (CLI) de Azure].
* Un [kit para desarrolladores de Java (JDK)] actualizado.
* La herramienta de compilación [Maven] de Apache (versión 3).
* Un cliente [Git].
* Un [cliente de Docker].

> [!NOTE]
>
> Debido a los requisitos de virtualización de este tutorial, no puede seguir los pasos descritos en este artículo en una máquina virtual. En su lugar, debe usar un equipo físico con las características de virtualización habilitadas.
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Creación de Spring Boot en la aplicación web Docker Getting Started

Los pasos siguientes le ayudarán a crear una sencilla aplicación web de Spring Boot y probarla de forma local.

1. Abra el símbolo del sistema. A continuación, cree un directorio local para alojar la aplicación y cambie a dicho directorio, por ejemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- o --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. Clone el proyecto de ejemplo [inicial de Spring Boot en Docker] en el directorio que ha creado, por ejemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. Cambie de directorio al del proyecto finalizado, por ejemplo:
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **Paso opcional**: si quiere ejecutar el servidor de Tomcat integrado en el puerto 80 en lugar del puerto 8080 predeterminado (por ejemplo, si va a probar el proyecto de Spring Boot localmente), puede configurar el puerto con los pasos siguientes:

   a. Cambie el directorio al directorio de recursos, por ejemplo:
   ```
   cd src/main/resources
   ```

   b. Abra el archivo **application.yml** en un editor de texto.

   c. Modifique la configuración de **server:** (servidor) de modo que el servidor se ejecute en el puerto 80, por ejemplo:
   ```
   server:
      port: 80
   ```

   d. Guarde y cierre el archivo **application.yml**.

   e. Cambie el directorio de nuevo al directorio raíz del proyecto finalizado, por ejemplo:
   ```
   cd ../../..
   ```

5. Compile el archivo JAR con Maven, por ejemplo:
   ```
   mvn package
   ```

6. Una vez creada la aplicación web, cambie el directorio al directorio `target` donde se encuentra el archivo JAR e inicie la aplicación web, por ejemplo:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. Pruebe la aplicación web. Para ello, navegue a ella localmente mediante un explorador web. Por ejemplo, puede usar el comando siguiente si curl está disponible y ha configurado el servidor de Tomcat para ejecutarse en el puerto 80:
   ```
   curl http://localhost
   ```

8. Debería aparecer el siguiente mensaje: **Hello Docker World!**

   ![Exploración de la aplicación de ejemplo de modo local][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Creación de una instancia de Azure Container Registry para usarla como un Registro de Docker privado

Los siguientes pasos le muestran cómo usar Azure Portal para crear una instancia de Azure Container Registry.

> [!NOTE]
> Si quiere usar la CLI de Azure en lugar de Azure Portal, siga los pasos descritos en [Creación de un registro de contenedor privado de Docker con la CLI de Azure 2.0][1].

1. Inicie sesión en [Azure Portal].

    Después de iniciar sesión con su cuenta en Azure Portal, siga los pasos descritos en [Creación de un registro de contenedor privado de Docker con Azure Portal]. A continuación se resumen los pasos de este artículo:

2. Seleccione el icono de menú **+ Nuevo**.

3. Seleccione **Contenedores** y, a continuación, seleccione **Azure Container Registry**.

   ![Crear una nueva instancia de Azure Container Registry][AR01]

4. Cuando aparezca la página de información de la plantilla de Azure Container Registry, haga clic en **Crear**.

   ![Crear una nueva instancia de Azure Container Registry][AR02]

5. Cuando se muestre la hoja **Crear registro de contenedor**:

   a. Escriba el **Nombre del registro** y el **Grupo de recursos**.  
   
   b. Seleccione **Habilitar** para el **Usuario administrador**.
   
   c. Seleccione **Crear**.

   ![Configuración de Azure Container Registry][AR03]

6. Una vez creado el registro de contenedor, vaya a él en Azure Portal. A continuación, seleccione **Claves de acceso**. Tome nota del nombre de usuario y la contraseña para usarlos en los pasos siguientes.

   ![Claves de acceso de Azure Container Registry][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurar Maven para que use las claves de acceso de Azure Container Registry

1. Vaya al directorio de configuración para la instalación de Maven. A continuación, abra el archivo **settings.xml** con un editor de texto.

2. Agregue la configuración de acceso de Azure Container Registry de la sección anterior de este tutorial a la colección `<servers>` en el archivo **settings.xml**, por ejemplo:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Vaya al directorio del proyecto completo de la aplicación de Spring Boot (por ejemplo, **C:\SpringBoot\gs-spring-boot-docker\complete** o **/users/robert/SpringBoot/gs-spring-boot-docker/complete**). A continuación, abra el archivo **pom.xml** con un editor de texto.

1. Actualice la colección `<properties>` en el archivo **pom.xml**. Use el valor del servidor de inicio de sesión de Azure Container Registry que anotó en la sección anterior de este tutorial, por ejemplo:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Actualice la colección `<plugins>` en el archivo **pom.xml** para que `<plugin>` contenga la dirección del servidor de inicio de sesión y el nombre de registro de la instancia de Azure Container Registry de la sección anterior de este tutorial. Por ejemplo:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Vaya al directorio del proyecto completo de la aplicación de Spring Boot. A continuación, ejecute el siguiente comando para volver a generar la aplicación e insertar el contenedor en la instancia de Azure Container Registry:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Cuando inserte el contenedor de Docker en Azure, podría recibir un mensaje de error similar a uno de los siguientes, incluso si el contenedor de Docker se ha creado correctamente:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Si esto ocurre, es posible que deba iniciar sesión en Azure desde la línea de comandos de Docker, por ejemplo:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Después, puede insertar el contenedor desde la línea de comandos, por ejemplo:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>Creación de una aplicación web en Linux en Azure App Service mediante la imagen de contenedor

1. Inicie sesión en [Azure Portal].

1. Haga clic en el icono de menú **+ Nuevo** y, a continuación, haga clic en **Web y móvil**.

2.  Haga clic en **Aplicación web en Linux**.

   ![Crear una aplicación web en Azure Portal][LX01]

3. Cuando aparezca la hoja **Aplicación web en Linux**, siga los pasos siguientes:

   a. Escriba un nombre único para el **Nombre de la aplicación**, por ejemplo: *wingtiptoyslinux*.

   b. Seleccione su **Suscripción** en la lista desplegable.

   c. Seleccione un **Grupo de recursos** existente o especifique un nombre para crear uno nuevo.

   d. Haga clic en **Configurar contenedor** y especifique la información siguiente:

      * Seleccione **Registro privado**.

      * **Imagen y etiqueta opcional**: especifique el nombre del contenedor del ejemplo anterior, por ejemplo: *wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*.

      * **Dirección URL del servidor**: especifique la dirección URL del registro del ejemplo anterior, por ejemplo: *https://wingtiptoysregistry.azurecr.io*.

      * **Nombre de usuario de inicio de sesión** y **Contraseña**: especifique las credenciales de inicio de sesión de las **Claves de acceso** que ha usado en los pasos anteriores.

   e. Después de haber especificado toda la información anterior, seleccione **Aceptar**.

   ![Configurar aplicaciones web][LX02]

1. Haga clic en **Crear**.

> [!NOTE]
> Azure asigna automáticamente las solicitudes de Internet al servidor Tomcat integrado que se ejecuta en los puertos estándar 80 u 8080. Aun así, si ha configurado el servidor Tomcat integrado para que se ejecute en un puerto personalizado, debe agregar una variable de entorno a la aplicación web que defina el puerto del servidor Tomcat integrado. Para ello, siga estos pasos:
>
>1. Inicie sesión en [Azure Portal].

>2. Seleccione el icono de **App Services**. (Vea el elemento 1 en la siguiente imagen.)

>3. Seleccione la aplicación web en la lista. (Vea el elemento 2 en la siguiente imagen.)

>4. Haga clic en **Configuración de la aplicación**. (Vea el elemento 3 en la siguiente imagen.)

>5. En la sección **Configuración de la aplicación**, agregue una nueva variable de entorno denominada **PORT**. A continuación, escriba el número de puerto personalizado para el valor. (Vea el elemento 4 en la siguiente imagen.)

>6. Seleccione **Guardar**. (Vea el elemento 5 en la siguiente imagen.)

> ![Guardar un número de puerto personalizado en Azure Portal][LX03]
>

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de aplicaciones de Spring Boot en Azure, consulte los siguientes artículos:

- [Implementación de una aplicación de Spring Boot en Azure App Service][2]


- [Ejecución de una aplicación de Spring Boot en un clúster de Kubernetes en Azure Container Service](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure] y [Java Tools for Visual Studio Team Services] (Herramientas de Java para Visual Studio Team Services).

Para más información acerca del proyecto de ejemplo de Spring Boot en Docker, consulte [Spring Boot en la aplicación web Docker Getting Started].

Para obtener ayuda para dar sus primeros pasos con sus propias aplicaciones de Spring Boot, consulte [Spring Initializr](https://start.spring.io/).

Para más información sobre cómo empezar a crear una sencilla aplicación de Spring Boot, consulte [Spring Initializr](https://start.spring.io/).

Para ver más ejemplos que muestran cómo usar imágenes de Docker personalizadas con Azure, consulte [Uso de una imagen personalizada de Docker para Azure Web App en Linux].

<!-- URL List -->

[Interfaz de la línea de comandos (CLI) de Azure]: /cli/azure/overview
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Azure Portal]: https://portal.azure.com/
[Creación de un registro de contenedor privado de Docker con Azure Portal]: /azure/container-registry/container-registry-get-started-portal
[Uso de una imagen personalizada de Docker para Azure Web App en Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[cliente de Docker]: https://www.docker.com/
[cuenta de Azure gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[kit para desarrolladores de Java (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[ventajas como suscriptor de MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[inicial de Spring Boot en Docker]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

