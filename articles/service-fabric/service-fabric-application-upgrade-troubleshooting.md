---
title: "Solución de problemas de las actualizaciones de aplicaciones | Microsoft Docs"
description: "En este artículo se tratan algunos de los problemas comunes de actualización de una aplicación de Service Fabric y cómo resolverlos."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: subramar
ms.openlocfilehash: acfd26674aafab4ed1925d6b33967f917058b1be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-application-upgrades"></a>Solucionar problemas de actualizaciones de aplicaciones
En este artículo se tratan algunos de los problemas comunes de actualización de una aplicación de Azure Service Fabric y cómo resolverlos.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Solucionar problemas de una actualización de aplicación con error
Cuando se produce un error en una actualización, la salida del comando **Get-ServiceFabricApplicationUpgrade** contiene información adicional para depurar el error.  En la lista siguiente se especifica cómo se puede usar esa información adicional:

1. Identificar el tipo de error.
2. Identificar el motivo del error.
3. Aísle uno o más componentes de error para investigarlos en profundidad.

Esta información está disponible tan pronto como Service Fabric detecta el error, con independencia de si el valor de **FailureAction** es revertir o suspender la actualización.

### <a name="identify-the-failure-type"></a>Identificar el tipo de error
En la salida de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica la marca de tiempo (en UTC) en la que Service Fabric encontró un error de actualización y se desencadenó **FailureAction**. **FailureReason** identifica una de las tres posibles causas de alto nivel del error:

1. UpgradeDomainTimeout: indica que un determinado dominio de actualización tardó demasiado tiempo en completarse y **UpgradeDomainTimeout** expiró.
2. OverallUpgradeTimeout: indica que la actualización general tardó demasiado tiempo en completarse y **UpgradeTimeout** expiró.
3. HealthCheck: indica que, tras la actualización de un dominio de actualización, la aplicación permaneció en estado incorrecto según las directivas de mantenimiento especificadas y **HealthCheckRetryTimeout** expiró.

Estas entradas solo aparecen en la salida cuando se produce un error en la actualización y empieza la reversión. Según el tipo de error, se muestra información adicional.

### <a name="investigate-upgrade-timeouts"></a>Investigar los tiempos de espera de actualización
Los errores de tiempo de espera de actualización se producen habitualmente por problemas de disponibilidad del servicio. La salida que sigue a este párrafo es habitual de las actualizaciones donde réplicas o instancias de servicio no se pueden iniciar en la nueva versión del código. El campo **UpgradeDomainProgressAtFailure** captura una instantánea de cualquier trabajo de actualización pendiente en el momento del error.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

En este ejemplo, la actualización produjo error en el dominio de actualización *MYUD1* y dos particiones, (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* y *4b43f4d8-b26b-424e-9307-7a7a62e79750*), se bloquearon. Esto fue debido a que el entorno de tiempo de ejecución no pudo colocar las réplicas principales (*WaitForPrimaryPlacement*) en los nodos de destino *Node1* y *Node4*.

El comando **Get-ServiceFabricNode** se puede usar para comprobar que estos dos nodos están en el dominio de actualización *MYUD1*. La fase *UpgradePhase* indica *PostUpgradeSafetyCheck*, lo que significa que estas comprobaciones de seguridad se producen después de que todos los nodos del dominio de actualización hayan finalizado la actualización. Toda esta información señala a un posible error con la nueva versión del código de la aplicación. Los problemas más habituales son errores de servicio en la apertura o promoción a rutas de acceso de código principal.

Una fase *UpgradePhase* de *PreUpgradeSafetyCheck* indica que se produjeron errores al preparar el dominio de actualización antes de realizar la actualización. Los problemas más habituales en este caso son errores de servicio en el cierre o disminución de nivel de las rutas de acceso de código principales.

El objeto **UpgradeState** actual es *RollingBackCompleted*, de modo que la actualización original debe haberse realizado con un objeto **FailureAction** de reversión, que revierte automáticamente la actualización tras el error. Si la actualización original se había realizado con un valor de **FailureAction**manual, la actualización estaría entonces en un estado suspendido para permitir una depuración en directo de la aplicación.

