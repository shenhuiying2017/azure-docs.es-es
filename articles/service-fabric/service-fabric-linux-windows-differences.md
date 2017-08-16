---
title: Diferencias entre Azure Service Fabric en Windows y en Linux | Documentos de Microsoft
description: "Diferencias entre Azure Service Fabric, versión preliminar, en Linux y Azure Service Fabric en Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: e2d21b28c482427c60f708171336e6901b50e544
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Diferencias entre Service Fabric en Linux (versión preliminar) y Windows (disponible con carácter general)

Dado que Service Fabric en Linux está en versión preliminar, hay varias características que se admiten en Windows, pero aún no en Linux. Por último, los conjuntos de características estarán a la par cuando Service Fabric en Linux esté disponible con carácter general. En las próximas versiones, esta distancia se reducirá. Existen las siguientes diferencias entre las últimas versiones disponibles (es decir, entre la versión 5.6 en Windows y la versión 5.5 en Linux): 

* Colecciones confiables (y servicios con estado confiables) 
* ReverseProxy 
* Instalador independiente 
* Validación del esquema XML para archivos de manifiesto 
* Redireccionamiento de la consola 
* El servicio de análisis de errores (FAS)
* Docker Compose y controladores de volúmenes y de registro para contenedores 
* Regulación de recursos para contenedores y servicios 
* Servicio DNS
* Compatibilidad con Azure Active Directory
* Comandos de la CLI equivalentes a ciertos comandos de Powershell 
* Solo se puede ejecutar un subconjunto de comandos de Powershell en un clúster de Linux (tal como se explica detalladamente en la sección siguiente).

>[!NOTE]
>El redireccionamiento de la consola no se admite en los clústeres de producción, ni siquiera en Windows.

Las herramientas de desarrollo también son diferentes en Windows y Linux. VisualStudio, Powershell, VSTS, and ETW se usan en Windows, mientras que Yeoman, Eclipse, Jenkins, y LTTng se usan en Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlets de PowerShell que no funcionan en un clúster de Service Fabric para Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Pasos siguientes
* [Prepare your development environment on Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de su primera aplicación de CSharp en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Uso de la CLI de Azure para administrar las aplicaciones de Service Fabric](service-fabric-azure-cli.md)

