---
title: "Publicación de un contenedor de Docker con el kit de herramientas de Azure para IntelliJ | Microsoft Docs"
description: "Aprenda a publicar una aplicación web en Microsoft Azure como un contenedor de Docker con el kit de herramientas de Azure para IntelliJ."
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
ms.openlocfilehash: f8ae15f4a5edb3cd84b5bb2302e1a5f709b57f82
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>Cómo publicar una aplicación web como un contenedor de Docker mediante el kit de herramientas de Azure para IntelliJ

Los contenedores de Docker son un método ampliamente usado para la implementación de aplicaciones web en las que los desarrolladores pueden consolidar todos sus archivos de proyecto y dependencias en un único paquete para su implementación en un servidor. El kit de herramientas de Azure para IntelliJ simplifica este proceso para los desarrolladores Java añadiendo *Publish as Docker Container* (Publicar como contenedor de Docker) características de implementación en Microsoft Azure y los pasos descritos en este artículo le llevarán a los pasos necesarios para publicar sus aplicaciones en Azure como contenedores de Docker.

> [!NOTE]
>
> Para más información acerca de Docker, consulte el [sitio web de Docker].
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicación de su aplicación web en Azure con un contenedor de Docker

> [!NOTE]
>
> Para publicar la aplicación web, debe crear un artefacto preparado para la implementación. Para más información, consulte [información adicional sobre la creación de artefactos](#artifacts) más adelante en este artículo.
>
> Además, después de haber completado el asistente para implementación al menos una vez, la mayor parte de la configuración que especifique en este tutorial se utilizará como valores predeterminados para cuando vuelva a ejecutar el asistente.
>

1. Abra el proyecto de aplicación web en IntelliJ.

1. Use uno de los métodos siguientes para iniciar el Asistente para publicar como contenedor de Docker:

   * Haga clic con el botón derecho en la ventana de la herramienta **Proyecto**, después, haga clic en **Azure** y, finalmente, haga clic en **Publish as Docker Container**:  ![Publish as Docker Container][PUB01] (Publicar como contenedor de Docker).

   * Haga clic en el menú **Publish Group** (Publicar grupo) en la barra de herramientas de Intellij y, después, haga clic en **Publish as Docker Container**:  ![Publish as Docker Container][PUB02] (Publicar como contenedor de Docker).

1. Cuando aparezca **Deploy Docker Container on Azure wizard** (Asistente para implementar un contenedor de Docker en Azure), verá un cuadro de diálogo similar al de la siguiente ilustración: ![Deploy Docker Container on Azure Wizard][PUB03] (Asistente para implementar un contenedor de Docker en Azure).

   a. Introduzca un nombre único en el cuadro de texto para su host de Docker en el cuadro de texto **Docker image name** (Nombre de la imagen de Docker). (El asistente creará un nombre automáticamente, pero puede modificarlo si así lo desea).

   b. La ventana **Hosts** mostrará todos los hosts de Docker que haya creado.
      * Si no ha creado ningún host de Docker, esta sección del cuadro de diálogo permanecerá vacía.
      * Si ya ha creado un host de Docker, puede implementar su aplicación web en un host que ya exista; de lo contrario, siga los pasos que se indican a continuación para crear un nuevo host de Docker.

1. Para crear un nuevo host de Docker, haga clic en el signo más verde ("**+**"); esto iniciará el cuadro de diálogo **Create Docker Host** (Crear host de Docker).
      ![Deploy Docker Container on Azure Wizard][PUB04a] (Asistente para implementar un contenedor de Docker en Azure)

   a. Especifique las opciones siguientes para el host de Docker. (El asistente generará automáticamente la mayoría de las opciones, pero puede modificar las que desee personalizar).

      * **Name** (Nombre): se refiere a un nombre único para el host de Docker. (No es lo mismo que el nombre de la imagen de Docker que especificó anteriormente).

      * **Subscription** (Suscripción): especifique qué suscripción de Azure va a usar para el host.
      
      * **Region** (Región): especifique la región geográfica donde se ubicará el host.
      
      * En la pestaña **OS and Size** (Sistema operativo y tamaño):
         * **Host OS** (Sistema operativo del host): especifique el sistema operativo de la máquina virtual que va a contener el host.
         * **Tamaño**: especifique el tamaño de la máquina virtual del host.

      * En la pestaña **Resource Group** (Grupos de recursos):
         * **New resource group** (Nuevo grupo de recursos): le permite crear un nuevo grupo de recursos para el host.
         * **Existing resource group** (Grupo de recursos existente): permite especificar un grupo de recursos existente desde su cuenta de Azure.

      * En la pestaña**Network** (Red):
         * **New virtual network** (Nueva red virtual): permite crear una nueva red virtual para el host.
         * **Existing virtual network** (Red virtual existente): permite especificar una red virtual existente desde su cuenta de Azure.

      * Haga clic en la pestaña **Storage** (Almacenamiento).
         * **New storage account** (Nueva cuenta de almacenamiento): permite crear una nueva cuenta de almacenamiento para el host.
         * **Existing storage account** (Cuenta de almacenamiento existente): permite especificar una cuenta de almacenamiento existente en su cuenta de Azure.

   b. Cuando haya especificado las opciones anteriores, haga clic en **Next** (Siguiente).

   c. Elija una de las siguientes opciones para el inicio de sesión de máquina virtual del host de Docker:    ![Create Docker Host][PUB05] (Crear un host de Docker).

      * **Import credentials from Azure Key Vault**  (Importar credenciales de Azure Key Vault): le permite especificar un conjunto de credenciales previamente guardadas que están almacenadas en la suscripción de Azure.

      > [!NOTE]
      > Una instancia de Azure Key Vault que se crea con una cuenta específica o entidad de servicio no será accesible automáticamente por otra cuenta o entidad de servicio que comparta la misma suscripción. Para permitir que otra cuenta o entidad de servicio use Key Vault, necesitará usar Azure Portal para agregar la cuenta o la entidad de servicio.

      * **New log in credentials** (Nuevas credenciales de registro): le permite crear un nuevo conjunto de credenciales de inicio de sesión, que requerirá que especifique las siguientes opciones en la pestaña **VM Credentials** (Credenciales de VM):
         * **Username** (nombre de usuario): especifica el nombre de usuario para el inicio de sesión de máquina virtual.
         * **Password** (Contraseña) y **Confirm** (Confirmar): especifica la contraseña para el inicio de sesión de la máquina virtual.
         * **SSH**: especifica la configuración de Shell Secure (SSH) para el host de Docker; puede elegir entre las siguientes opciones:
            * **None** (Ninguno): especifica que la máquina virtual no permitirá las conexiones SSH.
            * **Auto-generate** (Generar automáticamente): esta opción creará automáticamente los ajustes necesarios para la conexión a través de SSH.
            * **Import from directory** (Importar desde el directorio): permite especificar un directorio que contiene un conjunto de configuraciones de SSH previamente guardadas. Más concretamente, el directorio debe contener los dos archivos siguientes:
               * *id_rsa*: este archivo contiene la identificación de RSA para un usuario.
               * *id_rsa.pub*: este archivo contiene la clave pública de RSA que se usará para la autenticación.
        
      * En la pestaña **Docker Daemon Access** (Acceso de demonio de Docker), especifique las siguientes opciones: ![Create Docker Host ][PUB06] (Crear host de Docker).

         * **Docker Daemon port** (Puerto de demonio de Docker): especifica el puerto TCP único para su host Docker.
         * **TLS Security** (Seguridad de TLS): especifica la configuración de Transport Layer Security para su host de Docker; puede elegir entre las siguientes opciones:
            * **None** (Ninguna): especifica que su máquina virtual no permitirá conexiones TLS.
            * **Auto-generate** (Generar automáticamente): esta opción creará automáticamente los ajustes necesarios para la conexión a través de TLS.
            * **Import from directory** (Importar desde el directorio): permite especificar un directorio que contiene un conjunto de configuraciones TLS guardadas previamente. Más concretamente, el directorio debe contener los seis archivos siguientes:
               * *ca.pem* y *ca-key.pem*: estos archivos contienen el certificado y la clave pública para la entidad de certificación TLS.
               * *CERT.PEM* y *key.pem*: estos archivos contienen el certificado de cliente y una clave pública que se usará para la autenticación TLS.
               * *Server.PEM* y *key.pem server*: estos archivos contienen el certificado de servidor y una clave pública para el host.

   d. Cuando haya especificado todas las opciones anteriores, haga clic en **Finish** (Finalizar).

1. Cuando vuelva a aparecer **Deploy Docker Container on Azure wizard** (Asistente para implementar un contenedor de Docker en Azure), haga clic en **Siguiente**.
   ![Deploy Docker Container on Azure Wizard][PUB07] (Asistente para implementar un contenedor de Docker en Azure)

1. Cuando se encuentre en la última página del asistente, realice las acciones siguientes:

   * **Nombre del contenedor de docker**: este es el nombre único para el contenedor de Docker.

   * Elija una de las siguientes imágenes de Docker:

      * **Imagen de Docker predefinida**: permite especificar una imagen de Docker preexistente de Azure.

      > [!NOTE]
      > La lista de imágenes de Docker en este menú desplegable consta de varias imágenes que ha configurado el kit de herramientas de Azure para aplicar la revisión para que el artefacto se implemente automáticamente.

      * **Dockerfile personalizado**: permite especificar un Dockerfile guardado anteriormente desde el equipo local.

      > [!NOTE]
      > Esta es una característica más avanzada para los desarrolladores que deseen implementar su propio Dockerfile. Sin embargo, depende de los desarrolladores que usen esta opción el asegurarse de que su Dockerfile se ha creado correctamente. El kit de herramientas de Azure no valida el contenido de un Dockerfile personalizado, por lo que la implementación puede fallar si el Dockerfile tiene problemas. Además, el kit de herramientas de Azure espera que el Dockerfile personalizado contenga un artefacto de la aplicación web e intentará abrir una conexión HTTP; si los desarrolladores publican un tipo de artefacto diferente, es posible que reciban errores inofensivos después de implementar.

   * **Port settings** (Configuración de puertos): especifica el enlace de puerto TCP único para su contenedor de Docker.
   ![Deploy Docker Container on Azure Wizard][PUB08] (Asistente para implementar un contenedor de Docker en Azure)

1. Cuando haya completado todos los pasos anteriores, haga clic en **Finish** (Finalizar).

El kit de herramientas de Azure empezará a implementar la aplicación web en Azure en un contenedor de Docker y, a menos que se haya configurado IntelliJ para implementarse en segundo plano, aparecerá un cuadro de diálogo que muestra el progreso de la implementación. 
![Progreso de la implementación][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>Información adicional sobre la creación de artefactos

Para crear un artefacto preparado para la implementación, siga estos pasos:

1. Abra el proyecto de aplicación web en IntelliJ.

1. Haga clic en **Archivo** y después en **Estructura del proyecto**.
   ![Menú Estructura del proyecto][ART01]

1. Haga clic en el signo más verde ("**+**") para agregar un artefacto y, a continuación, haga clic en **Aplicación Web: artefacto**.
   ![Agregar artefacto][ART02]

1. Dé un nombre al artefacto asegurándose de no agregar la extensión ".war" y, a continuación, haga clic en **Aceptar**.
   ![Propiedades del artefacto][ART03]

Para más información sobre cómo crear artefactos en IntelliJ, consulte [Configuring Artifacts] (Configuración de artefactos) en el sitio web de JetBrains.

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
  * [Sign In Instructions for the Azure Toolkit for IntelliJ] (Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ)
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
[Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instrucciones de inicio de sesión del kit de herramientas de Azure para IntelliJ)
[Novedades del kit de herramientas de Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[sitio web de Docker]: https://www.docker.com/
[Configuring Artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html (Configuración de artefactos)

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png

