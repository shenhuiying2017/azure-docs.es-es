---
title: "Implementación y actualización local de microservicios de Azure | Microsoft Docs"
description: "Aprenda a configurar un clúster local de Service Fabric, implementar una aplicación existente en él y actualizar dicha aplicación."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi;mikhegn
ms.openlocfilehash: 359677972c7e1fa3f7435052021ddfae5b1ed85e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introducción a la implementación y actualización de aplicaciones en un clúster local
El SDK de Service Fabric de Azure incluye un completo entorno de desarrollo local y puede usar para empezar a trabajar rápidamente con aplicaciones de implementación y administración en un clúster local. En este artículo, se crea un clúster local, se implementa en él una aplicación existente y, a continuación, se actualiza la aplicación a una nueva versión, y todo ello desde Windows PowerShell.

> [!NOTE]
> En este artículo se asume que ya [configuró un entorno de desarrollo](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Creación de un clúster local
Un clúster de Service Fabric representa un conjunto de recursos de hardware en los que se pueden implementar aplicaciones. Normalmente, un clúster se compone de cualquier número entre cinco y varios miles de máquinas. Sin embargo, el SDK de Service Fabric incluye una configuración de clúster que se puede ejecutar en una única máquina.

Es importante comprender que el clúster local de Service Fabric no es un emulador o simulador. Ejecuta el mismo código de plataforma que se encuentra en clústeres de varias máquinas. La única diferencia es que ejecuta en una única máquina los procesos de plataforma que suelen estar dispersos en cinco máquinas.

El SDK proporciona dos maneras de configurar un clúster local: un script de Windows PowerShell y la aplicación de bandeja del sistema del Administrador de clústeres locales. En este tutorial, usaremos el script de PowerShell.

> [!NOTE]
> Si creó un clúster local mediante la implementación de una aplicación desde Visual Studio, puede omitir esta sección.
> 
> 

1. Inicie una ventana nueva de PowerShell como administrador.
2. Ejecute el script de instalación del clúster desde la carpeta del SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    La instalación del clúster tardará unos instantes. Una vez finalizada la instalación, debería ver una salida similar a esta:
   
    ![Salida de instalación de clúster][cluster-setup-success]
   
    Ya está listo para intentar implementar una aplicación en el clúster.

## <a name="deploy-an-application"></a>Implementar una aplicación
El SDK de Service Fabric incluye un amplio conjunto de marcos y herramientas para desarrolladores para crear aplicaciones. Si está interesado en aprender a crear aplicaciones en Visual Studio, consulte [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

En este tutorial, se usará una aplicación de ejemplo existente (llamada WordCount) de forma que puada centrarse en los aspectos de administración de la plataforma: implementación, supervisión y actualización.

1. Inicie una ventana nueva de PowerShell como administrador.
2. Importe el módulo de PowerShell del SDK de Service Fabric.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Cree un directorio para almacenar la aplicación que va a descargar e implementar, por ejemplo, C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Descargue la aplicación WordCount](http://aka.ms/servicefabric-wordcountapp) en la ubicación que creó.  Nota: el explorador Microsoft Edge guarda el archivo con extensión *.zip* .  Debe cambiar la extensión del archivo a *.sfpkg*.
5. Conecte con el clúster local:
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Cree una nueva aplicación mediante el comando de implementación del SDK, con un nombre y una ruta de acceso al paquete de aplicación.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Si todo va bien, debería ver el siguiente resultado:
   
    ![Implementación de una aplicación en el clúster local][deploy-app-to-local-cluster]
7. Para ver la aplicación en acción, inicie el explorador y vaya a [http://localhost:8081/wordcount/índex.html](http://localhost:8081/wordcount/index.html). Debería ver lo siguiente:
   
    ![Interfaz de usuario de aplicación implementada][deployed-app-ui]
   
    La aplicación WordCount es sencilla. Incluye código JavaScript del lado cliente para generar "palabras" aleatorias de cinco caracteres, que, posteriormente, se retransmiten a la aplicación mediante ASP.NET Web API. Un servicio con estado realiza el seguimiento del recuento de palabras. Se crean particiones basadas en el primer carácter de la palabra. El código fuente de la aplicación WordCount se puede encontrar en los [ejemplos de introducción clásicos](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    La aplicación que hemos implementado contiene cuatro particiones. Por tanto las palabras que empiezan de la A a la G se almacenan en la primera partición, las que comienzan de la H a la N en la segunda partición, y así sucesivamente.

## <a name="view-application-details-and-status"></a>Visualización de los detalles y estado de la aplicación
Ahora que hemos implementado la aplicación, echemos un vistazo a algunos de los detalles de la aplicación en PowerShell.

1. Consulte todas las aplicaciones implementadas en el clúster:
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Si solo implementó la aplicación WordCount, verá algo parecido a esto:
   
    ![Consultar todas las aplicaciones implementadas en PowerShell][ps-getsfapp]
2. Vaya al siguiente nivel, para lo que debe consultar el conjunto de servicios incluidos en la aplicación WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Enumeración de servicios de la aplicación en PowerShell][ps-getsfsvc]
   
    La aplicación consta de dos servicios: el front-end web y el servicio con estado que administra las palabras.
3. Por último, eche un vistazo a la lista de particiones de WordCountService:
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Ver las particiones de servicio en PowerShell][ps-getsfpartitions]
   
    El conjunto de comandos que ha usado, al igual que todos los comandos de PowerShell de Service Fabric, están disponibles para cualquier clúster al que se pueda conectar, tanto local como remoto.
   
    Si desea interactuar con el clúster de una forma más visual, puede usar la herramienta Explorador de Service Fabric basada en web, para lo que debe navegar a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) en el explorador.
   
    ![Ver detalles de aplicación en el Explorador de Service Fabric][sfx-service-overview]
   
   > [!NOTE]
   > Para más información acerca de Service Fabric Explorer, consulte [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Actualizar una aplicación
Service Fabric proporciona actualizaciones sin tiempo de inactividad mediante la supervisión del estado de la aplicación cuando se implementa en el clúster. Realice una actualización de la aplicación WordCount.

La nueva versión de la aplicación ahora contará solo las palabras que comiencen por vocal. Cuando se implemente la actualización, veremos dos cambios en el comportamiento de la aplicación. En primer lugar, la velocidad a la que crece el recuento debe reducirse, ya que se cuentan menos palabras. En segundo lugar, dado que la primera partición tiene dos vocales (A y E) y las restantes particiones contienen solo una, su recuento debería finalmente debería empezar a superar a los demás.

1. [Descargue el paquete de WordCount versión 2](http://aka.ms/servicefabric-wordcountappv2) en la misma ubicación en la que descargó el paquete de la versión 1.
2. Vuelva a la ventana de PowerShell y use el comando de actualización del SDK para registrar la nueva versión en el clúster. Después, empiece a actualizar la aplicación fabric:/WordCount.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Debería ver el siguiente resultado en PowerShell cuando comience la actualización.
   
    ![Progreso de la actualización en PowerShell][ps-appupgradeprogress]
3. Mientras que la actualización se lleva a cabo, le resultará más fácil supervisar su estado desde el Explorador de Service Fabric. Inicie una ventana del explorador y navegue a [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda **Aplicaciones** en el árbol de la izquierda, elija **WordCount** y finalmente **fabric:/WordCount**. En la pestaña de información básica, verá el estado de la actualización mientras progresa por los dominios de actualización del clúster.
   
    ![Progreso de la actualización en el Explorador de Service Fabric][sfx-upgradeprogress]
   
    Mientras se realiza la actualización en cada dominio, se realizan comprobaciones de mantenimiento para asegurarse de que la aplicación se comporta correctamente.
4. Si vuelve a ejecutar la consulta anterior en el conjunto de servicios de la aplicación fabric:/WordCount, observará que cambió la versión de WordCountService, pero no la de WordCountWebService:
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Consultar servicios de aplicación después de la actualización][ps-getsfsvc-postupgrade]
   
    Este ejemplo resalta cómo Service Fabric administra las actualizaciones de aplicaciones. Toca solo el conjunto de servicios (o los paquetes de código/configuración de dichos servicios) que cambiaron, lo que hace que el proceso de actualización sea más rápido y confiable.
5. Por último, vuelva al explorador para observar el comportamiento de la nueva versión de la aplicación. Como se esperaba, el recuento avanza más lentamente y la primera partición termina con un poco más del volumen.
   
    ![Ver la nueva versión de la aplicación en el explorador][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpiar
Antes de concluir, es importante recordar que el clúster local es real. Las aplicaciones seguirán ejecutándose en segundo plano hasta que se quiten.  Según la naturaleza de las aplicaciones, una aplicación en ejecución puede consumir importantes recursos en su máquina. Tiene varias opciones para administrar aplicaciones y el clúster:

1. Para quitar una aplicación determinada y todos sus datos, ejecute el siguiente comando:
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    O bien, elimine la aplicación del menú **ACCIONES** de Service Fabric Explorer o del menú contextual en la vista de lista de aplicaciones del lado izquierdo.
   
    ![Eliminación de una aplicación en el explorador de Service Fabric][sfe-delete-application]
2. Después de eliminar la aplicación del clúster, puede anular el registro de las versiones 1.0.0 y 2.0.0 del tipo de aplicación WordCount. La eliminación quita del almacén de imágenes del clúster los paquetes de la aplicación, incluido el código y la configuración.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    También puede seleccionar **Unprovision Type** (Tipo de anulación de aprovisionamiento) para la aplicación en Service Fabric Explorer.
3. Para cerrar el clúster pero mantener los datos y los seguimientos de la aplicación, haga clic en **Stop Local Cluster** (Detener clúster local) en la aplicación de bandeja del sistema.
4. Para eliminar totalmente el clúster, haga clic en **Remove Local Cluster** (Quitar clúster local) en la aplicación de la bandeja del sistema. Esta opción generará otra implementación lenta la próxima vez que presione F5 en Visual Studio. Quite el clúster local solo si no tiene intención utilizarlo durante algún tiempo o si necesita reclamar recursos.

## <a name="one-node-and-five-node-cluster-mode"></a>Modo de clúster de uno y cinco nodos
Al desarrollar aplicaciones, a menudo se encuentra realizando iteraciones rápidas de escritura de código, depuración, cambio de código y depuración. Para ayudar a optimizar este proceso, el clúster local se puede ejecutar de dos modos: con uno o cinco nodos. Ambos modos de clúster tienen sus ventajas. El modo de clúster de cinco nodos le permite trabajar con un clúster real. Puede probar escenarios de conmutación por error, trabajar con más instancias y réplicas de los servicios. El modo de clúster de un nodo está optimizado para la implementación y el registro de servicios de forma rápida, para ayudarle a validar rápidamente el código mediante el entorno de tiempo de ejecución de Service Fabric.

Ni el modo de clúster de un nodo ni el de cinco nodos es un emulador o un simulador. El clúster de desarrollo local ejecuta el mismo código de plataforma que se encuentra en los clústeres de varias máquinas.

> [!WARNING]
> Al cambiar el modo de clúster, el clúster actual se quita del sistema y se crea un nuevo clúster. Cuando se cambia el modo de clúster, se eliminan los datos almacenados en el clúster.
> 
> 

Para cambiar al modo de clúster de un nodo, seleccione **Switch Cluster Mode** (Cambiar modo de clúster) en la instancia local de Cluster Manager de Service Fabric.

![Cambio del modo de clúster][switch-cluster-mode]

O bien, cambie el modo de clúster mediante PowerShell:

1. Inicie una ventana nueva de PowerShell como administrador.
2. Ejecute el script de instalación del clúster desde la carpeta del SDK:
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    La instalación del clúster tardará unos instantes. Una vez finalizada la instalación, debería ver una salida similar a esta:
   
    ![Salida de instalación de clúster][cluster-setup-success-1-node]

## <a name="next-steps"></a>Pasos siguientes
* Tras la implementación y actualización de algunas aplicaciones pregeneradas, puede [intentar compilar la suya propia en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Todas las acciones realizadas en el clúster local en este artículo se pueden realizar también en un [clúster de Azure](service-fabric-cluster-creation-via-portal.md) .
* La actualización realizada en este artículo era básica. Consulte la [documentación de actualización](service-fabric-application-upgrade.md) para más información acerca de la eficacia y flexibilidad de las actualizaciones de Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
