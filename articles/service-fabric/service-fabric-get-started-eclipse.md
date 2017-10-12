---
title: Complemento de Azure Service Fabric para Eclipse | Microsoft Docs
description: Empezar a trabajar con el complemento de Service Fabric para Eclipse.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2016
ms.author: saysa
ms.openlocfilehash: 4fa77da8665908553072792d7f2ede47bf5567dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Complemento de Service Fabric para el desarrollo de aplicaciones Java de Eclipse
Eclipse es uno de los entornos de desarrollo integrado (IDE) por los desarrolladores de Java. En este artículo se describe cómo configurar un entorno de desarrollo de Eclipse para que funcione con Azure Service Fabric. Aprenderá instalar el complemento de Service Fabric, crear una aplicación de Service Fabric e implementarla en un clúster de Service Fabric local o remoto en Service Fabric.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Instalación o actualización del complemento Service Fabric en Eclipse Neon
En Eclipse se puede instalar un complemento Service Fabric. El complemento puede ayudar a simplificar el proceso de compilación e implementación de servicios de Java.

1.  Asegúrese de que tiene instaladas las versiones más reciente tanto de Eclipse Neon como de Buildship (1.0.17 o una versión posterior):
    -   Para ver las versiones de los componentes instalados, en Eclipse Neon, vaya a **Help** > **Installation Details** (Ayuda > Detalles de instalación).
    -   Para actualizar Buildship, consulte [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: complementos de Eclipse para Gradle).
    -   Para buscar actualizaciones de Eclipse Neon e instalarlas, vaya a **Help** > **Check for Updates** (Ayuda > Buscar actualizaciones).

2.  Para instalar el complemento Service Fabric, en Eclipse Neon, vaya a **Help** > **Install New Software** (Ayuda > Instalar software nuevo).
  1.    En el cuadro **Trabajar con**, escriba **http://dl.microsoft.com/eclipse**.
  2.    Haga clic en **Agregar**.

         ![Complemento Service Fabric para Eclipse Neon][sf-eclipse-plugin-install]
  3.    Seleccione el complemento Service Fabric y, después, haga clic en **Next** (Siguiente).
  4.    Complete los pasos de la instalación y acepte los términos de licencia del software de Microsoft.

Si el complemento Service Fabric ya está instalado, asegúrese de que tiene la versión más reciente. Para comprobar si hay actualizaciones disponibles, vaya a **Help** > **Installation Details** (Ayuda > Detalles de instalación). En la lista de complementos instalados, seleccione Service Fabric y, después, haga clic en **Update** (Actualizar). Se instalarán las actualizaciones disponibles.

