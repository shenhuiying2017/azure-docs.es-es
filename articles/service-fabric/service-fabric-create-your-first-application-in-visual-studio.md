---
title: "Creación de un servicio de confianza de Azure Service Fabric con C#"
description: "Creación, implementación y depuración de una aplicación de servicio de confianza integrada en Azure Service Fabric, con Visual Studio."
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
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f93298e6483fd8c9dfda835964aeebd1a430af69
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Creación de su primera aplicación de Reliable Services con estado de Service Fabric en C#

Aprenda a implementar su primera aplicación de Service Fabric para .NET en Windows en tan solo unos minutos. Cuando haya terminado, tendrá un clúster local que se ejecuta con una aplicación de servicio de confianza.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de haber [configurado el entorno de desarrollo](service-fabric-get-started.md). Esto incluye la instalación del SDK de Service Fabric SDK y Visual Studio 2017 o 2015.

## <a name="create-the-application"></a>Creación de la aplicación

Inicie Visual Studio como **administrador**.

Creación de un proyecto con `CTRL`+`SHIFT`+`N`

En el cuadro de diálogo **Nuevo proyecto**, elija **Nube > Aplicación de Service Fabric**.

Asigne el nombre **MyApplication** a la aplicación y presione **Aceptar**.

   
![Cuadro de diálogo de proyecto nuevo en Visual Studio.][1]

Puede crear cualquier tipo de aplicación de Service Fabric en el cuadro de diálogo siguiente. Para esta guía de inicio rápido, elija **Servicio con estado**.

Asigne el nombre **MyStatefulService** al servicio y presione **Aceptar**.

![Cuadro de diálogo de servicio nuevo en Visual Studio.][2]


Visual Studio crea el proyecto de aplicación y el proyecto de servicio con estado y los muestra en el Explorador de soluciones.

![Explorador de soluciones después de la creación de una aplicación con el servicio con estado][3]

El proyecto de aplicación (**MyApplication**) no contiene código directamente. En su lugar, hace referencia a un conjunto de proyectos de servicio. Además, contiene otros tres tipos de contenido:

* **Perfiles de publicación**  
Perfiles para la implementación en entornos diferentes.

* **Scripts**  
Script de PowerShell para implementar o actualizar la aplicación.

* **Definición de la aplicación**  
Incluye el archivo ApplicationManifest.xml en *ApplicationPackageRoot* que describe la composición de la aplicación. Los archivos de parámetros de aplicación asociados están en *ApplicationParameters* y se pueden utilizar para especificar parámetros específicos del entorno. Visual Studio seleccionará un archivo de parámetros de aplicación que se especificó en el perfil de publicación asociado durante la implementación en un entorno concreto.
    
Para obtener información general del contenido del proyecto de servicio, consulte [Introducción a Reliable Services de Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implemente la aplicación y depúrela

Ahora que tiene una aplicación, ejecútela.

Presione `F5` en Visual Studio para implementar la aplicación, con el fin de depurarla.

>[!NOTE]
>La primera vez que ejecute e implemente la aplicación localmente, Visual Studio creará un clúster local para la depuración. Esto puede tardar algún tiempo. El estado de creación del clúster se muestra en la ventana de salida de Visual Studio.

Cuando el clúster esté listo, recibirá una notificación de la aplicación de administración de la bandeja de sistema del clúster local que se incluye con el SDK.
   
![Notificación de bandeja de sistema del clúster local][4]

Cuando se inicia la aplicación, Visual Studio muestra automáticamente el **visor de eventos de diagnóstico**, donde se pueden ver los resultados del seguimiento del servicio.
   
![Visor de eventos de diagnóstico][5]

La plantilla del servicio con estado que se utilizó muestra simplemente un valor del contador, que se incrementa en el método `RunAsync` de **MyStatefulService.cs**.

Expanda uno de los eventos para ver más detalles, incluido el nodo en que se ejecuta el código. En este caso, es \_Node\_2, aunque puede que en su equipo sea otro.
   
![Detalle del Visor de eventos de diagnóstico][6]

El clúster local contiene cinco nodos que se hospedan en una sola máquina. En un entorno de producción, cada nodo se hospeda en una máquina virtual o física distinta. Para simular la pérdida de una máquina mientras se ejecuta el depurador de Visual Studio, vamos a desactivar uno de los nodos del clúster local.

En la ventana del **Explorador de soluciones**, abra **MyStatefulService.cs**. 

Buscar el método `RunAsync` y establezca un punto de interrupción en la primera línea del método.

![Punto de interrupción en el método RunAsync de servicio con estado][7]

Para iniciar la herramienta **Service Fabric Explorer**, haga clic con el botón derecho en la aplicación de la bandeja del sistema de la instancia de **Cluster Manager local** y elija **Manage Local Cluster** (Administrar clúster local).

![Inicie el Explorador de Service Fabric desde el Administrador de clústeres locales][systray-launch-sfx]

[**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) ofrece una representación visual de un clúster. Incluye el conjunto de las aplicaciones implementadas en él y el conjunto de nodos físicos que lo componen.

En el panel izquierdo, expanda **Clúster > Nodos** y busque el nodo en que se ejecuta el código.

Haga clic en **Acciones > Desactivar (reiniciar)** para simular un reinicio de la máquina.

![Detener un nodo en el Explorador de Service Fabric][sfx-stop-node]

Momentáneamente, debería ver que se alcanza el punto de interrupción en Visual Studio cuando el cálculo hacía perfectamente en un nodo se conmuta por error a otro.


A continuación, vuelva al Visor de eventos de diagnóstico y observe los mensajes. El contador no ha dejado de incrementarse, aunque los eventos proceden de otro nodo.

![Visor de eventos de diagnóstico después de la conmutación por error][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>Limpieza del clúster local (opcional)

Recuerde que este clúster local es real. La detención del depurador elimina la instancia de la aplicación y anula el registro del tipo de aplicación. No obstante, el clúster se sigue ejecutando en segundo plano. Una vez que está listo para detener el clúster local, hay un par de opciones.

### <a name="keep-application-and-trace-data"></a>Mantener los datos de aplicación y de seguimiento

Cierre el clúster haciendo clic con el botón derecho en la aplicación de la bandeja del sistema de la instancia de **Cluster Manager local** y seleccione **Stop Local Cluster** (Detener clúster local).

### <a name="delete-the-cluster-and-all-data"></a>Eliminar el clúster y todos los datos

Elimine el clúster haciendo clic con el botón derecho en la aplicación de la bandeja del sistema de la instancia de **Cluster Manager local** y seleccione **Remove Local Cluster** (Quitar clúster local). 

Si elige esta opción, Visual Studio volverá a implementar el clúster la próxima vez que ejecute la aplicación. Elija esta opción solo si no tiene intención de utilizar el clúster local durante algún tiempo o si necesita reclamar recursos.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre [Reliable Services](service-fabric-reliable-services-introduction.md).
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

