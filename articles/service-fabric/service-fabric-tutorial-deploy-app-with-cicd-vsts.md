---
title: "Implementar una aplicación de Azure Service Fabric con integración continua (Team Services) | Microsoft Docs"
description: "Aprenda a configurar la integración continua y la implementación para una aplicación de Service Fabric mediante Visual Studio Team Services.  Implemente una aplicación en un clúster de Service Fabric en Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 2fb7ab906208a58c0b5cd3af8b53188fbab94029
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Implementar una aplicación con CI/CD en un clúster de Service Fabric
Este tutorial es la tercera parte de una serie y describe cómo configurar la integración continua y la implementación de una aplicación de Azure Service Fabric mediante Visual Studio Team Services.  Se necesita una aplicación existente de Service Fabric; se usa como ejemplo la aplicación creada en [Compilación de una aplicación .NET](service-fabric-tutorial-create-dotnet-app.md).

En la tercera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Agregar un control de código fuente al proyecto
> * Crear una definición de compilación en Team Services
> * Crear una definición de versión en Team Services
> * Implementar y actualizar una aplicación automáticamente

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * [Crear una aplicación de .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar la aplicación en un clúster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Configurar CI/CD con Visual Studio Team Services
> * [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).
- Cree un clúster de Windows Service Fabric en Azure (por ejemplo, [siguiendo este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)).
- Cree una [cuenta de Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Descarga de la aplicación de ejemplo de votación
Si no compiló la aplicación de ejemplo de votación en la [primera parte de esta serie de tutoriales](service-fabric-tutorial-create-dotnet-app.md), puede descargarla. En una ventana Comandos, ejecute el comando siguiente para clonar el repositorio de la aplicación de ejemplo en la máquina local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparar un perfil de publicación
Ahora que ha [creado una aplicación](service-fabric-tutorial-create-dotnet-app.md) y ha [implementado la aplicación en Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), ya puede configurar la integración continua.  En primer lugar, prepare un perfil de publicación en la aplicación que se use durante el proceso de implementación que se ejecuta en Team Services.  El perfil de publicación debe configurarse para que tenga como destino el clúster que ha creado antes.  Inicie Visual Studio y abra un proyecto de aplicación existente de Service Fabric.  En el **Explorador de soluciones**, haga clic con el botón derecho en la aplicación y seleccione **Publicar...**.

Elija un perfil de destino en el proyecto de la aplicación que vaya a usar para el flujo de trabajo de integración continua (por ejemplo, Cloud).  Especifique el punto de conexión de la conexión del clúster.  Marque la casilla **Actualizar la aplicación** para que la aplicación se actualice para cada implementación de Team Services.  Haga clic en el hipervínculo **Guardar** para guardar la configuración en el perfil de publicación y, luego, haga clic en **Cancelar** para cerrar el cuadro de diálogo.  

![Perfil de inserción][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Compartir la solución de Visual Studio en un nuevo repositorio Git de Team Services
Comparta sus archivos de origen de la aplicación en un proyecto de equipo de Team Services para que pueda generar compilaciones.  

Cree un repositorio Git local para el proyecto seleccionando **Agregar al control de código fuente** -> **Git** en la barra de estado situada en la esquina inferior derecha de Visual Studio. 

En la vista **Inserción** de **Team Explorer**, seleccione el botón **Publicar repositorio Git** en **Insertar en Visual Studio Team Services**.

![Repositorio Git de inserción][push-git-repo]

Compruebe su correo electrónico y seleccione su cuenta en la lista desplegable **Dominio de Team Services**. Escriba el nombre del repositorio y seleccione **Publicar repositorio**.

![Repositorio Git de inserción][publish-code]

La publicación del repositorio crea un proyecto de equipo en su cuenta con el mismo nombre que el repositorio local. Para crear el repositorio en un proyecto de equipo existente, haga clic en **Avanzado**, junto al nombre del **repositorio**, y seleccione un proyecto de equipo. Puede ver el código en la web seleccionando **Ver en la web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurar la entrega continua con VSTS
Una definición de compilación de Team Services describe un flujo de trabajo compuesto por una serie de pasos de compilación que se ejecutan de manera secuencial. Cree una definición de compilación que genere un paquete de aplicación de Service Fabric y otros artefactos para implementarlos en un clúster de Service Fabric. Obtenga más información sobre las [definiciones de compilación de Team Services](https://www.visualstudio.com/docs/build/define/create). 

Las definiciones de versión de Team Services describen un flujo de trabajo que implementa un paquete de aplicación en un clúster. Cuando se usan juntas, la definición de compilación y la de versión ejecutan el flujo de trabajo completo empezando por los archivos de origen y terminando por la ejecución de una aplicación en el clúster. Obtenga más información sobre las [definiciones de versión](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)de Team Services.

### <a name="create-a-build-definition"></a>Creación de una definición de compilación
Abra un explorador web y vaya al nuevo proyecto de equipo en: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Seleccione la pestaña **Compilación y versión**, a continuación, **Compilaciones** y, finalmente, **+ Nueva definición**.  En **Seleccionar una plantilla**, seleccione la plantilla de la **aplicación de Azure Service Fabric** y haga clic en **Aplicar**. 

![Elegir la plantilla de compilación][select-build-template] 

En **Tareas**, escriba "VS2017 hospedado" como **Cola del agente**. 

![Seleccionar tareas][save-and-queue]

En **Desencadenadores**, habilite la integración continua estableciendo **Estado del desencadenador**.  Seleccione **Guardar y poner en cola** para iniciar manualmente una compilación.  

![Seleccionar desencadenadores][save-and-queue2]

Las compilaciones también se desencadenan después de una inserción o una protección. Para comprobar el progreso de la compilación, vaya a la pestaña **Compilaciones**.  Cuando haya comprobado que la compilación se ejecuta correctamente, defina una definición de versión que implemente la aplicación en un clúster. 

### <a name="create-a-release-definition"></a>Creación de una definición de versión  

Seleccione la pestaña **Compilación y versión**, a continuación, **Versiones** y, finalmente, **+ Nueva definición**.  En **Seleccionar una plantilla**, seleccione la plantilla **Implementación de Azure Service Fabric** de la lista y haga clic en **Aplicar**.  

![Elegir la versión de la plantilla][select-release-template]

Seleccione **Tareas**->**Entorno 1** y, a continuación, **+Nuevo** para agregar una nueva conexión de clúster.

![Agregar conexión del clúster][add-cluster-connection]

En la vista **Agregar nueva conexión de Service Fabric**, seleccione la autenticación **Basada en certificado** o **Azure Active Directory**.  Especifique como nombre de conexión "mysftestcluster" y como punto de conexión del clúster "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" (o el punto de conexión del clúster en el que se va a implementar). 

Para la autenticación basada en certificados, agregue la **Huella digital del certificado de servidor** del certificado de servidor utilizado para crear el clúster.  En **Certificado de cliente**, agregue la codificación en base 64 del archivo del certificado de cliente. Consulte la ventana de ayuda emergente de ese campo para obtener información sobre cómo obtener esa representación codificada en base 64 del certificado. Agregue también un valor de **Contraseña** del certificado.  Puede utilizar el certificado del clúster o del servidor si no tiene un certificado de cliente independiente. 

Para las credenciales de Azure Active Directory, agregue la **Huella digital del certificado de servidor** del certificado de servidor utilizado para crear el clúster y las credenciales que desea usar para conectarse al clúster en los campos **Nombre de usuario** y **Contraseña**. 

Haga clic en **Agregar** para guardar la conexión del clúster.

A continuación, agregue un artefacto de compilación a la canalización para que la definición de la versión pueda encontrar la salida de la compilación. Seleccione **Canalización** y **Artefactos**->**+Agregar**.  En **Origen (Definición de compilación)**, seleccione la definición de compilación que creó anteriormente.  Haga clic en **Agregar** para guardar el artefacto de compilación.

![Agregar artefacto][add-artifact]

Habilite un desencadenador de implementación continua para que se cree automáticamente una versión cuando finalice la compilación. Haga clic en el icono de rayo en el artefacto, habilite el desencadenador y haga clic en **Guardar** para guardar la definición de la versión.

![Habilitar el desencadenador][enable-trigger]

Seleccione **+Versión** -> **Crear versión** -> **Crear** para crear manualmente una versión.  Compruebe que la implementación se haya efectuado correctamente y que la aplicación se está ejecutando en el clúster.  Abra un explorador web y vaya a [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe que la versión de la aplicación de este ejemplo es "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e insertar cambios y desencadenar una versión
Para comprobar que la canalización de la integración continua funciona, inserte algunos cambios de código en Team Services.    

A medida que escribe el código, los cambios se controlan automáticamente con Visual Studio. Confirme los cambios en el repositorio Git local seleccionando el icono de cambios pendientes (![Pending][pending]) en la barra de estado situada en la parte inferior derecha.

En la vista **Cambios** de Team Explorer, agregue un mensaje que describa la actualización y confirme los cambios.

![Confirmar todo][changes]

Seleccione el icono de la barra de estado de los cambios no publicados (![Cambios no publicados][unpublished-changes]) o la vista Sincronización de Team Explorer. Seleccione **Insertar** para actualizar el código en Team Services o TFS.

![Inserción de los cambios][push]

La inserción de los cambios en Team Services desencadena automáticamente una compilación.  Cuando la definición de compilación se haya completado correctamente, se creará automáticamente una versión y se empezará a actualizar la aplicación en el clúster.

Para comprobar el progreso de la compilación, vaya a la pestaña **Compilaciones** de **Team Explorer** en Visual Studio.  Cuando haya comprobado que la compilación se ejecuta correctamente, defina una definición de versión que implemente la aplicación en un clúster.

Compruebe que la implementación se haya efectuado correctamente y que la aplicación se está ejecutando en el clúster.  Abra un explorador web y vaya a [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observe que la versión de la aplicación de este ejemplo es "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Actualizar la aplicación
Efectúe cambios de código en la aplicación.  Guarde y confirme los cambios siguiendo los pasos anteriores.

Cuando haya comenzado la actualización de la aplicación, podrá ver el progreso de la actualización en Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

La actualización de la aplicación puede tardar varios minutos. Una vez concluida la actualización, la aplicación ejecutará la próxima versión  En este ejemplo "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Agregar un control de código fuente al proyecto
> * Creación de una definición de compilación
> * Creación de una definición de versión
> * Implementar y actualizar una aplicación automáticamente

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