> [!NOTE]
> Si el proceso de instalación o actualización del complemento Service Fabric se realiza lentamente, es posible que se deba a la configuración de Eclipse. Eclipse recopila metadatos de todos los cambios para actualizar los sitios que están registrados en la instancia de Eclipse. Para acelerar el proceso de búsqueda e instalación de una actualización del complemento Service Fabric, vaya a **Available Software Sites** (Sitios de software disponibles). Desactive las casillas de todos los sitios, excepto del que apunta a la ubicación del complemento Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Si Eclipse no funciona según lo previsto en el equipo Mac (o necesita que ejecute como superusuario), vaya a la carpeta **ECLIPSE_INSTALLATION_PATH** y navegue a la subcarpeta **Eclipse.app/Contents/MacOS**. Inicie Eclipse ejecutando `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Creación de una aplicación de Service Fabric en Eclipse

1.  En Eclipse Neon, vaya **File** > **New** > **Other** (Archivo > Nuevo > Otro). Seleccione **Service Fabric Project** (Proyecto de Service Fabric) y haga clic en **Next** (Siguiente).

    ![Página 1 del nuevo proyecto de Service Fabric][create-application/p1]

2.  Escriba el nombre del proyecto y haga clic en **Next** (Siguiente).

    ![Página 2 del nuevo proyecto de Service Fabric][create-application/p2]

3.  En la lista de plantillas, seleccione **Service Template** (Plantilla de servicio). Seleccione el tipo de plantilla de servicio (Actor, Stateless, Container o Guest Binary) y, después, haga clic en **Next** (Siguiente).

    ![Página 3 del nuevo proyecto de Service Fabric][create-application/p3]

4.  Escriba el nombre y los detalles del servicio y, después, haga clic en **Finish** (Finalizar).

    ![Página 4 del nuevo proyecto de Service Fabric][create-application/p4]

5. Al crear el primer proyecto de Service Fabric, en el cuadro de diálogo **Open Associated Perspective** (Abrir perspectiva asociada), haga clic en **Yes** (Sí).

    ![Página 5 del nuevo proyecto de Service Fabric][create-application/p5]

6.  Así es el nuevo proyecto:

    ![Página 6 del nuevo proyecto de Service Fabric][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Creación e implementación de una aplicación de Service Fabric en Eclipse

1.  Haga clic con el botón derecho en la nueva aplicación de Service Fabric y, después, seleccione **Service Fabric**.

    ![Menú contextual de Service Fabric][publish/RightClick]

2. En el submenú, seleccione la opción que desee:
    -   Para compilar la aplicación sin realizar una limpieza, haga clic en **Build Application** (Compilar aplicación).
    -   Para realizar una compilación limpia de la aplicación, haga clic en **Rebuild Application** (Recompilar aplicación).
    -   Para limpiar la aplicación de los artefactos compilados, haga clic en **Clean Application** (Limpiar aplicación).

3.  Desde este menú también se puede implementar, anular la implementación y publicar una aplicación:
    -   Para realizar la implementación en un clúster local, haga clic en **Deploy Application** (Implementar aplicación).
    -   En el cuadro de diálogo **Publish Application** (Publicar aplicación), seleccione un perfil de publicación:
        -  **Local.json**
        -  **Cloud.json**

     Estos archivos JSON (notación de objetos JavaScript) almacenan información (como los puntos de conexión de la conexión y la información de seguridad) necesaria para conectarse a un clúster local o en la nube (Azure).

  ![Menú Publish (Publicar) de Service Fabric][publish/Publish]

Una forma alternativa de implementar una aplicación de Service Fabric es mediante las configuraciones de ejecución de Eclipse.

  1.    Vaya a **Run** > **Run Configurations** (Ejecución > Configuraciones de ejecución).
  2.    En **Gradle Project** (Proyecto Gradle), seleccione la configuración de ejecución **ServiceFabricDeployer**.
  3.    En el panel derecho, en la pestaña **Arguments** (Argumentos), en **publishProfile**, seleccione **local** o **cloud** (nube).  El valor predeterminado es **local**. Para realizar la implementación en un clúster remoto o en la nube, seleccione **cloud** (nube).
  4.    Para asegurarse de que se rellena la información correcta en los perfiles de publicación, edite **Local.json** o **Cloud.json**, según sea necesario. Puede agregar o actualizar los detalles del punto de conexión y las credenciales de seguridad.
  5.    Asegúrese de que **Working Directory** (Directorio de trabajo) apunta a la aplicación que desea implementar. Para cambiar la aplicación, haga clic en el botón **Workspace...** (Espacio de trabajo) y seleccione la aplicación que desee.
  6.    Haga clic en **Apply** (Aplicar) y luego en **Run** (Ejecutar).

La aplicación se compila e implementa en un momento. El estado de implementación se puede implementar en Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Incorporación de un servicio de Service Fabric a una aplicación de Service Fabric

Para agregar un servicio de Service Fabric a una aplicación de Service Fabric existente, siga estos pasos:

1.  Haga clic con el botón derecho en el proyecto al que desea agregar un servicio y, después, haga clic en **Service Fabric**.

    ![Página 1 de Agregar servicio de Service Fabric][add-service/p1]

2.  Haga clic en **Add Service Fabric Service** (Agregar servicio de Service Fabric) y complete el conjunto de pasos necesarios para agregar un servicio al proyecto.
3.  Seleccione la plantilla de servicio que desea agregar al proyecto y haga clic en **Next** (Siguiente).

    ![Página 2 de Agregar servicio de Service Fabric][add-service/p2]

4.  Escriba el nombre del servicio (y otros detalles, en caso de que sea necesario) y, después, haga clic en el botón **Add Service** (Agregar servicio).  

    ![Página 3 de Agregar servicio de Service Fabric][add-service/p3]

5.  Después de agregar el servicio, la estructura general del proyecto tiene un aspecto similar al siguiente proyecto:

    ![Página 4 de Agregar servicio de Service Fabric][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Edición de las versiones de manifiesto de su aplicación Java de Service Fabric

Para editar las versiones de manifiesto, haga clic con el botón derecho en el proyecto, vaya a **Service Fabric** y seleccione **Editar versiones de manifiesto...**  en el menú desplegable. En el asistente, puede actualizar las versiones del manifiesto de aplicación, el manifiesto de servicio y las versiones de los paquetes de **Code**, **Config** y **Data**.

Si activa la opción **Actualizar automáticamente las versiones de aplicación y servicio** y, a continuación, actualice una versión; las versiones de manifiesto se actualizarán automáticamente. Para ver un ejemplo, primero active la casilla y, después, actualice la versión de **Code** de 0.0.0 a 0.0.1 y haga clic en **Finalizar**; la versión del manifiesto de servicio y la versión del manifiesto de aplicación se actualizarán automáticamente a 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Actualización de la aplicación Java de Service Fabric

Para un escenario de actualización, supongamos que creó el proyecto **App1** mediante el complemento Service Fabric de Eclipse. Para implementarlo, usó el complemento para crear una aplicación llamada **fabric:/App1Application**. El tipo de aplicación es **App1AppicationType** y su versión es 1.0. Ahora desea actualizar la aplicación sin interrumpir la disponibilidad.

En primer lugar, realice en la aplicación los cambios que desee y vuelva a compilar el servicio modificado. Actualice el archivo de manifiesto del servicio modificado (ServiceManifest.xml) con las versiones actualizadas para el servicio (y Code, Config o Data, lo que corresponda). Modifique también el manifiesto de la aplicación (ApplicationManifest.xml) con el número de la versión actualizada de la aplicación y el servicio modificado.  

Para actualizar la aplicación mediante Eclipse Neon, puede crear un perfil de configuración de ejecución duplicado. A continuación, utilícelo para actualizar la aplicación según sea necesario.

1.  Vaya a **Run** > **Run Configurations** (Ejecución > Configuraciones de ejecución). En el panel izquierdo, haga clic en la flecha pequeña situada a la izquierda de **Gradle Project** (Proyecto Gradle).
2.  Haga clic con el botón derecho en **ServiceFabricDeployer** y seleccione **Duplicate** (Duplicar). Escriba un nuevo nombre para esta configuración, por ejemplo, **ServiceFabricUpgrader**.
3.  En el panel derecho, en la pestaña **Arguments** (Argumentos), cambie **-Pconfig='deploy'** a **-Pconfig='upgrade'** y haga clic en **Apply** (Aplicar).

Este proceso crea y guarda un perfil de configuración de ejecución que se puede usar en cualquier momento para actualizar la aplicación. También obtiene la versión del tipo de aplicación más reciente del archivo de manifiesto de la aplicación.

La aplicación tarda unos minutos en actualizarse. La actualización de la aplicación se puede supervisar en Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migración de aplicaciones de Java de Service Fabric anteriores para su uso con Maven
Recientemente se han transferido las bibliotecas de Java de Service Fabric desde el SDK de Java de Service Fabric al repositorio de Maven. Aunque las nuevas aplicaciones que genere con Eclipse generarán proyectos actualizados (que podrán trabajar con Maven), puede actualizar las aplicaciones existentes sin estado o de actor de Service Fabric, que utilizaban anteriormente el SDK de Java de Service Fabric, para que usen las dependencias de Java de Service Fabric de Maven. Siga los pasos mencionados [aquí](service-fabric-migrate-old-javaapp-to-use-maven.md) para asegurarse de que las aplicaciones anteriores funcionan con Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
