---
title: "Creación de un servicio de confianza de Azure Service Fabric con C#"
description: "Cree, implemente y depuración de una aplicación de Reliable Services integrada en Azure Service Fabric, con Visual Studio."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 43f77a1a2e1bbe28bb646aa23c28c253c20e8dda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Creación de su primera aplicación de Reliable Services con estado de Service Fabric en C#

Aprenda a implementar su primera aplicación de Azure Service Fabric para .NET en Windows en unos minutos. Cuando haya terminado, tendrá un clúster local que se ejecuta con una aplicación de Reliable Services.

## <a name="prerequisites"></a>requisitos previos

Antes de comenzar, asegúrese de haber [configurado el entorno de desarrollo](service-fabric-get-started.md). Este proceso incluye la instalación del SDK de Service Fabric y Visual Studio 2017 o 2015.

## <a name="create-the-application"></a>Creación de la aplicación

1. Inicie Visual Studio como administrador.

2. Cree un proyecto, para lo que debe presionar Ctrl + Mayús + N.

3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Nube** > **Aplicación de Service Fabric**.

4. Asigne el nombre **MyApplication** a la aplicación. Después seleccione **Aceptar**.

   ![Cuadro de diálogo Nuevo proyecto de Visual Studio][1]

5. En el cuadro de diálogo siguiente puede crear cualquier tipo de aplicación de Service Fabric. Para esta guía de inicio rápido, elija **Servicio con estado**.

6. Asigne el nombre **MyStatefulService** al servicio. Después seleccione **Aceptar**.

    ![Cuadro de diálogo Nuevo servicio en Visual Studio][2]

    Visual Studio crea el proyecto de aplicación y el proyecto de servicio con estado. Luego, muestra ambos proyectos en el Explorador de soluciones.

    ![Explorador de soluciones después de la creación de una aplicación con el servicio con estado][3]

    El proyecto de aplicación (**MyApplication**) no tiene código. En su lugar, hace referencia a un conjunto de proyectos de servicio. También tiene tipos de contenido diferentes:

    * **Perfiles de publicación**  
    Perfiles para la implementación en entornos diferentes.

    * **Scripts**  
    Scripts de PowerShell para implementar o actualizar aplicaciones.

    * **Definición de la aplicación**  
Incluye el archivo ApplicationManifest.xml en *ApplicationPackageRoot*, que describe la composición de la aplicación. Los archivos de parámetros de aplicación asociados están en *ApplicationParameters* y se pueden utilizar para especificar parámetros específicos del entorno. Visual Studio selecciona un archivo de parámetros de aplicación que se especificó en el perfil de publicación asociado.
    
