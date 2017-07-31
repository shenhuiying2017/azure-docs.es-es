---
title: "Publicación de una aplicación Spring Boot como contenedor de Docker con el kit de herramientas de Azure para Eclipse | Microsoft Docs"
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
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Publicación de una aplicación Spring Boot como contenedor de Docker con el kit de herramientas de Azure para Eclipse

**[Spring Framework]** es una solución de código abierto que ayuda a los desarrolladores de Java a crear aplicaciones de nivel empresarial. Uno de los proyectos más populares que se basa en dicha plataforma es [Spring Boot], que proporciona un enfoque simplificado para crear aplicaciones de Java independientes.

**[Docker]** es una solución de código abierto que ayuda a los desarrolladores a automatizar la implementación, el escalado y la administración de sus aplicaciones que se ejecutan en contenedores.

Este tutorial le guía por los pasos necesarios para implementar una aplicación Spring Boot como un contenedor de Docker en Microsoft Azure con el kit de herramientas de Azure para Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>Clonación del repositorio de aplicaciones de Docker de Spring Boot predeterminado

### <a name="importing-the-public-repository"></a>Importación del repositorio público

Los siguientes pasos le guiarán por la clonación del repositorio de Docker de Spring Boot al equipo local mediante Eclipse. Si desea usar una línea de comandos, consulte [Implementación de una aplicación de Spring Boot en Linux en Azure Container Service][Deploy Spring Boot on Linux in ACS].

1. Abra Eclipse.

1. Haga clic en **Archivo** y, luego, en **Importar**.

   ![Menú de importación de archivo][CL01]

1. Cuando se abra el cuadro de diálogo **Importar**:

   a. Expanda **GIT**.

   b. Resalte **Proyectos de GIT**.
   
   c. Haga clic en **Siguiente**.

   ![Cuadro de diálogo de importación][CL02]

1. En la página **Seleccionar origen del repositorio**:

   a. Resalte **Clonar URI**.
   
   b. Haga clic en **Siguiente**.

   ![Seleccionar origen del repositorio][CL03]

1. En la página **Repositorio GIT de origen**:

   a. Escriba `https://github.com/spring-guides/gs-spring-boot-docker.git` para el **URI** del repositorio. Con esta acción se deberían rellenar automáticamente los campos **Host** y **Ruta de acceso de repositorio** con los valores correctos.
   
   b. El repositorio de Spring Boot es público, por lo que no debe escribir el nombre de usuario ni la contraseña de GIT.
   
   c. Haga clic en **Siguiente**.

   ![Repositorio de GIT local][CL04]

1. En la página **Rama**, haga clic en **Siguiente**.

   ![Rama][CL05]

1. En la página **Destino local**:

   a. Especifique la carpeta local donde desea que esté el repositorio local.
   
   b. Haga clic en **Siguiente**.

   ![Destino local][CL06]

1. En la página **Seleccionar un asistente para usar en la importación de proyectos**:

   a. Seleccione **Import as a general project** (Importar como proyecto general).
   
   b. Haga clic en **Siguiente**.

   ![Destino local][CL07]

1. En la página **Importar proyectos**:

   a. Especifique el **nombre del proyecto**.
   
   b. Haga clic en **Finalizar**

   ![Importación de proyectos][CL08]

