---
title: "Creación de la primera aplicación de Service Fabric en Visual Studio | Microsoft Docs"
description: "Creación, implementación y depuración de una aplicación de Service Fabric con Visual Studio"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 037dc010a6dc60eb49ad4fdad2861e8653e36199


---
# <a name="create-your-first-azure-service-fabric-application"></a>Creación de la primera aplicación de Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

El SDK de Service Fabric incluye un complemento para Visual Studio que proporciona herramientas y plantillas para crear, implementar y depurar aplicaciones de Service Fabric. Este tema le guiará por el proceso de creación de la primera aplicación en Visual Studio.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, asegúrese de haber [configurado el entorno de desarrollo](service-fabric-get-started.md).

## <a name="video-walkthrough"></a>Tutorial en vídeo
El siguiente vídeo le guía a través de los pasos de este tutorial:

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio/player]
> 
> 

## <a name="create-the-application"></a>Creación de la aplicación
Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. Con el Asistente para nuevo proyecto se puede crear un proyecto de aplicación, junto con el primer proyecto de servicio. Puede agregar más servicios posteriormente.

1. Inicie Visual Studio como administrador.
2. Haga clic en **Archivo > Nuevo proyecto > Nube > Aplicación de Service Fabric**.
3. Asigne un nombre a la aplicación y haga clic en **Aceptar**.
   
    ![Cuadro de diálogo de proyecto nuevo en Visual Studio.][1]
