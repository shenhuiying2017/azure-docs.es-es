---
title: "Introducción al complemento de Eclipse para Azure Service Fabric | Microsoft Docs"
description: "Introducción al complemento de Eclipse para Azure Service Fabric."
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
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Introducción al complemento de Eclipse para el desarrollo de aplicaciones de Java de Service Fabric
Eclipse es uno de los IDE más usados por los desarrolladores de Java. En este artículo se describe cómo puede configurar el entorno de desarrollo de Eclipse para trabajar con Service Fabric. Este artículo le ayudará a instalar el complemento, crear aplicaciones de Service Fabric e implementar una aplicación de Service Fabric en el clúster de Service Fabric local o remoto.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Instalación o actualización del complemento para Service Fabric en Eclipse Neon
Service Fabric ofrece un complemento **IDE de Eclipse para desarrolladores de Java** que puede simplificar el proceso de compilación e implementación de servicios de Java.

1. Asegúrese de tener instaladas las versiones más recientes de Eclipse **Neon** y Buildship (1.0.17 o versiones posteriores). Puede comprobar las versiones de los componentes instalados en ``Help => Installation Details``. Para actualizar Buildship, siga [estas instrucciones][buildship-update]. Para comprobar si tiene la versión más reciente de Eclipse Neon y para actualizar, acuda a ``Help => Check for Updates``.

2. Para instalar el complemento para Service Fabric, elija ``Help => Install New Software...``.
  1. En el cuadro de texto "Work with" (Trabajar con), escriba: ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Haga clic en Agregar.

  ![Complemento de Service Fabric para Eclipse Neon][sf-eclipse-plugin-install]

  3. Elija el complemento de Service Fabric y haga clic en Siguiente.
  4. Lleve a cabo la instalación y acepte el contrato de licencia de usuario final.

Si ya tiene instalado el complemento de Eclipse para Service Fabric, compruebe que se trata de la versión más reciente. Puede comprobar si se puede actualizar aún más acudiendo a: ``Help => Installation Details``. A continuación, busque Service Fabric en la lista de complementos instalados y haga clic en actualizar. Si hay alguna actualización pendiente, se recupera y se instala.