1. Una vez que el repositorio se clona correctamente, verá todos los archivos en Eclipse.

   ![Repositorio local][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>Creación de un proyecto de Maven desde el repositorio local

El repositorio de Docker de Spring Boot contiene un proyecto de Maven finalizado que usará para este tutorial. 

1. Haga clic en **Archivo** y, luego, en **Importar**.

   ![Menú de importación de archivo][CL01]

1. Cuando se abra el cuadro de diálogo **Importar**:

   a. Expanda **Maven**.
   
   b. Resalte **Existing Maven Projects** (Proyectos existentes de Maven).
   
   c. Haga clic en **Siguiente**.

   ![Cuadro de diálogo de importación][MV01]

1. En la página **Maven Projects** (Proyectos de Maven):

   a. Especifique la carpeta `complete` dentro del repositorio local para el **directorio raíz**.
   
   b. Expanda la sección **Avanzado** y escriba un nombre personalizado para la **plantilla de nombre**.
   
   c. Active la casilla para el archivo `pom.xml` dentro del proyecto.
   
   d. Haga clic en **Finalizar**

   ![Proyectos de Maven][MV02]

1. Cuando el proyecto de Maven se abra correctamente, verá un segundo proyecto en Eclipse.

   ![Proyecto local de Maven][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>Compilación de una aplicación Spring Boot con Maven

1. En el Explorador de proyectos de Eclipse, resalte el proyecto de Maven.

1. Haga clic en **Ejecutar**, luego en **Ejecutar como** y, luego, **Maven build** (Compilación de Maven).

   ![Ejecución como compilación de Maven][BU01]

1. Una vez que la aplicación se compile correctamente, la ventana de la consola mostrará el estado.

   ![Compilación correcta de Maven][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicación de su aplicación web en Azure con un contenedor de Docker

1. En el Explorador de proyectos de Eclipse, resalte el proyecto de Maven.

1. Haga clic en el menú **Publicar** de Azure y, luego, en **Publicar como contenedor de Docker**.

   ![Publicar como contenedor de Docker][PU01]

1. Cuando aparezca el cuadro de diálogo **Implementación del contenedor de Docker en Azure**:

   a. Escriba un **nombre de imagen de Docker** personalizado.
   
   b. Para **Artifact to deploy** (Artefacto para implementar), especifique la ruta de acceso al archivo `gs-spring-boot-docker-0.1.0.jar` que acaba de crear.

   ![Especificar opciones de Docker][PU02]

   Se mostrarán todos los hosts de Docker existentes. Si decide implementar en un host existente, puede ir al paso 4. En caso contrario, use estos pasos para crear un nuevo host:

   a. Haga clic en **Agregar**.

      ![Adición de un nuevo host de Docker][PU03]

   b. Cuando se muestra el cuadro de diálogo **Create Docker Host** (Crear host de Docker), puede aceptar los valores predeterminados o puede especificar cualquier configuración personalizada para el nuevo host de Docker. (Las descripciones detalladas de las distintas configuraciones están disponibles en el artículo [Publicación de una aplicación web como contenedor de Docker con el kit de herramientas de Azure para IntelliJ][Publish Container with Azure Toolkit]). Haga clic en **Siguiente** cuando haya especificado qué configuración usar.

      ![Especificar las opciones de host de Docker][PU04]

   c. Puede elegir usar las credenciales de inicio de sesión existentes desde Azure Key Vault, o puede escribir las nuevas credenciales de inicio de sesión de Docker. Haga clic en **Finalizar** cuando haya especificado las opciones.

      ![Especificar credenciales del host de Docker][PU05]

1. Resalte el host de Docker y, después, haga clic en **Siguiente**.

   ![Seleccionar el host de Docker que se va a usar][PU06]

1. En la última página del cuadro de diálogo **Deploy Docker Container on Azure** (Implementar un contenedor de Docker en Azure), debe especificar las siguientes opciones:

   a. Puede elegir especificar un nombre personalizado para el contenedor que va a hospedar el contenedor de Docker, o puede aceptar el valor predeterminado.

   b. Debe especificar los puertos TCP para el host de Docker mediante la sintaxis siguiente: "*[puerto externo]*:*[puerto interno]*. Por ejemplo, "80:8080" especifica un puerto externo de "80" y el puerto de Spring Boot interno predeterminado de "8080".
   
      Si ha personalizado el puerto interno, (por ejemplo, mediante la edición del archivo *application.yml*), debe especificar el número de puerto para que el enrutamiento correcto se produzca en Azure.

   c. Una vez configuradas estas opciones, haga clic en **Finalizar**.

   ![Implementación del contenedor de Docker en Azure][PU07]

1. Cuando el kit de herramientas de Azure ha terminado la publicación, el registro de actividad de Azure mostrará **Publicado** en el estado.

   ![Host de Docker correctamente implementado][PU08]

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

