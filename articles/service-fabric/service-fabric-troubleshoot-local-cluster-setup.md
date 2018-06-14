---
title: Solucionar problemas de instalación del clúster local de Azure Service Fabric | Microsoft Docs
description: En este artículo se trata de un conjunto de sugerencias para solucionar problemas de su clúster de desarrollo local
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a7f58914fd6e498e717e19bfea11c9e3fcfc0399
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212024"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solución de problemas de instalación del clúster de desarrollo local
Si surge un problema al interactuar con el clúster de desarrollo local de Azure Service Fabric, revise las siguientes sugerencias para obtener posibles soluciones.

## <a name="cluster-setup-failures"></a>Errores de instalación de clúster
### <a name="cannot-clean-up-service-fabric-logs"></a>No se pueden limpiar los registros de Service Fabric
#### <a name="problem"></a>Problema
Mientras se ejecuta el script DevClusterSetup, verá el siguiente error:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solución
Cierre la ventana de PowerShell actual y abra una nueva ventana de PowerShell como administrador. Ahora puede ejecutar correctamente el script.

## <a name="cluster-connection-failures"></a>Errores de conexión del clúster

### <a name="type-initialization-exception"></a>Excepción de inicialización de tipo
#### <a name="problem"></a>Problema
Cuando se conecta al clúster en PowerShell, aparece el error TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solución
La variable de ruta de acceso no se estableció correctamente durante la instalación. Cierre la sesión de Windows y vuelva a iniciarla. Esto actualiza la ruta de acceso.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Se produce un error de conexión del clúster con el mensaje "El objeto está cerrado"
#### <a name="problem"></a>Problema
Se produce un error en una llamada a Connect-ServiceFabricCluster con un error similar al siguiente:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solución
Cierre la ventana de PowerShell actual y abra una nueva ventana de PowerShell como administrador.

### <a name="fabric-connection-denied-exception"></a>Excepción de conexión de tejido denegada
#### <a name="problem"></a>Problema
Cuando se depura desde Visual Studio, se obtiene un error FabricConnectionDeniedException.

#### <a name="solution"></a>Solución
Este error suele producirse cuando intenta iniciar manualmente un proceso de host de servicios.

Asegúrese de que no tenga ningún proyecto de servicio establecido como proyecto de inicio de la solución. Solo los proyectos de aplicación de Service Fabric deben establecerse como proyectos de inicio.

> [!TIP]
> Si, después de la configuración, el clúster local comienza a comportarse de forma anormal, puede restablecerse con la aplicación de la bandeja del sistema de administrador de clústeres locales. De este modo se quita el clúster existente y se configura uno nuevo. Tenga en cuenta que todas las aplicaciones implementadas y los datos asociados también se quitan.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Comprensión y solución de problemas de clústeres con informes de mantenimiento del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)

