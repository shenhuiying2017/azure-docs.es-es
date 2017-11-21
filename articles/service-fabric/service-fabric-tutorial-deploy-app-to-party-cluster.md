---
title: "Implementación de una aplicación de Azure Service Fabric en un clúster de entidad | Microsoft Docs"
description: "Obtenga información sobre cómo implementar una aplicación en un clúster de entidad."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: d7496b0578301713ebae7381e9a54642e226eb96
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Implementación de una aplicación en un clúster de entidad en Azure
Este tutorial es la segunda parte de una serie y le muestra cómo implementar una aplicación de Azure Service Fabric en un Party Cluster en Azure.

En la segunda parte de la serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Implementar una aplicación en un clúster remoto con Visual Studio
> * Eliminar una aplicación de un clúster mediante de Service Fabric Explorer

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * [Crear una aplicación de .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Implementar la aplicación en un clúster remoto
> * [Configurar CI/CD con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Descarga de la aplicación de ejemplo de votación
Si no compiló la aplicación de ejemplo de votación en la [primera parte de esta serie de tutoriales](service-fabric-tutorial-create-dotnet-app.md), puede descargarla. En una ventana Comandos, ejecute el comando siguiente para clonar el repositorio de la aplicación de ejemplo en la máquina local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Configuración de un clúster de entidad
Los clústeres de entidad son clústeres de Service Fabric gratuitos y de duración limitada, hospedados en Azure y ejecutados por el equipo de Service Fabric, donde cualquier usuario puede implementar aplicaciones y obtener información sobre la plataforma. ¡Gratis!

Para obtener acceso a un clúster de entidad, vaya al sitio http://aka.ms/tryservicefabric y siga las instrucciones para acceder a un clúster. Necesita una cuenta de Facebook o GitHub para acceder a un clúster de entidad.

Puede utilizar su propio clúster en lugar del clúster de la entidad, si lo desea.  El front-end web de ASP.NET Core usa el proxy inverso para comunicarse con el back-end del servicio con estado.  Los clústeres de entidades y el clúster de desarrollo local tienen el proxy inverso habilitado de forma predeterminada.  Si implementa la aplicación de ejemplo de votación en su propio clúster, debe [habilitar el proxy inverso en el clúster](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Los clústeres de entidades no están protegidos, por lo que las aplicaciones y los datos que coloque en ellos los pueden ver otros usuarios. No implemente nada que no desea que vean los demás usuarios. Asegúrese de leer nuestros términos de uso para conocer todos los detalles.

## <a name="deploy-the-app-to-the-azure"></a>Implementación de la aplicación en Azure
Ahora que la aplicación está lista, puede implementarla en un clúster de entidad directamente desde Visual Studio.

1. Haga clic con el botón derecho en el proyecto **Voting** en el Explorador de soluciones y seleccione **Publicar**.

    ![Cuadro de diálogo de publicación](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. Escriba el punto de conexión del clúster de entidad en el campo **Punto de conexión** y haga clic en **Publicar**.

    Cuando la publicación haya finalizado, debería poder enviar una solicitud a la aplicación a través de un explorador.

3. Abra su explorador preferido, escriba la dirección del clúster (punto de conexión sin la información de puerto; por ejemplo, win1kw5649s.westus.cloudapp.azure.com).

    Ahora debería ver el mismo resultado que vio cuando se ejecuta la aplicación localmente.

    ![Respuesta de API desde el clúster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Eliminación de la aplicación de un clúster mediante Service Fabric Explorer
Service Fabric Explorer es una interfaz gráfica de usuario para explorar y administrar aplicaciones en un clúster de Service Fabric.

Para quitar la aplicación del clúster de entidad:

1. Vaya a Service Fabric Explorer mediante el vínculo que proporciona la página de suscripción del clúster de entidad. Por ejemplo, http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. En Service Fabric Explorer, navegue hasta el nodo **fabric://Voting** en la vista de árbol del lado izquierdo.

3. Haga clic en el botón **Acción** en el recuadro **Essentials** de la derecha y elija **Eliminar aplicación**. Confirme la eliminación de la instancia de aplicación, lo que eliminará la instancia de la aplicación que se ejecuta en el clúster.

![Eliminación de una aplicación en Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Eliminación del tipo de aplicación de un clúster mediante Service Fabric Explorer
Las aplicaciones se implementan como tipos de aplicaciones en un clúster de Service Fabric, lo que permite tener varias instancias y versiones de la aplicación ejecutándose dentro del clúster. Una vez quitada la instancia en ejecución de nuestra aplicación, también podemos quitar el tipo para completar la limpieza de la implementación.

Para más información sobre el modelo de aplicación de Service Fabric, consulte [Modelar una aplicación en Service Fabric](service-fabric-application-model.md).

1. Navegue hasta el nodo **VotingType** en la vista de árbol.

2. Haga clic en el botón **Acción** en el recuadro **Essentials** de la derecha y elija **Deshacer aprovisionamiento del tipo**. Confirme la anulación del aprovisionamiento del tipo de aplicación.

![Deshacer el aprovisionamiento del tipo de aplicación en Service Fabric Explorer](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

De este modo se finaliza el tutorial.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Implementar una aplicación en un clúster remoto con Visual Studio
> * Eliminar una aplicación de un clúster mediante de Service Fabric Explorer

Avanzar hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Configurar la integración continua con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)