> [!NOTE]
> Si la instalación o actualización del complemento de Eclipse para Service Fabric tarda mucho tiempo en su instancia de Eclipse, es porque este siempre intenta capturar metadatos de todos los cambios que se han producido en todas las actualizaciones de sitios registrados con la instancia de Eclipse. Así que para que sea más rápido, puede utilizar este truco: vaya a `Available Software Sites` y desactive todo excepto lo que apunta a la ubicación del complemento para Service Fabric `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Creación de una aplicación de Service Fabric con Eclipse

1. Vaya a ``File => New => Other``. Seleccione ``Service Fabric Project``. Haga clic en ``Next``.

    ![Página 1 del nuevo proyecto de Service Fabric][create-application/p1]

2. Asigne un nombre al proyecto. Haga clic en ``Next``.

    ![Página 2 del nuevo proyecto de Service Fabric][create-application/p2]

3. Seleccione una plantilla de servicio en el conjunto de plantillas disponibles (de actor, sin estado, de contenedor o ejecutable invitado). Haga clic en ``Next``.

    ![Página 3 del nuevo proyecto de Service Fabric][create-application/p3]

4. Escriba el nombre de servicio o los detalles de servicio pertinentes en esta página y haga clic en ``Finish``.

    ![Página 4 del nuevo proyecto de Service Fabric][create-application/p4]

5. Al crear su primer proyecto de Service Fabric, se le preguntará si desea establecer la perspectiva de Service Fabric, seleccione ``yes`` para continuar.

    ![Página 5 del nuevo proyecto de Service Fabric][create-application/p5]

6. Después de crearlo correctamente el proyecto tiene el siguiente aspecto:

    ![Página 6 del nuevo proyecto de Service Fabric][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Compilación e implementación de la aplicación de Service Fabric con Eclipse

* Haga clic con el botón derecho en la aplicación de Service Fabric que acaba de crear. Seleccione la opción ``Service Fabric`` en el menú contextual. Esto abrirá un submenú con varias opciones, que tendrá el aspecto siguiente:

    ![Menú contextual de Service Fabric][publish/RightClick]

  Una vez que haga clic en las opciones de compilación, recopilación y limpieza, llevará a cabo las acciones previstas.
  - ``Build Application`` compilará la aplicación sin realizar una limpieza
  - ``Rebuild Application`` llevará a cabo una compilación limpia de la aplicación
  - ``Clean Application`` limpiará la aplicación de los artefactos integrados


* También puede elegir implementar, anular la implementación y publicar su aplicación desde este menú.
  - ``Deploy Application`` realizará la implementación en el clúster local
  - ``Publish Application...`` le preguntará de cuál perfil de publicación desea realizar la selección, entre ``Local.json`` y ``Cloud.json``. Estos archivos JSON se usan para almacenar información (como la información de seguridad y los puntos de conexión de la conexión) que es necesaria para conectarse al clúster local o en la nube (Azure).

  ![Menú contextual de Service Fabric][publish/Publish]

* Hay una forma alternativa para implementar la aplicación de Service Fabric mediante las configuraciones de ejecución de Eclipse.

  1. Elija ``Run => Run Configurations``. Seleccione la configuración de ejecución ``ServiceFabricDeployer`` en ``Grade Project``.
  2. En la pestaña ``Arguments`` en el panel derecho, especifique **local** o **cloud** como ``publishProfile``. La configuración predeterminada es **local**. Para implementar en un clúster remoto, o en la nube seleccione **cloud**.
  3. Asegúrese de que la información adecuada se rellena en los perfiles de publicación, editando `Local.json` o `Cloud.json` según corresponda, con detalles de punto de conexión y credenciales de seguridad, si las hubiera.
  4. Asegúrese de que ``Working Directory`` en el panel derecho de ``Grade Project`` apunta a la aplicación que desea implementar. Si no es así, haga clic en el botón ``Workspace...`` y seleccione la aplicación que desee.
  5. Haga clic en **Apply** (Aplicar) y en **Run** (Ejecutar).

La aplicación se compila e implementa en un momento. Puede supervisar su estado en Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Incorporación de un nuevo servicio de Service Fabric a su aplicación Service Fabric

Agregar un nuevo servicio de Service Fabric a una aplicación existente de Service Fabric es posible mediante los siguientes pasos:

1. Haga clic con el botón derecho en el proyecto al que desea agregar un servicio para abrir el menú contextual y seleccione la opción "Service Fabric". Esto abrirá un submenú con varias opciones.

    ![Página 1 de Agregar servicio de Service Fabric][add-service/p1]

2. Seleccione la opción `Add ServiceFabric Service`, esto le guiará por el siguiente conjunto de pasos para agregar un servicio al proyecto.
3. Seleccione la plantilla de servicio que desea agregar al proyecto y haga clic en "Siguiente".

    ![Página 2 de Agregar servicio de Service Fabric][add-service/p2]

4. Escriba el nombre del servicio (y otros detalles necesarios a medida que se requieran) y haga clic en el botón "Agregar servicio" en la parte inferior.  

    ![Página 3 de Agregar servicio de Service Fabric][add-service/p3]

5. Una vez que el servicio se agrega correctamente, la estructura de proyecto completo tiene un aspecto similar a la imagen que se ve a continuación:

    ![Página 4 de Agregar servicio de Service Fabric][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Actualización de la aplicación Java de Service Fabric

Supongamos que ha creado el proyecto ``App1`` usando el complemento de Eclipse para Service Fabric y lo ha implementado mediante el complemento para crear una aplicación con el nombre ``fabric:/App1Application`` del tipo de aplicación ``App1AppicationType`` y la versión de aplicación 1.0. Ahora desea actualizar la aplicación sin retirarla.

Realice el cambio en la aplicación y vuelva a compilar el servicio modificado.  Actualice el archivo de manifiesto del servicio modificado (``ServiceManifest.xml``) con las versiones actualizadas para el servicio (y el código o la configuración o los datos según corresponda). Modifique también el manifiesto de la aplicación (``ApplicationManifest.xml``) con el número de la versión actualizada de la aplicación y el servicio modificado.  

Para actualizar la aplicación usando Eclipse, puede crear una configuración de ejecución duplicada y usarla para actualizar la aplicación de la forma y en el momento que lo necesite, mediante los siguientes pasos:
1. Elija ``Run => Run Configurations``. Haga clic en la flecha pequeña situada a la izquierda de ``Grade Project`` en el panel izquierdo.
2. Haga clic con el botón derecho en ``ServiceFabricDeployer`` y seleccione ``Duplicate``. Asigne un nombre nuevo para esta configuración, por ejemplo ``ServiceFabricUpgrader``.
3. En el panel derecho, en la pestaña ``Arguments``, modifique ``-Pconfig='deploy'`` a ``-Pconfig=upgrade`` y haga clic en ``Apply``.
4. Ahora ha creado y guardado una configuración de ejecución para actualizar la aplicación, que puede ``Run`` cuando desee. Esto se encargará de obtener la versión de tipo de aplicación más actualizada desde el archivo de manifiesto de aplicación.

Ahora puede supervisar mediante Service Fabric Explorer la actualización de la aplicación. En unos minutos se habrá actualizado la aplicación.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

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