4. En la página siguiente, elija **Con estado** como el primero tipo de servicio que se va a incluir en la aplicación. Asígnele un nombre y haga clic en **Aceptar**.
   
    ![Cuadro de diálogo de servicio nuevo en Visual Studio.][2]
   
   > [!NOTE]
   > Para más información acerca de las opciones, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).
   > 
   > 
   
    Visual Studio crea el proyecto de aplicación y el proyecto de servicio con estado y los muestra en el Explorador de soluciones.
   
    ![Explorador de soluciones después de la creación de una aplicación con el servicio con estado][3]
   
    El proyecto de aplicación no contiene código directamente. En su lugar, hace referencia a un conjunto de proyectos de servicio. Además, contiene otros tres tipos de contenido:
   
   * **Perfiles de publicación**: se usa para administrar las herramientas preferidas para los distintos entornos.
   * **Scripts**: incluye un script de PowerShell para implementar o actualizar aplicaciones. Visual Studio utiliza el script en segundo plano de Visual Studio. También se puede invocar el script directamente en la línea de comandos.
   * **Definición de la aplicación**: incluye el manifiesto de aplicación en *ApplicationPackageRoot*. Los archivos de aplicación asociados se encuentran en *ApplicationParameters*, donde se define la aplicación y se la puede configurar específicamente para un entorno determinado.
     
     Para obtener información general del contenido del proyecto de servicio, consulte [Introducción a Reliable Services de Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implemente la aplicación y depúrela
Ahora que tiene una aplicación, pruebe a ejecutarla.

1. Presione F5 en Visual Studio para implementar la aplicación, con el fin de depurarla.
   
   > [!NOTE]
   > La primera vez que se realiza esta implementación, tarda bastante en completarse, ya que Visual Studio tiene que crear un clúster local para el desarrollo. En los clústeres locales se ejecuta el mismo código de plataforma que se compila en los clústeres de varias máquinas, pero en una sola máquina. El estado de creación del clúster se puede ver en la ventana de salida de Visual Studio.
   > 
   > 
   
    Cuando el clúster esté listo, recibirá una notificación de la aplicación de administración de la bandeja de sistema del clúster local que se incluye con el SDK.
   
    ![Notificación de bandeja de sistema del clúster local][4]
2. Cuando se inicia la aplicación, Visual Studio muestra automáticamente el visor de eventos de diagnóstico, donde se pueden ver los resultados del seguimiento del servicio.
   
    ![Visor de eventos de diagnóstico][5]
   
    En el caso de la plantilla del servicio con estado, los mensajes simplemente muestran el valor del contador, que se incrementa en el método `RunAsync` de MyStatefulService.cs.
3. Expanda uno de los eventos para ver más detalles, incluido el nodo en que se ejecuta el código. En este caso, es _Node_2, aunque puede que en su equipo sea otro.
   
    ![Detalle del Visor de eventos de diagnóstico][6]
   
    El clúster local contiene cinco nodos que se hospedan en una sola máquina. Imita a un clúster de cinco nodos, en el que los nodos están en máquinas distintas. Desactivemos uno de los nodos del clúster local para simular la pérdida de una máquina mientras se ejecuta el depurador de Visual Studio.
   
   > [!NOTE]
   > Los eventos de diagnóstico de la aplicación emitidos por la plantilla del proyecto usan la clase `ServiceEventSource` incluida. Para más información, consulte [Servicios de supervisión y diagnóstico en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
   > 
   > 
4. Busque en el proyecto de servicio la clase que deriva de StatefulService (por ejemplo, MyStatefulService) y establezca un punto de interrupción en la primera línea del método `RunAsync` .
   
    ![Punto de interrupción en el método RunAsync de servicio con estado][7]
5. Haga clic con el botón derecho en la aplicación de la bandeja del sistema del Administrador de clústeres local y elija **Administrar clúster local** para iniciar Service Fabric Explorer.
   
    ![Inicie el Explorador de Service Fabric desde el Administrador de clústeres locales][systray-launch-sfx]
   
    Service Fabric Explorer ofrece una representación visual de un clúster, incluidos el conjunto de las aplicaciones implementadas y el conjunto de nodos físicos que lo componen. Para aprender más sobre Service Fabric, consulte [Visualización del clúster](service-fabric-visualizing-your-cluster.md).
6. En el panel izquierdo, expanda **Clúster > Nodos** y busque el nodo en que se ejecuta el código.
7. Haga clic en **Acciones > Desactivar (reiniciar)** para simular un reinicio de la máquina. (Tenga en cuenta que también se puede desactivar desde el menú contextual en la vista de lista del nodo en el panel izquierdo).
   
    ![Detener un nodo en el Explorador de Service Fabric][sfx-stop-node]
   
    Momentáneamente, debería ver que se alcanza el punto de interrupción en Visual Studio cuando el cálculo hacía perfectamente en un nodo se conmuta por error a otro.
8. Vuelva al Visor de eventos de diagnóstico y observe los mensajes. El contador no ha dejado de incrementarse, aunque los eventos proceden de otro nodo.
   
    ![Visor de eventos de diagnóstico después de la conmutación por error][diagnostic-events-viewer-detail-post-failover]

## <a name="switch-cluster-mode"></a>Cambio del modo de clúster
De forma predeterminada, el clúster de desarrollo local está configurado para ejecutarse como un clúster de 5 nodos lo cual resulta útil para depurar los servicios implementados en varios nodos. No obstante, implementar una aplicación en el clúster de desarrollo de 5 nodos puede tardar un tiempo. Si desea iterar los cambios de código rápidamente sin ejecutar la aplicación en los 5 nodos, puede cambiar el clúster de desarrollo al modo de un nodo. Para ejecutar el código en un clúster con un nodo, haga clic con el botón derecho en el administrador de clústeres local en la bandeja del sistema y seleccione **Switch Cluster Mode -> 1 Node** (Cambiar modo de clústeres -> 1 nodo).  

![Cambio del modo de clúster][switch-cluster-mode]

Al cambiar el modo de clúster, el clúster de desarrollo se restablece y se quitan todas las aplicaciones aprovisionadas o en ejecución en el clúster.

## <a name="cleaning-up"></a>Limpiar
  Antes de concluir, es importante recordar que el clúster local es real. La detención del depurador elimina la instancia de la aplicación y anula el registro del tipo de aplicación. No obstante, el clúster se sigue ejecutando en segundo plano. Tiene varias opciones para administrar el clúster:

1. Para cerrar el clúster pero mantener los datos y los seguimientos de la aplicación, haga clic en **Stop Local Cluster** (Detener clúster local) en la aplicación de bandeja del sistema.
2. Para eliminar totalmente el clúster, haga clic en **Remove Local Cluster** (Quitar clúster local) en la aplicación de la bandeja del sistema. Esta opción generará otra implementación lenta la próxima vez que presione F5 en Visual Studio. Elimine el clúster solo si no tiene intención utilizar el clúster local durante algún tiempo o si necesita reclamar recursos.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a crear un [clúster en Azure](service-fabric-cluster-creation-via-portal.md) o un [clúster independiente en Windows](service-fabric-cluster-creation-for-windows-server.md).
* Intente crear un servicio con los modelos de programación [Reliable Services](service-fabric-reliable-services-quick-start.md) o [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Aprenda cómo puede exponer los servicios en Internet con un [front-end de servicio web](service-fabric-add-a-web-frontend.md).
* Recorra un [laboratorio práctico](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) y cree un servicio sin estado, configure informes de supervisión y estado, y realice una actualización de la aplicación.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png



<!--HONumber=Nov16_HO2-->


