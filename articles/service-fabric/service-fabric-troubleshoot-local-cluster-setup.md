<properties
   pageTitle="Solución de problemas de instalación del clúster local"
   description="En este artículo se trata de un conjunto de sugerencias para solucionar problemas de su clúster de desarrollo local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="seanmck"/>

# Solución de problemas de instalación del clúster de desarrollo local

Si surge un problema al interactuar con el clúster de desarrollo local, revise las siguientes sugerencias para obtener posibles soluciones.

## Errores de instalación de clúster

### No se pueden limpiar los registros de Service Fabric

#### Problema

Mientras se ejecuta el script DevClusterSetup, verá un error similar al siguiente:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Solución

Cierre la ventana de PowerShell actual e inicie una nueva ventana de PowerShell como administrador. Ahora podrá ejecutar correctamente el script.

## Errores de conexión del clúster

### Excepción de inicialización de tipo

#### Problema

Al conectarse al clúster en PowerShell o en el Explorador de Service Fabric, verá un TypeInitializationException para System.Fabric.Common.AppTrace.

#### Solución

La variable de ruta de acceso no se estableció correctamente durante la instalación. Cierre la sesión de Windows y vuelva a iniciarla. Se actualizará completamente la ruta de acceso.

### Se produce un error de conexión del clúster con el mensaje "El objeto está cerrado"

#### Problema

Se produce un error en una llamada a Connect-ServiceFabricCluster con un error similar al siguiente:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Solución

Cierre la ventana de PowerShell actual e inicie una nueva ventana de PowerShell como administrador. Ahora podrá conectarse correctamente.

### FabricConnectionDeniedException

#### Problema

Cuando se depura desde Visual Studio, obtiene una excepción FabricConnectionDeniedException.

#### Solución

Este error suele producirse cuando se intenta iniciar manualmente un proceso de host de servicio en lugar de permitir que el tiempo de ejecución de Service Fabric se inicie automáticamente.

Asegúrese de que no tenga ningún proyecto de servicio establecido como proyecto de inicio de la solución. Solo los proyectos de aplicación de Service Fabric deben establecerse como proyectos de inicio.


## Pasos siguientes

- [Comprensión y solución de problemas de clústeres con informes de mantenimiento del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)
 

<!---HONumber=July15_HO2-->