---
title: "Actualización de clústeres de Service Fabric independientes en Windows Server | Microsoft Docs"
description: "Actualice el código de Service Fabric o la configuración que ejecuta un clúster de Service Fabric independiente, incluida la configuración del modo de actualización del clúster"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 3d2e6380e4987c559e9ed62c3eaacb1ce45c917d
ms.openlocfilehash: 79d9d65c93aae92170667c1bc98fbd6bb1ffc3ff


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Actualización del clúster de Service Fabric independiente en Windows Server
> [!div class="op_single_selector"]
> * [Clúster de Azure](service-fabric-cluster-upgrade.md)
> * [Clúster independiente](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Para cualquier sistema moderno, diseñar la capacidad de actualización es clave para lograr el éxito a largo plazo de su producto. Un clúster de Service Fabric es un recurso que usted posee. Este artículo describe cómo puede asegurarse de que el clúster siempre ejecuta versiones compatibles y las configuraciones del código de Service Fabric.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Control de la versión de Service Fabric que se ejecuta en el clúster
Puede configurar el clúster para descargar las actualizaciones de Service Fabric cuando Microsoft publique una versión nueva, o seleccionar la versión compatible de Service Fabric que desee que tenga el clúster. 

Para ello, establezca la configuración del clúster "fabricClusterAutoupgradeEnabled" en true o false.

> [!NOTE]
> Asegúrese de que tiene siempre una versión compatible de Service Fabric en ejecución en el clúster. Cuando anunciamos el lanzamiento de una nueva versión de Service Fabric, se marca la versión anterior para que finalice el soporte después de un mínimo de 60 días a partir de esa fecha. Las nuevas versiones se anuncian [en el blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nueva versión estará disponible para su elección a partir de ese momento. 
> 
> 

Puede actualizar el clúster a la nueva versión solo si usa una configuración de nodo de estilo de producción, donde cada nodo de Service Fabric se asigna a una máquina virtual o física independiente. Si tiene un clúster de desarrollo, donde hay de más de un nodo de tejido de servicio en una única máquina física o virtual, debe anular el clúster y volver a crearlo con la nueva versión.

Hay dos flujos de trabajo distintos para actualizar el clúster a la versión más reciente o a una versión de Service Fabric compatible. Uno para los clústeres con conectividad para descargar automáticamente la versión más reciente y otro para los clústeres sin conectividad para descargar la versión más reciente de Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Actualización de los clústeres con conectividad para descargar el código y la configuración más recientes
Siga estos pasos para actualizar el clúster a una versión compatible, si los nodos del clúster tienen conectividad a internet para [http://download.microsoft.com](http://download.microsoft.com) 

Para los clústeres con conectividad para [http://download.microsoft.com](http://download.microsoft.com), comprobamos periódicamente la disponibilidad de nuevas versiones de Service Fabric.

Cuando una nueva versión de Service Fabric está disponible, el paquete se descarga en el clúster y se aprovisiona para la actualización. Además, para informar al cliente de esta nueva versión, el sistema coloca una advertencia de estado de clúster explícita similar a esta:

"La compatibilidad de la versión [n.º de versión] del clúster actual finaliza el [fecha]". Cuando el clúster ejecute la versión más reciente, la advertencia desaparece.

#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización del clúster.
Cuando vea la advertencia de mantenimiento del clúster, debe hacer lo siguiente:

1. Conéctese al clúster desde cualquier máquina con acceso de administrador a todas las máquinas que se muestran como nodos en el clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster
   
    ```powershell
   
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
2. Obtenga la lista de versiones de Service Fabric a las que puede actualizar
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    debería obtener una salida similar a esta:
   
    ![obtener versiones de tejido][getfabversions]
3. Iniciar una actualización de clúster en una de las versiones disponibles mediante el [cmd Inicio ServiceFabricClusterUpgrade de PowerShell ](https://msdn.microsoft.com/library/mt125872.aspx)
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Puede supervisar el progreso de la actualización en Service Fabric Explorer o puede ejecutar el siguiente comando de PowerShell
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Puede especificar las directivas de mantenimiento personalizado en el momento para el comando Start-ServiceFabricClusterUpgrade; consulte [este documento](https://msdn.microsoft.com/library/mt125872.aspx) para más información. 

Una vez que haya solucionado los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Actualización de los clústeres <U>sin conectividad</u> para descargar el código y la configuración más recientes
Siga estos pasos para actualizar el clúster a una versión compatible, si los nodos del clúster **no tienen** conectividad a internet para [http://download.microsoft.com](http://download.microsoft.com) 

> [!NOTE]
> Si está ejecutando un clúster que no esté conectado a internet, tendrá que supervisar el blog del equipo de Service Fabric para recibir notificaciones de las nuevas versiones. El sistema **no** le avisa del estado de clúster.  
> 
> 

1. Modifique la configuración del clúster para establecer la siguiente propiedad en false.
   
        "fabricClusterAutoupgradeEnabled": false,

y comenzar una actualización de la configuración. Consulte el [cmd Start-ServiceFabricClusterConfigurationUpgrade de PS ](https://msdn.microsoft.com/en-us/library/mt788302.aspx) para los detalles de uso. Asegúrese de actualizar 'clusterConfigurationVersion' en JSON antes de comenzar la actualización de la configuración.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización del clúster.
1. Descargue la versión más reciente del paquete del documento [Creación y administración de un clúster que se ejecute en Windows Server](service-fabric-cluster-creation-for-windows-server.md) 
2. Conéctese al clúster desde cualquier máquina con acceso de administrador a todas las máquinas que se muestran como nodos en el clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster 
   
    ```powershell
   
    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. Copie el paquete descargado en el almacén de imágenes del clúster.
   
    ```powershell
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

1. Registro del paquete copiado 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
2. Inicie una actualización de clúster a una de las versiones disponibles. 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Puede supervisar el progreso de la actualización en Service Fabric Explorer o puede ejecutar el siguiente comando de PowerShell
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Puede especificar las directivas de mantenimiento personalizado en el momento para el comando start-serviceFabricClusterUpgrade; consulte [este documento](https://msdn.microsoft.com/library/mt125872.aspx) para más información. 

Una vez que haya solucionado los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

## <a name="next-steps"></a>Pasos siguientes
* Aprender a personalizar la [configuración de Service Fabric para el clúster](service-fabric-cluster-fabric-settings.md)
* Aprenda cómo [escalar o reducir horizontalmente el clúster](service-fabric-cluster-scale-up-down.md)
* Obtenga información sobre [actualizaciones de aplicaciones](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG



<!--HONumber=Nov16_HO3-->


