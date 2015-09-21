<properties
   pageTitle="Visualización del mantenimiento agregado de entidades de Service Fabric de Azure"
   description="Describe cómo consultar, ver y evaluar el mantenimiento agregado de entidades de Service Fabric de Azure, a través de consultas de mantenimiento y generales."
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

# Cómo ver los informes de mantenimiento de Service Fabric
Service Fabric presenta un [modelo de mantenimiento](service-fabric-health-introduction.md) formado por entidades de mantenimiento sobre qué componentes y guardianes locales del sistema pueden notificar qué condiciones locales están supervisando. El [almacén de mantenimiento](service-fabric-health-introduction.md#health-store) agrega todos los datos de mantenimiento para determinar si las entidades son correctas.

De fábrica, el clúster se rellena con los informes de mantenimiento enviados por los componentes del sistema. Obtenga más información en [Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric proporciona varias maneras de obtener el mantenimiento agregado de las entidades:

- [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) y otras herramientas de visualización

- Consultas de mantenimiento (a través de Powerhsell/API/REST)

- Las consultas generales que devuelven una lista de entidades con el mantenimiento como una de las propiedades (a través de Powerhsell/API/REST)

Para mostrar estas opciones, vamos a usar un clúster local con **5 nodos**. Junto a la aplicación **fabric:/System** (que existe de serie), hay algunas otras aplicaciones implementadas, una de las cuales es **fabric:/WordCount**. Esta aplicación contiene un servicio con estado configurado con 7 réplicas. Puesto que solo hay 5 nodos, los componentes del sistema marcarán que la partición es inferior al recuento de destino con una advertencia.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Mantenimiento del Explorador de Service Fabric
El Explorador de Service Fabric proporciona una vista visual del clúster. En la siguiente ilustración, puede ver que:

- La aplicación **fabric:/WordCount** está en "rojo" (en Error) porque tiene un evento de error notificado por MyWatchdog para la propiedad Availability.

- Uno de sus servicios, **fabric:/WordCount/WordCount.Service** está en "amarillo" (en Advertencia). Como se describió anteriormente, el servicio está configurado con 7 réplicas, que no se puede poner (ya que solo hay 5 nodos). Aunque no se muestra aquí, la partición de servicio está en "amarillo" debido al informe del sistema. La partición en "amarillo" desencadena el servicio en "amarillo".

- El **clúster** está en "rojo" debido a la aplicación en "rojo".

La evaluación usa las directivas predeterminadas desde el manifiesto de clúster y el manifiesto de la aplicación, que son las directivas estrictas (no toleran ningún error).

![Vista del clúster con ServiceFabricExplorer.][1]

Vista del clúster con ServiceFabricExplorer.

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]Obtenga más información sobre el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md).

## Consultas de mantenimiento
Service Fabric expone las consultas de mantenimiento para cada uno de los [tipos de entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy) admitidos. Se pueden acceder a través de API (métodos en FabricClient.HealthManager), cmdlets de PowerShell y REST. Estas consultas devuelven información de mantenimiento completa acerca de la entidad, incluido el estado de mantenimiento agregado, eventos de mantenimiento informados sobre la entidad, los estados de mantenimiento de los elementos secundarios (si procede) y las evaluaciones de mantenimiento incorrecto cuando el mantenimiento de la entidad no es correcto.

> [AZURE.NOTE]Una entidad de mantenimiento se devuelve al usuario cuando se rellena completamente en el almacén de mantenimiento: la entidad tiene un informe de sistema, está activo (no eliminado) y las entidades principales en la cadena de jerarquía tienen informes de sistema. Si no se cumple alguna de estas condiciones, las consultas de mantenimiento devuelven una excepción que se muestra por qué no se devuelve la entidad.

