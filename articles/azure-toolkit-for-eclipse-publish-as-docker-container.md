---
title: "Publicación de un contenedor de Docker con el kit de herramientas de Azure para Eclipse| Microsoft Docs"
description: "Aprenda a publicar una aplicación web en Microsoft Azure como un contenedor de Docker con el kit de herramientas de Azure para Eclipse."
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
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Cómo publicar una aplicación web como un contenedor de Docker con el kit de herramientas de Azure para Eclipse

Los contenedores de Docker son un método ampliamente usado para la implementación de aplicaciones web en los cuales los desarrolladores pueden consolidar todos sus archivos y dependencias de proyecto en un único paquete para su implementación en un servidor. El kit de herramientas de Azure para Eclipse simplifica este proceso para los desarrolladores de Java al agregar la característica *Publish as Docker Container* (Publicar como contenedor de Docker) para la implementación en Microsoft Azure. Los pasos descritos en este artículo le llevan por las acciones necesarias para publicar sus aplicaciones en Azure como contenedores de Docker.

> [!NOTE]
>
> Para más información sobre Docker, visite el [sitio web de Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicación de la aplicación web en Azure con un contenedor de Docker

1. Abra el proyecto de aplicación web en Eclipse.

1. Use uno de los métodos siguientes para iniciar el asistente de publicación como contenedor de Docker:

   * Haga clic con el botón derecho en el proyecto en la vista **Navigator** (Navegador), haga clic en **Azure** y, después, en **Publish as Docker ContainerPublicar** (Publicar como contenedor de Docker):  ![Publish as Docker ContainerPublicar][PUB01] (Publicar como contenedor de Docker)

   * Haga clic en el menú **Publicar** (Publicar) en la barra de herramientas de Eclipse y, a continuación, haga clic en **Publish as Docker Container** (Publicar como contenedor de Docker):  ![Publish as Docker Container][PUB02] (Publicar como contenedor de Docker)

1. Cuando aparezca el asistente para **Deploy Docker Container on Azure** (Implementar contenedor de Docker en Azure), verá un cuadro de diálogo similar a la siguiente ilustración: ![Asistente para Deploy Docker Container on Azure][PUB03] (Implementar contenedor de Docker en Azure)

   a. Escriba un nombre único para su host de Docker en el cuadro de texto **Docker image name** (Nombre de imagen de Docker). (El asistente crea un nombre automáticamente, pero puede modificarlo si así lo desea).

   b. La ventana **Hosts** muestra todos los host de Docker que ha creado.
      * Si no ha creado ningún host de Docker, esta sección del cuadro de diálogo permanecerá vacía.
      * Si ya ha creado un host de Docker, puede elegir implementar su aplicación web en un host que ya exista; de lo contrario, siga los pasos que se indican a continuación para crear un nuevo host de Docker.

1. Para crear un nuevo host de Docker, haga clic en el botón **Add** (Agregar); se inicia el cuadro de diálogo **Create Docker Host**  (Crear host de Docker).
      ![Asistente para Deploy Docker Container on Azure](Implementar contenedor de Docker en Azure)[PUB04]

   a. Especifique las opciones siguientes para el host de Docker. (El asistente genera automáticamente la mayoría de las opciones, pero puede modificar las que desee).

      * **Name** (Nombre): se refiere a un nombre único para el host de Docker. (No es lo mismo que el nombre de la imagen de Docker que especificó anteriormente).

      * **Subscription** (Suscripción): especifique qué suscripción de Azure va a usar para el host.
      
      * **Region** (Región): especifique la región geográfica donde se ubicará el host.
      
      * En la pestaña **OS and Size** (Sistema operativo y tamaño):
         * **Host OS** (Sistema operativo host): especifique el sistema operativo de la máquina virtual que va a contener el host.
         * **Size** (Tamaño): especifique el tamaño de la máquina virtual del host.

      * En la pestaña **Resource Group** (Grupo de recursos):
         * **New resource group** (Nuevo grupo de recursos): le permite crear un nuevo grupo de recursos para el host.
         * **Existing resource group** (Grupo de recursos existente): le permite especificar un grupo de recursos existente desde su cuenta de Azure.

      * En la pestaña **Network** (Red):
         * **New virtual network** (Nueva red virtual): permite crear una nueva red virtual para el host.
         * **Existing virtual network** (Red virtual existente): permite especificar una red virtual existente desde su cuenta de Azure.

      * Haga clic en la pestaña **Storage** (Almacenamiento):
         * **New storage account** (Nueva cuenta de almacenamiento): permite crear una nueva cuenta de almacenamiento para el host.
         * **Existing storage account** (Cuenta de almacenamiento existente): permite especificar una cuenta de almacenamiento existente desde su cuenta de Azure.

   b. Cuando haya especificado todas las opciones anteriores, haga clic en **Next** (Siguiente).

   c. Elija una de las siguientes opciones para el inicio de sesión de máquina virtual del host de Docker:    ![Create Docker Host][PUB05] (Crear un host de Docker)

      * **Import credentials from Azure Key Vault**  (Importar credenciales de Azure Key Vault): le permite especificar un conjunto de credenciales previamente guardadas que están almacenadas en la suscripción de Azure.

      > [!NOTE]
      > Una instancia de Azure Key Vault que se crea con una cuenta o entidad de servicio específica no será accesible automáticamente por otra cuenta o entidad de servicio que comparta la misma suscripción. Para permitir que otra cuenta o entidad de servicio use Key Vault, deberá usar Azure Portal para agregar la cuenta o la entidad de servicio.

      * **New log in credentials** (Nuevas credenciales de inicio de sesión): le permite crear un nuevo conjunto de credenciales de inicio de sesión, lo que requerirá que especifique las siguientes opciones en la pestaña **VM Credentials** (Credenciales de VM):
         * **Username** (Nombre de usuario): especifica el nombre de usuario del inicio de sesión de la máquina virtual.
         * **Password** (Contraseña) y **Confirm** (Confirmar): especifica la contraseña del inicio de sesión de la máquina virtual.
         * **SSH**: especifica la configuración de Shell Secure (SSH) del host de Docker; puede elegir entre las siguientes opciones:
            * **None** (Ninguna): especifica que la máquina virtual no permitirá conexiones SSH.
            * **Auto-generate** (Generar automáticamente): esta opción crea automáticamente la configuración necesaria para la conexión mediante SSH.
            * **Import from directory** (Importar desde directorio): permite especificar un directorio que contiene un conjunto de configuraciones de SSH previamente guardadas. Más concretamente, el directorio debe contener los dos archivos siguientes:
               * *id_rsa*: este archivo contiene la identificación de RSA de un usuario.
               * *id_rsa.pub*: este archivo contiene la clave pública de RSA que se usará para la autenticación.
        
      * En la pestaña **Docker Daemon Access** (Acceso al demonio de Docker), especifique las siguientes opciones: ![Create Docker Host ][PUB06] (Crear host de Docker)

         * **Docker Daemon port** (Puerto de demonio de Docker): especifica el puerto TCP único de su host de Docker.
         * **TLS Security** (Seguridad TLS): especifica la configuración de Transport Layer Security de su host de Docker; puede elegir entre las siguientes opciones:
            * **None** (Ninguna): especifica que su máquina virtual no permitirá conexiones TLS.
            * **Auto-generate** (Generar automáticamente): esta opción crea automáticamente la configuración necesaria para la conexión mediante SSH.
            * **Import from directory** (Importar desde directorio): permite especificar un directorio que contiene un conjunto de configuraciones de SSH previamente guardadas. Más concretamente, el directorio debe contener los seis archivos siguientes:
               * *ca.pem* y *ca-key.pem*: estos archivos contienen el certificado y la clave pública de la entidad de certificación de TLS.
               * *CERT.PEM* y *key.pem*: estos archivos contienen el certificado de cliente y la clave pública que se usarán para la autenticación TLS.
               * *server.pem* y *server-key.pem*: estos archivos contienen el certificado de servidor y la clave pública del host.

   d. Cuando haya especificado todas las opciones anteriores, haga clic en **Finish** (Finalizar).

1. Cuando vuelva a aparecer el asistente para **Deploy Docker Container on Azure** (Implementar contenedor de Docker en Azure), haga clic en **Next** (Siguiente).
   ![Asistente para Deploy Docker Container on Azure][PUB07] (Implementar contenedor de Docker en Azure)

1. Cuando se encuentre en la última página del asistente, especifique las opciones siguientes:

   * **Docker container name** (Nombre del contenedor de Docker): este es el nombre único del contenedor de Docker.

   * Elija una de las siguientes imágenes de Docker:

      * **Predefined Docker image** (Imagen de Docker predefinida): permite especificar una imagen de Docker preexistente de Azure.

      > [!NOTE]
      > La lista de imágenes de Docker de este menú desplegable consta de varias imágenes que con las que se ha configurado el kit de herramientas de Azure para aplicar revisiones, de modo que su artefacto se implemente automáticamente.

      * **Custom Dockerfile** (Dockerfile personalizado): permite especificar un Dockerfile guardado anteriormente desde el equipo local.

      > [!NOTE]
      > Esta es una característica más avanzada para desarrolladores que deseen implementar su propio Dockerfile. Sin embargo, es responsabilidad de los desarrolladores que usen esta opción asegurarse de que su Dockerfile se ha creado correctamente. El kit de herramientas de Azure no valida el contenido de un Dockerfile personalizado, por lo que la implementación puede dar error si el Dockerfile tiene problemas. Además, el kit de herramientas de Azure espera que el Dockerfile personalizado contenga un artefacto de aplicación web e intentará abrir una conexión HTTP; si los desarrolladores publican un tipo de artefacto diferente, es posible que reciban errores de poca importancia después de la implementación.

   * **Port settings** (Configuración de puertos): especifica el enlace de puerto TCP único para su contenedor de Docker.
   ![Asistente para Deploy Docker Container on Azure][PUB08] (Implementar contenedor de Docker en Azure)

1. Cuando haya especificado todas las opciones anteriores, haga clic en **Finish** (Finalizar).

El kit de herramientas de Azure comenzará a implementar la aplicación web en Azure en un contenedor de Docker. 

## <a name="see-also"></a>Otras referencias
Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Sign In Instructions for the Azure Toolkit for Eclipse] (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse)
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
* [Kit de herramientas de Azure para IntelliJ]
  * [Novedades del kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * [Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]
  * [Creación de una aplicación web Hello World para Azure en IntelliJ]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure] y [Java Tools for Visual Studio Team Services] (Herramientas de Java para Visual Studio Team Services).

Para obtener recursos adicionales para Docker, consulte el [sitio web de Docker].

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md (Instrucciones de inicio de sesión del kit de herramientas de Azure para Eclipse)
[Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[sitio web de Docker]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png

