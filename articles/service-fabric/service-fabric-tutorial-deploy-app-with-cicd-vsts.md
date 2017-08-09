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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c528974951d3b4a83111cb92b931810a91f660f4
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Implementar una aplicación con CI/CD en un clúster de Service Fabric
En este tutorial se describe cómo configurar la integración continua y la implementación de una aplicación de Azure Service Fabric mediante Visual Studio Team Services.  Se necesita una aplicación existente de Service Fabric; se usa como ejemplo la aplicación creada en [Compilación de una aplicación .NET](service-fabric-tutorial-create-dotnet-app.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Agregar un control de código fuente al proyecto
> * Crear una definición de compilación en Team Services
> * Crear una definición de versión en Team Services
> * Implementar y actualizar una aplicación automáticamente

El tutorial se divide en tres artículos; este artículo es el tercero de la serie.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).
- Cree una aplicación de Service Fabric (por ejemplo, [siguiendo este tutorial](service-fabric-tutorial-create-dotnet-app.md)). 
- Cree un clúster de Windows Service Fabric en Azure (por ejemplo, [siguiendo este tutorial](service-fabric-tutorial-create-cluster-azure-ps.md)).
- Cree una [cuenta de Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

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
Use el asistente integrado de Visual Studio para configurar la entrega continua con VSTS.

1. Haga clic con el botón derecho en el proyecto de aplicación **MyApplication** en el **Explorador de soluciones** y, luego, seleccione **Agregar** -> **Entrega continua con VSTS**. Aparecerá un cuadro de diálogo para que escriba la configuración necesaria.

    ![Entrega continua con VSTS][continuous-delivery-with-VSTS]

    Los valores de **Cuenta**, **Proyecto** y **Repositorio Git** se indican automáticamente.

2. Elija **VS2017 hospedado** como cola del agente.

3. Elija **Crear conexión** en la lista desplegable **Conexión de clúster**, que abre un sitio web para configurar un punto de conexión de servicio en VSTS. 

4. Establezca el foco en la ventana del explorador y elija **Nuevo punto de conexión de servicio** -> **Service Fabric**.

    ![Nuevo punto de conexión de servicio][new-service-endpoint]

    Aparecerá un cuadro de diálogo para agregar una nueva conexión de Service Fabric.
    
5. Seleccione **Basada en certificados** y complete el cuadro de diálogo:

    ![Cuadro de diálogo de nuevo punto de conexión de servicio][new-service-endpoint-dialog]

    1. Escriba un **nombre de conexión**.
    2. Escriba el punto de conexión de administración del clúster en el campo **Punto de conexión del clúster** (por ejemplo, "tcp://mycluster.eastus.cloudapp.azure.com:19000"). Especifique "tcp://" como protocolo.  El puerto de punto de conexión de administración predeterminado es el 19000.
    3. Proporcione la **huella digital del certificado de servidor**.  La huella digital se puede obtener abriendo el Service Fabric Explorer del clúster (https://mycluster.eastus.cloudapp.azure.com:19080).
        - Elija el nodo **Clúster** en la vista de árbol y la pestaña **Manifiesto** situada en el panel derecho.
        - Busque el elemento **<ServerCertificate>** en el archivo XML y copie el contenido del atributo **X509FindValue**.
    4. En el campo **Certificado de cliente** indique el certificado de cliente como una cadena codificada en Base64, necesario para que el certificado se instale en el agente de compilación:
        - Asegúrese de que tiene disponible el certificado como archivo PFX.
        - Ejecute el siguiente comando de PowerShell para generar el archivo PFX como cadena codificada en Base64 en el Portapapeles: `[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - Pegue (Control + V) el valor del Portapapeles al campo del cuadro de diálogo.
    5. Escriba la **contraseña del certificado** en el campo **Contraseña** y haga clic en **Aceptar**.

6. En Visual Studio, elija **<Refresh>** en el campo **Conexión de clúster** del cuadro de diálogo **Entrega continua con VSTS**. El punto de conexión del clúster que acaba de crear debería aparecer en la lista desplegable.

7. Elija el nombre de definición predeterminado de compilación o de versión o cambie los nombres propuestos en el cuadro de diálogo. Cuando haya terminado, haga clic en **Aceptar**.

Después de unos instantes, aparece un cuadro de diálogo en Visual Studio, en el que se le pregunta si quiere abrir la definición de compilación y de versión en el explorador. Puede hacerlo para inspeccionar su configuración, aunque este paso no es obligatorio en este tutorial.

- Una definición de compilación de Team Services describe un flujo de trabajo compuesto por una serie de pasos de compilación que se ejecutan de manera secuencial. Cree una definición de compilación que genere un paquete de aplicación de Service Fabric y otros artefactos para implementarlos en un clúster de Service Fabric. Obtenga más información sobre las [definiciones de compilación de Team Services](https://www.visualstudio.com/docs/build/define/create).
- Las definiciones de versión de Team Services describen un flujo de trabajo que implementa un paquete de aplicación en un clúster. Cuando se usan juntas, la definición de compilación y la de versión ejecutan el flujo de trabajo completo empezando por los archivos de origen y terminando por la ejecución de una aplicación en el clúster. Obtenga más información sobre las [definiciones de versión](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)de Team Services.

## <a name="commit-and-push-changes-trigger-a-release"></a>Confirmar e insertar cambios y desencadenar una versión
Para comprobar que la canalización de la integración continua funciona, inserte algunos cambios de código en Team Services.    

A medida que escribe el código, los cambios se controlan automáticamente con Visual Studio. Confirme los cambios en el repositorio Git local seleccionando el icono de cambios pendientes (![Pending][pending]) en la barra de estado situada en la parte inferior derecha.

En la vista **Cambios** de Team Explorer, agregue un mensaje que describa la actualización y confirme los cambios.

![Confirmar todo][changes]

Seleccione el icono de la barra de estado de los cambios no publicados (![Cambios no publicados][unpublished-changes]) o la vista Sincronización de Team Explorer. Seleccione **Insertar** para actualizar el código en Team Services o TFS.

![Inserción de los cambios][push]

La inserción de los cambios en Team Services desencadena automáticamente una compilación.  Cuando la definición de compilación se haya completado correctamente, se creará automáticamente una versión y se empezará a actualizar la aplicación en el clúster.

Para comprobar el progreso de la compilación, vaya a la pestaña **Compilaciones** de **Team Explorer** en Visual Studio.  Cuando haya comprobado que la compilación se ejecuta correctamente, defina una definición de versión que implemente la aplicación en un clúster.

Compruebe que la implementación se haya efectuado correctamente y que la aplicación se está ejecutando en el clúster.  Abra un explorador web y vaya a [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Observe que la versión de la aplicación de este ejemplo es "1.0.0.20170616.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Actualizar la aplicación
Efectúe cambios de código en la aplicación.  Guarde y confirme los cambios siguiendo los pasos anteriores.

Cuando haya comenzado la actualización de la aplicación, podrá ver el progreso de la actualización en Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

La actualización de la aplicación puede tardar varios minutos. Una vez concluida la actualización, la aplicación ejecutará la próxima versión  (en este ejemplo es "1.0.0.20170616.4").

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Agregar un control de código fuente al proyecto
> * Creación de una definición de compilación
> * Creación de una definición de versión
> * Implementar y actualizar una aplicación automáticamente

Ahora que ha implementado una aplicación y ha configurado la integración continua, pruebe lo siguiente:
- [Actualizar una aplicación](service-fabric-application-upgrade.md)
- [Probar una aplicación](service-fabric-testability-overview.md) 
- [Supervisar y diagnosticar](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
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