Las consultas de mantenimiento requieren pasar el identificador de entidad, que depende del tipo de entidad. Aceptan parámetros de directivas de mantenimiento opcionales. Si no se especifican, se usan para la evaluación las [directivas de mantenimiento](service-fabric-health-introduction.md#health-policies) del manifiesto de aplicación o de clúster. También aceptan filtros para devolver solo los elementos secundarios o eventos parciales, los que respetan los filtros especificados.

> [AZURE.NOTE]Los filtros de salida se aplican en el servidor, por lo que se reduce el tamaño de la respuesta del mensaje. Se recomienda usar los filtros para limitar los datos devueltos en lugar de aplicar filtros en el cliente.

Un mantenimiento de entidad contiene la siguiente información:

- El estado de mantenimiento agregado de la entidad. Esto lo calcula el almacén de mantenimiento en función de los informes de mantenimiento de entidades, estados de mantenimiento de los elementos secundarios (si procede) y las directivas de mantenimiento. Obtenga más información sobre la [Evaluación del mantenimiento de entidades](service-fabric-health-introduction.md#entity-health-evaluation).  

- Eventos de mantenimiento de la entidad.

- Para las entidades que pueden tener elementos secundarios, una colección de los estados de mantenimiento para todos los elementos secundarios. Los estados de mantenimiento contienen el identificador de entidad y el estado de mantenimiento agregado. Para obtener el mantenimiento para un elemento secundario, llame el mantenimiento de la consulta para el tipo de entidad secundario, pasando el identificador del elemento secundario.

- Si la entidad no es correcta, las evaluaciones de mantenimiento incorrecto que seleccionen el informe que desencadenó el estado de la entidad.

## Obtención del mantenimiento de clúster
Devuelve el mantenimiento de la entidad de clúster. Contiene los estados de mantenimiento de aplicaciones y nodos (elementos secundarios del clúster). Entrada:

- [Opcional] Asignación de la directiva de mantenimiento de aplicación con las directivas de mantenimiento usadas para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos, nodos, aplicaciones con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento del clúster, cree un FabricClient y llame al método GetClusterHealthAsync en su administrador de mantenimiento.

Lo siguiente obtiene el mantenimiento del clúster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Lo siguiente obtiene el mantenimiento del clúster mediante la directiva de mantenimiento de clústeres personalizada y filtros para nodos y aplicaciones. Tenga en cuenta que crea System.Fabric.Description.ClusterHealthQueryDescription que contiene todos los datos de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del clúster es Get-ServiceFabricClusterHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster. Estado del clúster: 5 nodos, la aplicación System y fabric:/WordCount configurado como el anterior.

El siguiente cmdlet obtiene el mantenimiento del clúster con directivas de mantenimiento predeterminadas. El estado de mantenimiento agregado es Advertencia, porque la aplicación fabric:/WordCount está en advertencia. Tenga en cuenta cómo las evaluaciones de mantenimiento incorrecto muestran detalles de la condición que ha desencadenado el mantenimiento agregado.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

El siguiente cmdlet de PowerShell obtiene el mantenimiento del clúster con la directiva de aplicación personalizada. Filtra los resultados para obtener nodos y aplicaciones de solo error o advertencia+. Como resultado, no se devolverá ningún nodo ya que todos son correctos. Solo la aplicación fabric:/WordCount respeta el filtro de aplicaciones. Puesto que la directiva personalizada especifica que hay que tener en cuenta la advertencia como un error para la aplicación fabric:/WordCount, la aplicación se evalúa en Error y de la misma forma el clúster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Obtención del mantenimiento de nodo
Devuelve el mantenimiento de una entidad del nodo. Contiene los eventos de mantenimiento notificados en el nodo. Entrada:

- [Obligatorio] El nombre del nodo que identifica el nodo.

- [Opcional] Configuración de la directiva de mantenimiento de clústeres usada para evaluar el mantenimiento.

- [Opcional] Filtro para devolver solo eventos con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento del clúster a través de la API, cree un FabricClient y llame al método GetNodeHealthAsync en su administrador de mantenimiento.

Lo siguiente obtiene el mantenimiento del nodo para el nombre de nodo especificado.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Lo siguiente obtiene el mantenimiento del nodo para el nombre de nodo especificado, pasando el filtro de eventos y la directiva personalizada a través de System.Fabric.Description.NodeHealthQueryDescription.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del nodo es Get-ServiceFabricNodeHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster. El siguiente cmdlet obtiene el mantenimiento del nodo con directivas de mantenimiento predeterminadas.

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

El siguiente cmdlet obtiene el mantenimiento de todos los nodos del clúster.

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Obtención del mantenimiento de la aplicación
Devuelve el mantenimiento de una entidad de aplicación. Contiene los estados de mantenimiento de la aplicación implementada y de los elementos secundarios de servicio. Entrada:

- [Obligatorio] Nombre de la aplicación (Uri) que identifica la aplicación

- [Opcional] Directiva de mantenimiento de aplicación usada para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos, servicios, aplicaciones implementadas con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento de la aplicación, cree un FabricClient y llame al método GetApplicationHealthAsync en su administrador de mantenimiento.

Lo siguiente obtiene el mantenimiento de la aplicación para el Uri del nombre de la aplicación especificado.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Lo siguiente obtiene el mantenimiento de la aplicación para el Uri del nombre de aplicación especificado, mediante la especificación de filtros y directivas personalizadas a través de System.Fabric.Description.ApplicationHealthQueryDescription.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la aplicación es Get-ServiceFabricApplicationHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster.

El siguiente cmdlet devuelve el mantenimiento de la aplicación fabric:/WordCount.

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

El PowerShell siguiente pasa la directiva personalizada y filtra los elementos secundarios y eventos.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Obtención del mantenimiento del servicio
Devuelve el mantenimiento de una entidad del servicio. Contiene los estados de mantenimiento de la partición. Entrada:

- [Obligatorio] Nombre del servicio (Uri) que identifica el servicio
- [Opcional] Directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.
- [Opcional] Filtro para devolver solo eventos y particiones con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento del servicio a través de la API, cree un FabricClient y llame al método GetServiceHealthAsync en su administrador de mantenimiento.

En el ejemplo siguiente se obtiene el mantenimiento de un servicio con el nombre de servicio especificado (Uri):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

Lo siguiente obtiene el mantenimiento del servicio para el Uri del nombre de aplicación especificado, mediante la especificación de filtros y directivas personalizadas a través de System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del servicio es Get-ServiceFabricServiceHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster.

El siguiente cmdlet obtiene el mantenimiento del servicio con directivas de mantenimiento predeterminadas.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Obtención del mantenimiento de partición
Devuelve el mantenimiento de una entidad de partición. Contiene los estados de mantenimiento de la réplica. Entrada:

- [Obligatorio] Identificador de partición (Guid) que identifica la partición

- [Opcional] Directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos, réplicas con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento de la partición a través de la API, cree un FabricClient y llame al método GetPartitionHealthAsync en su administrador de mantenimiento. Para especificar parámetros opcionales, cree System.Fabric.Description.PartitionHealthQueryDescription.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la partición es Get-ServiceFabricPartitionHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster.

El cmdlet siguiente obtiene el mantenimiento de todas las particiones del servicio de recuento de palabras.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Obtención del mantenimiento de réplica
Devuelve el mantenimiento de una réplica. Entrada.

- [Obligatorio] Identificador de partición (Guid) e identificador de réplica que identifican a la réplica

- [Opcional] Parámetros de la directiva de mantenimiento de aplicación usados para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento de la réplica a través de la API, cree un FabricClient y llame al método GetReplicaHealthAsync en su administrador de mantenimiento. Especifique los parámetros avanzados con System.Fabric.Description.ReplicaHealthQueryDescription.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la réplica es Get-ServiceFabricReplicaHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster.

El cmdlet siguiente obtiene el mantenimiento de las réplicas principales para todas las particiones del servicio.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtención del mantenimiento de la aplicación implementada
Devuelve el mantenimiento de una aplicación implementada en una actividad del nodo. Contiene los estados de mantenimiento del paquete de servicio implementado. Entrada:

- [Obligatorio] Nombre de la aplicación (Uri) y nombre del nodo (cadena) que identifican la aplicación implementada

- [Opcional] Directiva de mantenimiento de aplicación usada para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos, paquetes de servicio implementados con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento en una aplicación implementada en un nodo a través de API, cree un FabricClient y llame al método GetDeployedApplicationHealthAsync en su administrador de mantenimiento. Para especificar parámetros opcionales, cree System.Fabric.Description.DeployedApplicationHealthQueryDescription.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la aplicación implementada es Get-ServiceFabricDeployedApplicationHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster. Para averiguar dónde se implementa una aplicación, ejecute Get-ServiceFabricApplicationHealth y observe los elementos secundarios de la aplicación implementada.

El siguiente cmdlet obtiene el mantenimiento de la aplicación fabric:/WordCount implementada en el nodo Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtención del mantenimiento del paquete de servicio implementado
Devuelve el mantenimiento de una entidad de paquete de servicio implementado. Entrada:

- [Obligatorio] El nombre de la aplicación (URI), el nombre de nodo (cadena) y el nombre de manifiesto de servicio (cadena) que identifican el paquete de servicio implementado.

- [Opcional] Directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.

- [Opcional] Filtro para devolver solo eventos con determinado estado de mantenimiento (p. ej., devolver solo errores o advertencias y errores).

### API
Para obtener el mantenimiento de un paquete de servicio implementado a través de API, cree un FabricClient y llame al método GetDeployedServicePackageHealthAsync en su administrador de mantenimiento.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del paquete de servicio implementado es Get-ServiceFabricDeployedServicePackageHealth. Conéctese primero al clúster con el cmdlet Connect-ServiceFabricCluster. Para ver dónde se implementa una aplicación, ejecute Get-ServiceFabricApplicationHealth y observe las aplicaciones implementadas. Para ver qué paquetes de servicio están en una aplicación, examine los elementos secundarios del paquete del servicio implementado en la salida de Get-ServiceFabricDeployedApplicationHealth.

El siguiente cmdlet obtiene el mantenimiento del paquete de servicio WordCount.Service de la aplicación fabric:/WordCount implementada en el nodo Node.1. La entidad tiene informes System.Hosting para la activación correcta del paquete de servicio y el punto de entrada y el registro de tipo de servicio correcto.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Consultas generales
Las consultas generales devuelven la lista de entidades de Service Fabric del tipo especificado. Se exponen a través de API (métodos en FabricClient.QueryManager), cmdlets de PowerShell y REST. Estas consultas agregan subconsultas de varios componentes. Uno de ellos es el [almacén de mantenimiento](service-fabric-health-introduction.md#health-store), que rellena el estado de mantenimiento agregado para cada resultado de la consulta.

> [AZURE.NOTE]Las consultas generales devuelven el estado de mantenimiento agregado de la entidad y no contienen los datos completos de mantenimiento. Si el mantenimiento de una entidad no es correcto, puede seguir con las consultas de mantenimiento para obtener toda información de mantenimiento, como eventos, estados de mantenimiento de los elementos secundarios y las evaluaciones de mantenimiento incorrecto.

Si las consultas generales devuelven un estado de mantenimiento desconocido para una entidad, es posible que el almacén de mantenimiento no tenga los datos completos de la entidad o la subconsulta del almacén de mantenimiento no se realizó correctamente (p. ej., un error de comunicación, el almacén de mantenimiento está limitado, etc.). Realice un seguimiento con una consulta de mantenimiento para la entidad. Esto puede funcionar si la subconsulta ha detectado errores transitorios (p. ej. problemas de red), o se brindará más información acerca de por qué la entidad no se expone en el almacén de mantenimiento.

Las consultas que contienen HealthState para las entidades son las siguientes:

- Lista de nodos. Devuelve los nodos de la lista en el clúster.
  - Api: FabricClient.QueryManager.GetNodeListAsync.
  - PowerShell: Get-ServiceFabricNode.
- Lista de aplicaciones. Devuelve la lista de aplicaciones del clúster.
  - Api: FabricClient.QueryManager.GetApplicationListAsync.
  - PowerShell: Get-ServiceFabricApplication.
- Lista de servicios Devuelve la lista de servicios de una aplicación.
  - Api: FabricClient.QueryManager.GetServiceListAsync.
  - PowerShell: Get-ServiceFabricService.
- Lista de particiones. Devuelve la lista de particiones de un servicio.
  - Api: FabricClient.QueryManager.GetPartitionListAsync.
  - PowerShell: Get-ServiceFabricPartition.
- Lista de réplicas. Devuelve la lista de réplicas de una partición.
  - Api: FabricClient.QueryManager.GetReplicaListAsync.
  - PowerShell: Get-ServiceFabricReplica.
- Lista de aplicaciones implementadas. Devuelve la lista de aplicaciones implementadas en un nodo.
  - Api: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.
- Lista de paquetes de servicios implementados. Devuelve la lista de paquetes de servicios en una aplicación implementada.
  - Api: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.

### Ejemplos

Lo siguiente obtiene las aplicaciones cuyo mantenimiento es incorrecto en el clúster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

El siguiente cmdlet obtiene los detalles de la aplicación para la aplicación fabric:/WordCount. Observe que el estado de mantenimiento es Advertencia.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

El siguiente cmdlet obtiene los servicios con estado de mantenimiento de Advertencia.

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Actualización de clústeres y de aplicaciones
Durante la actualización supervisada de clústeres y aplicaciones, Service Fabric comprueba el mantenimiento para asegurarse de que todo es y permanece correcto. Si hay algo incorrecto en las directivas configuradas, la actualización se pausa para permitir la interacción del usuario o se revierte automáticamente.

Durante la actualización del **clúster**, puede obtener el estado de actualización del clúster, que incluirá las evaluaciones del mantenimiento incorrecto que señalan lo que es incorrecto en el clúster. Si la actualización se revierte debido a problemas de mantenimiento, el estado de la actualización conservará las últimas razones del mantenimiento incorrecto para que los administradores pueden investigar qué salió mal. De forma similar, durante la actualización de la **aplicación**, el estado de actualización de la aplicación contiene las evaluaciones del mantenimiento incorrecto.

A continuación se muestra el estado de actualización de la aplicación para una aplicación fabric/WordCount modificada. Un guardián local ha informado de un error en una de sus réplicas. La actualización se revierte porque no se respetan las comprobaciones de mantenimiento.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Obtenga más información sobre la [Actualización de aplicaciones de Service Fabric](service-fabric-application-upgrade.md).

## Solución de problemas de mantenimiento
Siempre que haya un problema en el clúster o en una aplicación, consulte el mantenimiento del clúster o de la aplicación para aislar el problema. Las evaluaciones cuyo mantenimiento sea incorrecto mostrarán con detalles lo que activó el estado incorrecto actual. Si es necesario, profundice en las entidades de los elementos secundarios incorrectos para descubrir problemas.

## Pasos siguientes
[Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adición de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)

[Cómo supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Sept15_HO2-->