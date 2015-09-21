<properties
   pageTitle="Uso de informes de mantenimiento del sistema para solucionar problemas"
   description="Describe los informes de mantenimiento del sistema y cómo utilizarlas para solucionar problemas de clúster o de aplicación."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Uso de informes de mantenimiento del sistema para solucionar problemas

Los componentes de Service Fabric informan desde el primer momento de todas las entidades del clúster. El [almacén de mantenimiento](service-fabric-health-introduction.md#health-store) crea y elimina entidades basándose en los informes del sistema y los organiza en una jerarquía que captura las interacciones de la entidad.

> [AZURE.NOTE]Obtenga más información sobre el [Modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md) para comprender los conceptos relacionados con el mantenimiento.

Los informes de mantenimiento del sistema proporcionan visibilidad en el clúster y funcionalidad de la aplicación, así como indican problemas a través del mantenimiento. Para aplicaciones y servicios, los informes de mantenimiento del sistema comprueban que las entidades se implementan y que se comportan correctamente desde la perspectiva de Service Fabric. Los informes no proporcionan ninguna supervisión del mantenimiento de la lógica empresarial del servicio o de la detección de los procesos bloqueados. Los servicios de usuario pueden enriquecer los datos de mantenimiento con información específica de su lógica.

> [AZURE.NOTE]Solo son visibles los informes de mantenimiento de los guardianes locales **después** de que los componentes del sistema hayan creado la entidad. Cuando se elimina una entidad, el almacén de mantenimiento elimina automáticamente todos los informes de mantenimiento asociados a él. Igual cuando se crea una nueva instancia de la entidad (p. ej., se crea una nueva instancia de la réplica de servicio): se eliminan todos los informes asociados a la instancia anterior y se limpian del almacén.

Los informes de los componentes del sistema se identifican mediante el origen, que comienza por el prefijo *"System"*. Los guardianes locales no puede utilizar el mismo prefijo de sus orígenes, ya que los informes se rechazarán con parámetros no válidos. Veamos algunos informes de sistema y comprenda qué los desencadena y cómo corregir los posibles problemas que representan.

> [AZURE.NOTE]Service Fabric sigue agregando informes de condiciones de interés que podrían mejorar la visibilidad de lo que sucede en el clúster o aplicación.

## Informes de mantenimiento del sistema de clúster
La entidad de mantenimiento del clúster se crea automáticamente en el almacén de mantenimiento, por tanto si todo funciona correctamente no habrá un informe del sistema.

### Pérdida de entorno
System.Federation notifica un Error cuando detecta una pérdida de entorno. El informe procede de los nodos individuales y el identificador de nodo se incluye en el nombre de la propiedad. Si hay una pérdida de entorno en el todo el anillo de Service Fabric, podemos esperar dos eventos (notificarán ambos lados del intervalo). Si hay más pérdida de entorno, se producirán más eventos. El informe especifica el tiempo de espera de la concesión global como el período de vida y el informe se vuelve a enviar cada mitad del TTL siempre que la condición siga activa. El evento se quita automáticamente cuando caduca, por lo que si el nodo de informes está inactivo, sigue limpio en el almacén de estado.

- SourceId: System.Federation
- Propiedad: comienza por "Neighborhood" e incluye información sobre el nodo.
- Pasos siguientes: investigue el motivo de la pérdida del entorno. P. ej.. Comprobar la comunicación entre los nodos del clúster.

## Informes de mantenimiento del sistema de nodo
System.FM, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los nodos del clúster. Los nodos deben tener un informe de System.FM que muestre el estado. Las entidades de nodo se quitan cuando el nodo está deshabilitado.

### Nodo activo o inactivo
System.FM notifica Ok cuando el nodo se une el anillo (está activo) y Error cuando el nodo sale del anillo (no está activo, ya sea para actualizarse, o simplemente produjo un error). La jerarquía de mantenimiento generada por el almacén de mantenimiento realiza una acción sobre las entidades implementadas en correlación con los informes de nodo System.FM. Considera el nodo como un elemento principal virtual de todas las entidades implementadas. Las entidades implementadas en ese nodo no se exponen a través de las consultas si el nodo está inactivo o no se notifican, o el nodo tiene una instancia distinta de la instancia asociada a las entidades. Cuando FM notifica que el nodo está inactivo o que se ha reiniciado (nueva instancia), el almacén de mantenimiento limpia automáticamente las entidades implementadas que solo pueden existir en el nodo inactivo o en la instancia anterior del nodo.

- SourceId: System.FM
- Propiedad: State
- Pasos siguientes: si el nodo está inactivo para la actualización, debe activarse una vez actualizado, en cuyo caso debe volver a cambiar el estado de mantenimiento a Ok. Si el nodo no recupera ese estado o produce un error, necesita más investigación.

A continuación muestra el evento System.FM con el estado de mantenimiento Ok para el nodo activo:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Caducidad del certificado
System.FabricNode notifica de una advertencia cuando los certificados usados por el nodo están a punto de caducar. Hay tres certificados por nodo: Certificate\_cluster, Certificate\_server y Certificate\_default\_client. Cuando la fecha de caducidad es de al menos dos semanas, el tipo de informe es correcto; si la fecha de caducidad es inferior a dos semanas, el tipo de informes es de advertencia. El TTL de estos eventos es infinito, se quitan cuando el nodo deja el clúster.

- SourceId: System.FabricNode
- Propiedad: comienza por "Certificate" y contiene más información sobre el tipo de certificado.
- Pasos siguientes: actualice los certificados si están a punto de caducar.

### Infracción de la capacidad de carga
El equilibrador de carga de Service Fabric informa de una advertencia si detecta una infracción de la capacidad de nodo.

 - SourceId: System.PLB
 - Propiedad: comienza por "Capacity".
 - Pasos siguientes: compruebe las métricas proporcionadas y vea la capacidad actual en el nodo.

## Informes de mantenimiento del sistema de aplicación
System.CM, que representa el servicio Administrador de clústeres, es la autoridad que administra la información acerca de la aplicación.

### Estado
System.CM informa de un estado Ok cuando se crea o actualiza la aplicación. Informa al almacén de mantenimiento cuando se elimina la aplicación, por lo que puede quitarse del almacén.

- SourceId: System.CM
- Propiedad: State
- Pasos siguientes: si se crea la aplicación, debe tener el informe de mantenimiento de CM. De lo contrario, compruebe el estado de la aplicación mediante la envío de una consulta (p. ej. el cmdlet de PowerShell Get-ServiceFabricApplication - ApplicationName <applicationName>).

Lo siguiente muestra el evento de estado en la aplicación fabric:/WordCount.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Informes de mantenimiento del sistema de servicio
System.FM, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los servicios.

### Estado
System.FM notifica de un estado Ok cuando se crea el servicio. Elimina la entidad de almacén de mantenimiento cuando se elimina el servicio.

- SourceId: System.FM
- Propiedad: State.

Lo siguiente muestra el evento de estado en el servicio fabric:/WordCount/WordCountService.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Infracción de réplicas sin colocar
System.PLB informa de una advertencia si no pudo encontrar una ubicación para una o varias de las réplicas del servicio. El informe se quita cuando caduca.

- SourceId: System.FM
- Propiedad: State.
- Pasos siguientes: compruebe las restricciones de servicio. Compruebe el estado actual de la ubicación.

## Informes de mantenimiento del sistema de partición
System.FM, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de las particiones del servicio.

### Estado
System.FM notifica Ok cuando la partición se crea y es correcta. Elimina la entidad de almacén de mantenimiento cuando se elimina la partición. Si la partición es inferior al recuento mínimo de réplicas, notifica un error. Si la partición no es inferior al recuento mínimo de réplicas, pero es inferior al recuento objetivo de réplicas, notifica una advertencia. Si la partición ha perdido el quórum, FM notifica de un error. Otros eventos importantes son: advertencia, cuando la reconfiguración tarda más tiempo de lo esperado y cuando la compilación tarda más de lo previsto. Los tiempos de compilación o reconfiguración esperados se pueden configurar en función de los escenarios de servicio. Por ejemplo, si un servicio tiene TB de estado, como SQL Azure, la compilación tardará más que un servicio con una pequeña cantidad de estado.

- SourceId: System.FM
- Propiedad: State.
- Pasos siguientes: si el estado de mantenimiento no es correcto, es posible que algunas réplicas no se hayan creado/abierto/promocionado a los elementos principales o secundarios de manera correcta. En muchos casos, la causa raíz es un error de servicio en la implementación de la función de apertura o cambio.

Lo siguiente muestra una partición correcta.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Lo siguiente muestra el mantenimiento de una partición que es inferior al recuento objetivo de réplicas. Pasos siguientes: obtenga la descripción de la partición, que muestra cómo se ha configurado: MinReplicaSetSize es 2, TargetReplicaSetSize es 7. Después, obtenga el número de nodos del clúster: 5. Así que en este caso, no se pueden colocar dos réplicas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Infracción de restricción de réplica
System.PLB notifica de una advertencia si detecta una infracción de restricción de réplica y no se pueden colocar réplicas de la partición.

- SourceId: System.PLB
- Propiedad: comienza por "ReplicaConstraintViolation".

## Informes de mantenimiento del sistema de replica
System.RA, que representa el componente del agente de reconfiguración, es la autoridad para el estado de la réplica.

### Estado
System.RA notifica Ok cuando se crea la réplica.

- SourceId: System.RA
- Propiedad: State.

Lo siguiente muestra una réplica correcta.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Estado de apertura de réplica
La descripción de este informe de mantenimiento contiene la hora de inicio (UTC) de cuando se ha invocado la llamada de API. System.RA informa de advertencia si la apertura de la réplica tarda más tiempo del configurado (valor predeterminado: 30 minutos). Si la API afecta a la disponibilidad de servicio, el informe se emite con mayor rapidez (intervalo configurable, valor predeterminado de 30 segundos). Este tiempo incluye el tiempo necesario para la apertura del replicador y del servicio. La propiedad cambia a Ok si se completa la apertura.

- SourceId: System.RA
- Propiedad: ReplicaOpenStatus.
- Pasos siguientes: si el estado de mantenimiento no es Ok, compruebe por qué la apertura de la réplica tarda más de lo esperado.

### Llamada a la API de servicio lenta
System.RAP y System.Replicator informa de una advertencia si una llamada al código de servicio de usuario tarda más tiempo del configurado. La advertencia se borra cuando finaliza la llamada.

- SourceId: System.RAP o System.Replicator
- Propiedad: el nombre de la API lenta. La descripción ofrece más detalles sobre el tiempo que la API ha estado pendiente.
- Pasos siguientes: investigue por qué la llamada tarda más tiempo de lo esperado.

El ejemplo siguiente muestra una partición la pérdida de quórum así como los pasos de investigación realizados para descubrir el motivo. Una de las réplicas tiene el estado de mantenimiento de advertencia, por tanto, tenemos su mantenimiento. Muestra que la operación de servicio tarda más de esperado, evento notificado por System.RAP. Después de esta información, el paso siguiente es examinar el código de servicio e investigar. En este caso, la implementación de RunAsync del servicio con estado produce una excepción no controlada. Tenga en cuenta que las réplicas se reciclan, por lo que quizás no pueda ver ninguna réplica en estado de advertencia. Vuelva a intentar obtener el mantenimiento y el aspecto si hay diferencias en los identificadores de réplica; le dará pistas en ciertos casos.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Al iniciar la aplicación defectuosa con error en el depurador, las ventanas de eventos de diagnóstico muestran la excepción de RunAsync:

![Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en fabric:/HelloWorldStatefulApplication.][1]

Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en fabric:/HelloWorldStatefulApplication.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Cola de replicación completa
System.Replicator notifica de una ¡advertencia si la cola de replicación está llena. En la principal, esto suele ocurrir porque una o varias réplicas secundarias de las réplicas son lentas en confirmar operaciones. En la secundaria, esto suele ocurrir cuando el servicio es lento en aplicar las operaciones. La advertencia se borra cuando la cola ya no está llena.

- SourceId: System.Replicator
- Propiedad: PrimaryReplicationQueueStatus o SecondaryReplicationQueueStatus, dependiendo del rol de réplica.

## Informes de mantenimiento del sistema DeployedApplication
System.Hosting es la autoridad en las entidades implementadas.

### Activación
System.Hosting notifica Ok cuando una aplicación se activó correctamente en el nodo; notifica de error en caso contrario.

- SourceId: System.Hosting
- Propiedad: Activation. Incluye la versión de lanzamiento.
- Pasos siguientes: si el mantenimiento no es correcto, investigue el motivo del error de la activación.

A continuación se muestra una activación correcta:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Descargar
System.Hosting notifica un error si se produjo un error al descargar el paquete de aplicación.

- SourceId: System.Hosting
- Propiedad: Download:<RolloutVersion>
- Pasos siguientes: investigue el motivo del error de descarga en el nodo.

## Informes de mantenimiento del sistema DeployedServicePackage
System.Hosting es la autoridad en las entidades implementadas.

### Activación de paquete de servicio
System.Hosting notifica Ok si la activación del paquete de servicio en el nodo se ha realizado correctamente, notifica de error en caso contrario.

- SourceId: System.Hosting
- Propiedad: Activation.
- Pasos siguientes: investigue el motivo del error de la activación.

### Activación del paquete de código
System.Hosting notifica Ok para cada paquete de código si la activación se ha realizado correctamente. Si se produce un error en la activación, notifica una advertencia tal como se ha configurado; si CodePackage no se pudo activar o terminó con un error más del 'CodePackageHealthErrorThreshold' configurado, Hosting notifica de un error. Si hay varios paquetes de código en un paquete de servicios, habrá un informe de activación generado para cada uno.

- SourceId: System.Hosting
- Propiedad: prefijo de CodePackageActivation y contiene el nombre del paquete de código y del punto de entrada. CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>. P. ej. CodePackageActivation:Code:SetupEntryPoint.

### Registro del tipo de servicio
System.Hosting notifica Ok si el tipo de servicio se registró correctamente. Notifica un error si el registro no se ha realizado a tiempo (configurado con ServiceTypeRegistrationTimeout). Si el tipo de servicio se anula del registro del nodo, porque se cerró el tiempo de ejecución. Hosting notifica una advertencia.

- SourceId: System.Hosting
- Propiedad: prefijo ServiceTypeRegistration y contiene el nombre del tipo de servicio. P. ej. "ServiceTypeRegistration:FileStoreServiceType"

Lo siguiente muestra un paquete de servicio implementado con mantenimiento correcto.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Descargar
System.Hosting notifica un error si se produjo un error al descargar el paquete de servicio.

- SourceId: System.Hosting
- Propiedad: Download:<RolloutVersion>
- Pasos siguientes: investigue el motivo del error de descarga en el nodo.

### Validación de actualización
System.Hosting notifica un error si se produce un error de validación durante la actualización o si se produce un error de actualización en el nodo.

- SourceId: System.Hosting
- Propiedad: prefijo FabricUpgradeValidation, contiene la versión de actualización.
- Descripción: señala el error encontrado.

## Pasos siguientes
[Visualización de los informes de mantenimiento de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Supervisión y diagnóstico de servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Sept15_HO2-->