Para obtener información general del contenido del proyecto de servicio, consulte [Introducción a Reliable Services de Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implemente la aplicación y depúrela

Ahora que tiene una aplicación, ejecútela, impleméntela y depúrela, para lo que debe seguir estos pasos.

1. Presione F5 en Visual Studio para implementar la aplicación, con el fin de depurarla.

    >[!NOTE]
    >La primera vez que ejecute e implemente la aplicación localmente, Visual Studio creará un clúster local para la depuración. Esta operación puede tardar un tiempo. El estado de creación del clúster se muestra en la ventana de salida de Visual Studio.

    Cuando el clúster esté listo, recibirá una notificación de la aplicación de administración de la bandeja de sistema del clúster local que se incluye con el SDK.
    
    ![Notificación de bandeja de sistema del clúster local][4]

    Tras iniciarse la aplicación, Visual Studio muestra automáticamente el Visor de eventos de diagnóstico, donde se pueden ver los resultados del seguimiento de los servicios.
    
    ![Visor de eventos de diagnóstico][5]

    >[!NOTE]
    >Los eventos deben iniciar automáticamente el seguimiento en el Visor de eventos de diagnóstico. Si necesita configurar manualmente el Visor de eventos de diagnóstico, abra primero el archivo `ServiceEventSource.cs`, que se encuentra en el proyecto **MyStatefulService**. Copie el valor del atributo `EventSource` en la parte superior de la clase `ServiceEventSource`. En el ejemplo siguiente, el origen del evento se llama `"MyCompany-MyApplication-MyStatefulService"`, pero en su caso el nombre puede ser otro.
>
    >![Búsqueda del nombre de origen del evento de servicio][service-event-source-name]


2. A continuación, abra el cuadro de diálogo **Proveedores de ETW**. Luego, seleccione el icono de la rueda dentada que se encuentra en la pestaña **Eventos de diagnóstico**. Pegue el nombre del origen del evento que ha copiado en el cuadro de entrada **Proveedores de ETW**. Después, haga clic en el botón **Aplicar**. De este modo se inician automáticamente los eventos de seguimiento.

    ![Establecimiento del nombre del origen de Evento de diagnóstico][setting-event-source-name]

    Ahora debería ver que los eventos aparecen en la ventana Eventos de diagnóstico.

    La plantilla del servicio con estado muestra un valor del contador que se incrementa en el método `RunAsync` de **MyStatefulService.cs**.

3. Expanda uno de los eventos para ver más detalles, incluido el nodo en que se ejecuta el código. En este caso, es **\_Node\_0,** aunque puede diferir en su máquina.
   
    ![Detalle del Visor de eventos de diagnóstico][6]

4. El clúster local contiene cinco nodos que se hospedan en una sola máquina. En un entorno de producción, cada nodo se hospeda en una máquina virtual o física distinta. Para simular la pérdida de una máquina mientras se ejecuta el depurador de Visual Studio, desactive uno de los nodos del clúster local.

5. En la ventana del **Explorador de soluciones**, abra **MyStatefulService.cs**. 

6. Busque el método `RunAsync` y establezca un punto de interrupción en la primera línea del método.

    ![Punto de interrupción en el método RunAsync de servicio con estado][7]

7. Para iniciar la herramienta Service Fabric Explorer, haga clic con el botón derecho en la aplicación de la bandeja del sistema del **Administrador de clústeres locales** y seleccione **Manage Local Cluster** (Administrar clúster local).

    ![Iniciar Service Fabric Explorer desde el Administrador de clústeres locales][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ofrece una representación visual de un clúster. Incluye el conjunto de aplicaciones que se implementa en él y el conjunto de nodos físicos que lo componen.

8. En el panel izquierdo, expanda **Clúster** > **Nodos** y busque el nodo en que se ejecuta el código. Luego, para simular el reinicio de una máquina, seleccione **Acciones** > **Desactivar (reiniciar)**.

    ![Detener un nodo en el Explorador de Service Fabric][sfx-stop-node]

    Momentáneamente, debería ver que se alcanza el punto de interrupción en Visual Studio cuando el cálculo hacía perfectamente en un nodo se conmuta por error a otro.

9. A continuación, vuelva al Visor de eventos de diagnóstico y observe los mensajes. El contador no ha dejado de incrementarse, aunque los eventos proceden de otro nodo.

    ![Visor de eventos de diagnóstico después de la conmutación por error][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Limpiar el clúster local (opcional)

Recuerde que este clúster local es real. La detención del depurador elimina la instancia de la aplicación y anula el registro del tipo de aplicación. No obstante, el clúster se sigue ejecutando en segundo plano. Una vez que está listo para detener el clúster local, hay un par de opciones.

### <a name="keep-application-and-trace-data"></a>Mantener los datos de aplicación y de seguimiento

Cierre el clúster haciendo clic con el botón derecho en la aplicación de la bandeja del sistema del **Administrador de clústeres locales** y seleccionando **Stop Local Cluster** (Detener clúster local).

### <a name="delete-the-cluster-and-all-data"></a>Eliminar el clúster y todos los datos

Elimine el clúster haciendo clic con el botón derecho en la aplicación de la bandeja del sistema del **Administrador de clústeres locales**. Luego, elija **Remove Local Cluster** (Quitar clúster local). 

Si elige esta opción, Visual Studio volverá a implementar el clúster la próxima vez que ejecute la aplicación. Elija esta opción si no tiene intención de utilizar el clúster local durante un tiempo o si necesita reclamar recursos.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de [Reliable Services](service-fabric-reliable-services-introduction.md).
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
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
