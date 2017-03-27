---
title: "Actualización del clúster de Azure Service Fabric independiente en Windows Server | Microsoft Docs"
description: "Actualice el código de Azure Service Fabric o la configuración que ejecuta un clúster de Azure Service Fabric independiente, incluida la configuración del modo de actualización del clúster."
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
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: eedaefeed1a704f7816e71ef7b2dddda2268a90f
ms.lasthandoff: 03/14/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Actualizar el clúster de Azure Service Fabric independiente en Windows Server
> [!div class="op_single_selector"]
> * [Clúster de Azure](service-fabric-cluster-upgrade.md)
> * [Clúster independiente](service-fabric-cluster-upgrade-windows-server.md)
>
>

En cualquier sistema moderno, la capacidad de actualización es clave para el éxito a largo plazo del producto. Un clúster de Azure Service Fabric es un recurso que usted posee. Este artículo describe cómo puede asegurarse de que el clúster siempre ejecuta versiones compatibles y las configuraciones del código de Service Fabric.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Control de la versión de Service Fabric que se ejecuta en el clúster
Para establecer el clúster para que descargue actualizaciones de Service Fabric cuando Microsoft publica una nueva versión, establezca la configuración del clúster **fabricClusterAutoupgradeEnabled** en true. Para seleccionar una versión compatible de Service Fabric en la que desee que esté el clúster, establezca la configuración de clúster **fabricClusterAutoupgradeEnabled** en false.

> [!NOTE]
> Asegúrese de que el clúster siempre ejecuta una versión compatible de Service Fabric. Cuando Microsoft anuncia el lanzamiento de una nueva versión de Service Fabric, se marca la versión anterior para que finalice el soporte después de un mínimo de 60 días a partir de la fecha del anuncio. Las versiones nuevas se anuncian [en el blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nueva versión estará disponible para su elección a partir de ese momento.
>
>

Puede actualizar el clúster a la nueva versión solo si usa una configuración de nodo de estilo de producción, donde cada nodo de Service Fabric se asigna a una máquina virtual o física independiente. Si tiene un clúster de desarrollo, donde hay más de un nodo de Service Fabric en una única máquina física o virtual, debe volver a crear el clúster con la nueva versión.

Hay dos flujos de trabajo distintos para actualizar el clúster a la versión más reciente o a una versión de Service Fabric compatible. Uno de los flujos es para los clústeres que tienen conectividad para descargar automáticamente la versión más reciente. El otro es para los clústeres que no tienen conectividad para descargar la versión más reciente de Service Fabric.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Actualización de los clústeres con conectividad para descargar el código y la configuración más recientes
Siga estos pasos para actualizar el clúster a una versión compatible, si los nodos del clúster tienen conectividad a Internet para conectarse a [http://download.microsoft.com](http://download.microsoft.com)

Para los clústeres con conectividad en [http://download.microsoft.com](http://download.microsoft.com), Microsoft comprueba periódicamente la disponibilidad de nuevas versiones de Service Fabric.

Cuando una nueva versión de Service Fabric está disponible, el paquete se descarga localmente en el clúster y se aprovisiona para la actualización. Además, para informar al cliente de esta nueva versión, el sistema muestra una advertencia de estado de clúster explícita similar a la siguiente:

"La compatibilidad con la versión [versión] de clúster actual finaliza el [Fecha]."

Después de que el clúster ejecuta la versión más reciente, se quita la advertencia.

#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización de clúster
Cuando vea la advertencia de mantenimiento del clúster, haga lo siguiente:

1. Conéctese al clúster desde cualquier máquina con acceso de administrador a todas las máquinas que se muestran como nodos en el clúster. La máquina donde se ejecuta este script no tiene que formar parte del clúster.

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

2. Obtenga la lista de versiones de Service Fabric a las que puede actualizar.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Debería obtener una salida similar a esta:

    ![obtener versiones de tejido][getfabversions]
3. Inicie una actualización de clúster a una versión disponible mediante el cmd de PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Para supervisar el progreso de la actualización, puede usar Service Fabric Explorer o ejecutar el siguiente comando de Windows PowerShell.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Para especificar las directivas de mantenimiento personalizado para el comando **Start-serviceFabricClusterUpgrade** consulte la documentación de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Después de corregir los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos descritos anteriormente.

### <a name="upgrade-clusters-that-have-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Actualización de los clústeres <U>sin conectividad</u> para descargar el código y la configuración más recientes
Siga estos pasos para actualizar el clúster a una versión compatible, si los nodos del clúster no tienen conectividad a Internet para conectarse a [http://download.microsoft.com](http://download.microsoft.com).

> [!NOTE]
> Si está ejecutando un clúster que no está conectado a Internet, tendrá que supervisar el blog del equipo de Service Fabric para conocer más información acerca de una nueva versión. El sistema no muestra ninguna advertencia de mantenimiento de clúster para informarle de una nueva versión.  
>
>

Modifique la configuración del clúster para establecer la siguiente propiedad en false antes de iniciar una actualización de la configuración.

        "fabricClusterAutoupgradeEnabled": false,

Consulte el [cmd Start-ServiceFabricClusterConfigurationUpgrade de PS ](https://msdn.microsoft.com/en-us/library/mt788302.aspx) para los detalles de uso. Asegúrese de actualizar 'clusterConfigurationVersion' en JSON antes de iniciar la actualización de la configuración.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Flujo de trabajo de actualización de clúster

1. Descargue la versión más reciente del paquete del documento [Create Service Fabric cluster for Windows Server](service-fabric-cluster-creation-for-windows-server.md) (Crear un clúster de Service Fabric para Windows Server).
2. Conéctese al clúster desde cualquier máquina con acceso de administrador a todas las máquinas que se muestran como nodos en el clúster. El equipo donde se ejecuta este script no tiene que formar parte del clúster.

    ```powershell

    ###### Connect to the cluster
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

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. Registre el paquete copiado.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
5. Inicie una actualización del clúster a una versión disponible.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Puede supervisar el progreso de la actualización en Service Fabric Explorer o puede ejecutar el siguiente comando de PowerShell.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Para especificar las directivas de mantenimiento personalizado para el comando **Start-serviceFabricClusterUpgrade** consulte la documentación de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Después de corregir los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos descritos anteriormente.


## <a name="upgrade-the-cluster-configuration"></a>Actualizar la configuración del clúster
Para actualizar la configuración del clúster, ejecute **Start-ServiceFabricClusterConfigurationUpgrade**. El dominio de actualización procesa la actualización de la configuración.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```


## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre cómo personalizar la [configuración de clúster de Service Fabric](service-fabric-cluster-fabric-settings.md).
* Obtenga información sobre cómo [escalar o reducir horizontalmente el clúster](service-fabric-cluster-scale-up-down.md).
* Obtenga información sobre [actualizaciones de aplicaciones](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

