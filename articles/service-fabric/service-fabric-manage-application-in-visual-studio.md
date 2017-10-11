---
title: "Administración de las aplicaciones en Visual Studio | Microsoft Docs"
description: Use Visual Studio para crear, desarrollar, empaquetar, implementar y depurar las aplicaciones y servicios de Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: 3f6a47a15b74a7ceb6504b2834be62e76ab70bcc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Uso de Visual Studio para simplificar la escritura y la administración de las aplicaciones de Service Fabric
Puede administrar los servicios y aplicaciones de Service Fabric de Azure a través de Visual Studio. Cuando haya [configurado su entorno de desarrollo](service-fabric-get-started.md), puede usar Visual Studio para crear aplicaciones de Service Fabric, agregar servicios, o empaquetar, registrar e implementar aplicaciones en el clúster de desarrollo local.

## <a name="deploy-your-service-fabric-application"></a>Implementar la aplicación de Service Fabric
De forma predeterminada, la implementación de una aplicación combina los pasos siguientes en una operación sencilla:

1. Creación del paquete de aplicación
2. Carga del paquete de aplicación en el almacén de imágenes
3. Registro del tipo de aplicación
4. Eliminación de cualquier instancia de aplicación en ejecución
5. Creación de una instancia de aplicación

En Visual Studio, al presionar **F5** se implementará su aplicación y se asociará el depurador a todas las instancias de aplicación. Puede usar **CTRL+F5** para implementar una aplicación sin depurar o bien, publicar en un clúster local o remoto mediante el perfil de publicación. Para obtener más información, consulte [Publicación de una aplicación en un clúster remoto con Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Application Debug Mode
Visual Studio proporciona una propiedad llamada **Application Debug Mode**, que controla cómo desea que Visual Studio controle la implementación de la aplicación como parte de la depuración.

#### <a name="to-set-the-application-debug-mode-property"></a>Establecimiento de la propiedad Application Debug Mode
1. En el menú de acceso directo del proyecto de la aplicación de Service Fabric (*.sfproj), elija **Propiedades** (o presione la tecla **F4**).
2. En la ventana **Propiedades**, establezca la propiedad **Application Debug Mode** (Modo de depuración de aplicación).

![Establecer la propiedad Application Debug Mode][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuración de la aplicación

1. **Actualizar aplicación** Este modo le permite cambiar y depurar su código rápidamente y permite editar archivos web estáticos durante la depuración. Este modo solo funciona si el clúster de desarrollo local está en [modo 1 nodo](/service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Quitar aplicación** : la aplicación se quita cuando finaliza la sesión de depuración.
3. **Actualización automática** La aplicación continúa ejecutándose cuando finaliza la sesión de depuración. La siguiente sesión de depuración tratará la implementación como una actualización. El proceso de actualización conserva todos los datos especificados en una sesión de depuración anterior.
4. **Mantener aplicación** La aplicación sigue ejecutándose en el clúster cuando finaliza la sesión de depuración. Al principio de la siguiente sesión de depuración, se eliminará la aplicación.

Se conservan los datos de **Actualización automática** por medio de las funcionalidades de actualización de aplicaciones de Service Fabric. Para obtener más información sobre la actualización de aplicaciones y cómo se realiza una actualización en un entorno real, consulte [Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Agregue un servicio a su aplicación Service Fabric
Puede agregar nuevos servicios a su aplicación para ampliar su funcionalidad.  Para garantizar que el servicio se incluye en el paquete de aplicación, agregue el servicio a través del elemento de menú **Nuevo servicio del tejido...** .

![Agregar un nuevo servicio de Service Fabric][newservice]

Seleccione un tipo de proyecto de Service Fabric para agregarlo a la aplicación y especifique un nombre para el servicio.  Vea [Elección de un marco para el servicio](service-fabric-choose-framework.md) para ayudarle a decidir qué tipo de servicio quiere usar.

![Seleccione un tipo de proyecto de servicio de Service Fabric para agregarlo a la aplicación][addserviceproject]

El nuevo servicio se agrega a la solución y al paquete de aplicación existente. Se agregarán al manifiesto de la aplicación las referencias del servicio y una instancia del servicio predeterminada, haciendo que el servicio se cree y se inicie la próxima vez que implemente la aplicación.

![Se agrega el nuevo servicio al manifiesto de la aplicación][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empaquetar la aplicación de Service Fabric
Para implementar la aplicación y sus servicios en un clúster, debe crear una paquete de aplicación.  El paquete organiza el manifiesto de la aplicación, los manifiestos de servicio y otros archivos necesarios en un diseño específico.  Visual Studio configura y administra el paquete en la carpeta del proyecto de aplicación, en el directorio 'pkg'.  Haga clic en **Paquete** en el menú contextual **Aplicación** para crear o actualizar el paquete de aplicación.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Eliminación de aplicaciones y tipos de aplicación mediante Cloud Explorer
Puede realizar operaciones básicas de administración de clúster desde Visual Studio mediante Cloud Explorer, que se puede iniciar desde el menú **Vista** . Por ejemplo, puede eliminar aplicaciones y deshacer el aprovisionamiento de tipos de aplicación en clústeres locales o remotos.

![Eliminación de una aplicación][removeapplication]

> [!TIP]
> Para obtener una mejor funcionalidad de administración de clúster, consulte [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Modelo de aplicación de Service Fabric](service-fabric-application-model.md)
* [Implementación de la aplicación de Service Fabric](service-fabric-deploy-remove-applications.md)
* [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depuración de la aplicación de Service Fabric](service-fabric-debugging-your-application.md)
* [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png