En raras ocasiones, el campo **UpgradeDomainProgressAtFailure** puede estar vacío si se agota el tiempo de expiración de la actualización general cuando el sistema completa todo el trabajo del dominio de actualización actual. Si esto sucede, intente aumentar los valores de los parámetros de actualización **UpgradeTimeout** y **UpgradeDomainTimeout** y vuelva a intentar la actualización.

### <a name="investigate-health-check-failures"></a>Investigar errores de comprobación de estado
Los errores de comprobación de estado pueden desencadenarse debido a varios problemas que se pueden producir después de que todos los nodos de un dominio de actualización terminen de actualizarse y pasen todas las comprobaciones de seguridad. La salida que sigue a este párrafo es habitual de un error de actualización debido a errores en las comprobaciones de estado. El campo **UnhealthyEvaluations** captura una instantánea de todas las comprobaciones de estado con error en el momento de la actualización según la [directiva de mantenimiento](service-fabric-health-introduction.md)especificada.

```
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Para investigar errores de comprobación de estado, es necesario conocer el modelo de mantenimiento de Service Fabric, pero incluso sin una comprensión profunda, podemos ver que los dos servicios son incorrectos: *fabric:/DemoApp/Svc3* y *fabric:/DemoApp/Svc2*, junto con los informes de mantenimiento de error ("InjectedFault" en este caso). En este ejemplo, dos de cada cuatro servicios son incorrectos, una relación que están por debajo del objetivo predeterminado de 0 % incorrecto (*MaxPercentUnhealthyServices*).

La actualización se suspendió tras el error al especificar un valor de **FailureAction** manual cuando se inicia la actualización. Este modo nos permite investigar el sistema activo en el estado de error antes de realizar ninguna acción.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperarse de una actualización suspendida
Con un valor de **FailureAction**de reversión, no hace falta ninguna recuperación puesto que la actualización se revierte automáticamente tras el error. Con una **FailureAction**manual, hay varias opciones de recuperación:

1.  Desencadenar una reversión
2. Completar por el resto de la actualización manualmente
3. Reanudar la actualización supervisada

El comando **Start-ServiceFabricApplicationRollback** puede usarse en cualquier momento para empezar a revertir la aplicación. Cuando el comando se devuelve correctamente, la solicitud de reversión se ha registrado en el sistema y comienza en breve.

El comando **Resume-ServiceFabricApplicationUpgrade** se puede usar para continuar con el resto de la actualización manualmente, un dominio de actualización cada vez. En este modo, el sistema solo realiza comprobaciones de seguridad. No se realizan más comprobaciones de estado. Este comando solo se puede usar cuando *UpgradeState* muestra *RollingForwardPending*, lo que significa que el dominio de actualización actual ha terminado de actualizarse pero que el siguiente no se ha iniciado (está pendiente).

El comando **Update-ServiceFabricApplicationUpgrade** se puede usar para reanudar la actualización supervisada mientras se llevan a cabo las comprobaciones tanto de seguridad como de estado.

```
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
```

La actualización continúa desde el dominio de actualización donde se suspendió la última vez y usa los mismos parámetros de actualización y las mismas directivas de mantenimiento que antes. En caso necesario, cualquiera de los parámetros de actualización y directivas de mantenimiento que se muestran en la salida anterior puede cambiarse en el mismo comando cuando la actualización se reanuda. En este ejemplo, la actualización se reanudó en modo supervisado, con los parámetros y las directivas de mantenimiento sin cambios.

## <a name="further-troubleshooting"></a>Más información de solución de problemas
### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric no sigue las directivas de mantenimiento especificadas
Causa posible 1:

Service Fabric convierte todos los porcentajes en números reales de entidades (por ejemplo, réplicas, particiones y servicios) para la evaluación de estado y siempre se redondea a las entidades completas. Por ejemplo, si el valor máximo de *MaxPercentUnhealthyReplicasPerPartition* es 21 % y hay cinco réplicas, Service Fabric permite hasta dos réplicas con estado incorrecto (es decir, `Math.Ceiling (5*0.21)`). Por lo tanto, las directivas de mantenimiento deben establecerse como corresponda.

Causa posible 2:

Las directivas de mantenimiento se especifican en términos de porcentajes de servicios totales e instancias de servicio no específicas. Por ejemplo, antes de una actualización, supongamos que una aplicación tiene cuatro instancias de servicio A, B, C y D, donde el servicio D tiene un estado incorrecto, pero sin ningún impacto importante en la aplicación. Queremos omitir el servicio D con estado incorrecto conocido durante la actualización y establecer el parámetro *MaxPercentUnhealthyServices* para que sea el 25 % suponiendo que solo A, B y C deban tener un estado correcto.

Sin embargo, durante la actualización, D puede convertirse en correcto mientras C pasa a ser incorrecto. Aun así, la actualización se realizaría correctamente porque solo el 25 % de los servicios es incorrecto. Sin embargo, como es C quién es incorrecto inesperadamente, en lugar de D, pueden producirse errores no anticipados. En esta situación, D se debe modelar como un tipo de servicio diferente de A, B y C. Como las directivas de mantenimiento se especifican por tipo de servicio, se pueden aplicar diferentes umbrales de porcentajes incorrectos a diferentes servicios. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>No he especificado una directiva de mantenimiento para la actualización de la aplicación, pero la actualización continúa generando errores para algunos tiempos de expiración que nunca he especificado
Si no se proporcionan directivas de mantenimiento para la solicitud de actualización, se obtienen del archivo *ApplicationManifest.xml* de la versión actual de la aplicación. Por ejemplo, si va a actualizar la aplicación x de la versión 1.0 a la versión 2.0, se usan las directivas de mantenimiento especificadas para la versión 1.0. Si se debe usar una directiva de mantenimiento diferente para la actualización, la directiva necesita especificarse como parte de la llamada API de actualización de la aplicación. Las directivas especificadas como parte de la llamada API solo se aplican durante la actualización. Cuando se completa la actualización, se usan las directivas especificadas en el archivo *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Se especifican los tiempos de expiración incorrectos
Es posible que se pregunte por lo que sucede cuando los tiempos de espera se establecen de forma incoherente. Por ejemplo, si *UpgradeTimeout* es inferior a *UpgradeDomainTimeout*. La respuesta es que se devuelve un error. Se devuelven errores si *UpgradeDomainTimeout* es inferior a la suma de *HealthCheckWaitDuration* y *HealthCheckRetryTimeout*, o si *UpgradeDomainTimeout* es inferior a la suma de *HealthCheckWaitDuration* y *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mis actualizaciones están tardando mucho tiempo
El tiempo que tarde una actualización dependerá de las comprobaciones de estado y de los tiempos de espera especificados. Las comprobaciones de estado y los tiempos de espera dependen de lo que se tarde en copiar, implementar y estabilizar la aplicación. Ser demasiado agresivo con los tiempos de expiración puede dar lugar a que se realicen más actualizaciones con errores; por ello, recomendamos empezar de manera conservadora con tiempos de expiración más prolongados.

A continuación ofrecemos un repaso rápido sobre la interacción de los tiempos de expiración con los tiempos de actualización:

Las actualizaciones de un dominio de actualización no se pueden completar con mayor rapidez que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

No se puede producir un error de actualización con mayor rapidez que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

El tiempo de actualización de un dominio de actualización está limitado por *UpgradeDomainTimeout*.  Si *HealthCheckRetryTimeout* y *HealthCheckStableDuration* son distintos de cero y el estado de la aplicación continúa pasando de atrás para adelante, la actualización agotará el tiempo de espera en *UpgradeDomainTimeout*. *UpgradeDomainTimeout* comienza la cuenta atrás cuando comienza la actualización para el dominio de actualización actual.

## <a name="next-steps"></a>Pasos siguientes
[actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

[actualización de aplicaciones mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).
