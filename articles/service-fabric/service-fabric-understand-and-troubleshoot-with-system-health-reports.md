---
title: "Solución de problemas con los informes de mantenimiento del sistema | Microsoft Docs"
description: "Describe los informes de estado enviados por los componentes de Azure Service Fabric y su uso para la resolución de problemas de clúster o de aplicaciones."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: oanapl
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 458e14f48a329cd36d3986986724e587839b355e
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utilización de informes de mantenimiento del sistema para solucionar problemas
Los componentes de Azure Service Fabric informan desde el primer momento de todas las entidades del clúster. El [almacén de estado](service-fabric-health-introduction.md#health-store) crea y elimina entidades basándose en los informes del sistema. También las organiza en una jerarquía que captura las interacciones de la entidad.

> [!NOTE]
> Para comprender los conceptos relacionados con el mantenimiento, obtenga más información sobre el [modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md).
> 
> 

Los informes de mantenimiento del sistema proporcionan visibilidad en el clúster y funcionalidad de la aplicación, así como indican problemas a través del mantenimiento. Para aplicaciones y servicios, los informes de mantenimiento del sistema comprueban que las entidades se implementan y que se comportan correctamente desde la perspectiva de Service Fabric. Los informes no proporcionan ninguna supervisión del mantenimiento de la lógica empresarial del servicio o de la detección de los procesos bloqueados. Los servicios de usuario pueden enriquecer los datos de mantenimiento con información específica de su lógica.

> [!NOTE]
> Solo son visibles los informes de mantenimiento de los guardianes *después* de que los componentes del sistema hayan creado la entidad. Cuando se elimina una entidad, el almacén de estado elimina automáticamente todos los informes de mantenimiento asociados a ella. Lo mismo sucede cuando se crea una nueva instancia de la entidad (por ejemplo, se crea una nueva instancia de la réplica del servicio con estado y persistente). Todos los informes asociados a la instancia anterior se eliminan y se limpian del almacén.
> 
> 

Los informes de los componentes del sistema se identifican mediante el origen, que comienza por el prefijo "**System.**" . Los guardianes no pueden utilizar el mismo prefijo para sus orígenes, ya que los informes con parámetros no válidos se rechazan.
Veamos algunos informes de sistema para entender qué los desencadena y cómo corregir los posibles problemas que representan.

> [!NOTE]
> Service Fabric sigue agregando informes de condiciones de interés que mejoran la visibilidad de lo que sucede en el clúster y la aplicación. También se pueden mejorar los informes existentes con más detalles para ayudar a solucionar el problema con mayor rapidez.
> 
> 

## <a name="cluster-system-health-reports"></a>Informes de mantenimiento del sistema de clúster
La entidad de mantenimiento del clúster se crea automáticamente en el Almacén de estado. Si todo funciona correctamente, no tiene un informe del sistema.

### <a name="neighborhood-loss"></a>Pérdida de entorno
**System.Federation** notifica un error cuando detecta una pérdida de entorno. El informe procede de los nodos individuales y el identificador de nodo está incluido en el nombre de la propiedad. Si hay una pérdida de entorno en todo el anillo de Service Fabric, puede esperar dos eventos (se notifican ambos lados del intervalo). Si se pierden más entornos, se producen más eventos.

El informe especifica el tiempo de espera de concesión global como período de vida. El informe se vuelve a enviar una vez transcurrida la mitad de la duración del período de vida siempre y cuando la condición permanezca activa. El evento se quita automáticamente cuando haya expirado. El comportamiento de eliminación cuando caduca garantiza que el informe se limpie del Almacén de estado correctamente, incluso si el nodo de informes está inactivo.

* **SourceId**: System.Federation
* **Propiedad**: comienza por **Neighborhood** e incluye información sobre el nodo.
* **Pasos siguientes**: investigue por qué se pierde el entorno (por ejemplo, compruebe la comunicación entre los nodos del clúster).

## <a name="node-system-health-reports"></a>Informes de mantenimiento del sistema de nodos
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los nodos del clúster. Todos los nodos deben tener un informe de System.FM que muestre el estado. Las entidades de nodo se quitan cuando se quita el estado del nodo (consulte [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync)).

### <a name="node-updown"></a>Nodo activo o inactivo
System.FM notifica que está todo correcto cuando el nodo se une al anillo (está en funcionamiento). Notifica un error cuando el nodo sale del anillo (no funciona, ya sea porque se está actualizando o simplemente porque no pudo). La jerarquía de mantenimiento generada por el almacén de estado realiza una acción sobre las entidades implementadas en correlación con los informes de nodo de System.FM. Considera el nodo como un elemento primario virtual de todas las entidades implementadas. Las entidades implementadas en ese nodo se exponen a través de las consultas si System.FM notifica que el nodo está activo, con la misma instancia que la instancia asociada a las entidades. Cuando System.FM notifica que el nodo está inactivo o que se ha reiniciado (una nueva instancia), el almacén de estado limpia automáticamente las entidades implementadas que solo pueden existir en el nodo inactivo o en la instancia anterior del nodo.

* **SourceId**: System.FM
* **Property**: State
* **Pasos siguientes**: si el nodo está inactivo durante una actualización, debería volver a estar activo una vez que se haya actualizado. En este caso, se debe cambiar el estado de mantenimiento a Correcto. Si el nodo no recupera ese estado o se produce un error, deberá investigar más el problema.

El ejemplo siguiente muestra el evento System.FM con el estado de mantenimiento Correcto para el nodo activo:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Caducidad del certificado
**System.FabricNode** notifica una advertencia cuando los certificados usados por el nodo están a punto de expirar. Hay tres certificados por nodo: **Certificate_cluster**, **Certificate_server** y **Certificate_default_client**. Si faltan más de dos semanas para expirar, el estado de mantenimiento del informe es Correcto. Si faltan menos de dos semanas, el tipo de informe es una advertencia. El TTL de estos eventos es infinito y se quitan cuando un nodo deja el clúster.

* **SourceId**: System.FabricNode
* **Property**: comienza por **Certificate** y contiene más información sobre el tipo de certificado.
* **Pasos siguientes**: actualice los certificados si están a punto de expirar.

### <a name="load-capacity-violation"></a>Infracción de la capacidad de carga
El equilibrador de carga de Service Fabric notifica una advertencia cuando detecta una infracción de la capacidad del nodo.

* **SourceId**: System.PLB
* **Property**: comienza por **Capacity**.
* **Pasos siguientes**: compruebe las métricas proporcionadas y vea la capacidad actual en el nodo.

## <a name="application-system-health-reports"></a>Informes de mantenimiento del sistema de la aplicación
**System.CM**, que representa el servicio Administrador de clústeres, es la autoridad que administra la información acerca de una aplicación.

### <a name="state"></a>Estado
System.CM notifica un estado Correcto cuando se ha creado o actualizado la aplicación. Informa al almacén de estado cuando se ha eliminado la aplicación, por lo que puede quitarse del almacén.

* **SourceId**: System.CM
* **Property**: State
* **Pasos siguientes**: si se ha creado o actualizado la aplicación, debe incluir el informe de mantenimiento de Cluster Manager. De lo contrario, compruebe el estado de la aplicación mediante el envío de una consulta (por ejemplo, el cmdlet de PowerShell **Get-ServiceFabricApplication -ApplicationName *nombreAplicación***).

El ejemplo siguiente muestra el evento de estado en la aplicación **fabric:/WordCount** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Informes de mantenimiento del sistema de servicio
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los servicios.

### <a name="state"></a>Estado
System.FM notifica un estado Correcto cuando se ha creado el servicio. Elimina la entidad del almacén de estado cuando se ha eliminado el servicio.

* **SourceId**: System.FM
* **Property**: State

El ejemplo siguiente muestra el evento de estado en el servicio **fabric:/WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Error de correlación de servicio
**System.PLB** notifica un error cuando detecta que la actualización de un servicio que se va a correlacionar con otro crea una cadena de afinidad. El informe se borra cuando se produce una actualización correcta.

* **SourceId**: System.PLB
* **Propiedad**: ServiceDescription
* **Pasos siguientes**: compruebe las descripciones de servicios correlacionados.

## <a name="partition-system-health-reports"></a>Informes de mantenimiento del sistema de partición
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de las particiones del servicio.

### <a name="state"></a>Estado
System.FM notifica un estado Correcto cuando la partición se ha creado y es correcta. Elimina la entidad del almacén de estado cuando se ha eliminado la partición.

Si la partición es inferior al recuento mínimo de réplicas, notifica un error. Si la partición no es inferior al recuento mínimo de réplicas, pero es inferior al recuento objetivo de réplicas, notifica una advertencia. Si la partición está en situación de pérdida de cuórum, System.FM notifica un error.

Otros eventos importantes incluyen una advertencia cuando la reconfiguración tarda más tiempo de lo esperado y cuando la compilación tarda más de lo previsto. Los tiempos de compilación o reconfiguración previstos se pueden configurar en función de los escenarios de servicio. Por ejemplo, si un servicio tiene un terabyte de estado como, por ejemplo, SQL Database, la compilación tarda más tiempo del que tardaría para un servicio con una pequeña cantidad de estado.

* **SourceId**: System.FM
* **Property**: State
* **Pasos siguientes**: si el estado de mantenimiento no es correcto, es posible que algunas réplicas no se hayan creado, abierto o promocionado a principales o secundarias de manera correcta. En muchos casos, la causa raíz es un error de servicio en la implementación del rol de apertura o cambio.

El ejemplo siguiente muestra una partición correcta:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

El ejemplo siguiente muestra el mantenimiento de una partición que es inferior al recuento objetivo de réplicas. El paso siguiente consiste en obtener la descripción de la partición, que muestra cómo se ha configurado: **MinReplicaSetSize** es dos y **TargetReplicaSetSize** es siete. Después, obtenga el número de nodos del clúster: cinco. Así que en este caso, no se pueden colocar dos réplicas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/P RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Infracción de restricción de réplica
**System.PLB** notifica una advertencia si detecta una infracción de restricción de réplica y no se pueden colocar todas las réplicas de la partición. Los detalles del informe muestran qué restricciones y propiedades evitan la ubicación de las réplicas.

* **SourceId**: System.PLB
* **Property**: comienza por **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Informes de mantenimiento del sistema de replica
**System.RA**, que representa el componente del agente de reconfiguración, es la autoridad para el estado de la réplica.

### <a name="state"></a>Estado
**System.RA** notifica un estado Correcto cuando se ha creado la réplica.

* **SourceId**: System.RA
* **Property**: State

El ejemplo siguiente muestra una réplica correcta:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replica-open-status"></a>Estado de apertura de réplica
La descripción de este informe de mantenimiento contiene la hora de inicio (hora universal coordinada) de cuando se ha invocado la llamada API.

**System.RA** notifica una advertencia si la apertura de la réplica tarda más tiempo del configurado (valor predeterminado: 30 minutos). Si la API afecta a la disponibilidad de servicio, el informe se emite con mayor rapidez (intervalo configurable, valor predeterminado de 30 segundos). El tiempo medido incluye el tiempo necesario para la apertura del replicador y del servicio. La propiedad cambia a Correcto si se completa la apertura.

* **SourceId**: System.RA
* **Property**: **ReplicaOpenStatus**
* **Pasos siguientes**: si el estado de mantenimiento no es Correcto, compruebe por qué la apertura de la réplica tarda más de lo previsto.

### <a name="slow-service-api-call"></a>Llamada a la API de servicio lenta
**System.RAP** y **System.Replicator** notifican una advertencia si una llamada al código de servicio de usuario tarda más tiempo del configurado. La advertencia se borra cuando finaliza la llamada.

* **SourceId**: System.RAP o System.Replicator
* **Property**: el nombre de la API lenta. La descripción proporciona más detalles sobre el tiempo que la API ha estado pendiente.
* **Pasos siguientes**: investigue por qué la llamada tarda más de lo previsto.

El ejemplo siguiente muestra una partición en la pérdida de quórum, así como los pasos de investigación realizados para descubrir el motivo. Una de las réplicas tiene un estado de mantenimiento de advertencia, por tanto, ya conoce su estado. Muestra que la operación de servicio tarda más de lo previsto, un evento notificado por System.RAP. Después de recibir esta información, el paso siguiente es examinar el código de servicio e investigar. En este caso, la implementación de **RunAsync** del servicio con estado produce una excepción no controlada. Las réplicas se reciclan, por lo que quizás no pueda ver ninguna réplica en estado de advertencia. Puede intentar obtener de nuevo el estado de mantenimiento y buscar las diferencias en el identificador de réplica. En algunos casos, los reintentos pueden proporcionar pistas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics

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

Al iniciar la aplicación defectuosa en el depurador, las ventanas de eventos de diagnóstico muestran la excepción de RunAsync:

![Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en fabric:/HelloWorldStatefulApplication.][1]

Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en **fabric:/HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Cola de replicación completa
**System.Replicator** notifica una advertencia cuando la cola de replicación está llena. En la réplica principal, la cola de replicación suele llenarse porque una o varias réplicas secundarias son lentas a la hora de confirmar las operaciones. En la secundaria, esto suele ocurrir cuando el servicio es lento en aplicar las operaciones. La advertencia se borra cuando la cola ya no está llena.

* **SourceId**: System.Replicator
* **Property**: **PrimaryReplicationQueueStatus** o **SecondaryReplicationQueueStatus**, según el rol de réplica.

### <a name="slow-naming-operations"></a>Operaciones de nomenclatura lentas
**System.NamingService** informa del estado en su réplica principal cuando una operación de nomenclatura tarda más de lo aceptable. [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) o [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) son ejemplos de operaciones de nomenclatura. En FabricClient pueden encontrarse más métodos, como en [métodos de administración de servicios](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) o [métodos de administración de propiedades](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> El servicio de nombres resuelve los nombres de servicio en una ubicación del clúster y permite a los usuarios administrar las propiedades y los nombres de servicio. Se trata de un servicio guardado con particiones de Service Fabric. Una de las particiones representa a Authority Owner, que contiene metadatos sobre todos los nombres y servicios de Service Fabric. Los nombres de Service Fabric se asignan a particiones diferentes, denominadas particiones Name Owner, por lo que el servicio se puede ampliar. Obtenga más información sobre el [servicio de nomenclatura](service-fabric-architecture.md).
> 
> 

Cuando una operación de nomenclatura tarda más de lo esperado, la operación se marca con un informe de advertencia en la *réplica principal de la partición del servicio de nomenclatura que se usa para la operación*. Si la operación se completa correctamente, la advertencia se elimina. Si la operación se completa con un error, el informe de estado incluye detalles sobre el error.

* **SourceId**: System.NamingService
* **Property**: comienza con el prefijo **Duration_** e identifica la operación lenta y el nombre de Service Fabric en el que se aplica la operación. Por ejemplo, si el servicio de creación en el nombre fabric:/MyApp/MyService tarda demasiado, la propiedad es Duration_AOCreateService.fabric:/MyApp/MyService. AO apunta al rol de la partición de nomenclatura para este nombre y operación.
* **Pasos siguientes**: compruebe por qué se produce un error en la operación de nomenclatura. Cada operación puede tener diferentes causas. Por ejemplo, el servicio de eliminación puede estar atascado en un nodo debido a que el host de la aplicación se bloquee continuamente en un nodo por un error de usuario en el código del servicio.

El ejemplo siguiente muestra una operación de servicio de creación. La operación tardó más de la duración configurada. AO vuelve a intentarlo y envía trabajo a NO. NO completó la última operación con tiempo de espera. En este caso, la misma réplica es la principal tanto para el rol de AO como para el de NO.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Informes de mantenimiento del sistema DeployedApplication
**System.Hosting** es la autoridad en las entidades implementadas.

### <a name="activation"></a>Activación
System.Hosting notifica un estado Correcto cuando una aplicación se ha activado correctamente en el nodo. De lo contrario, notifica un error.

* **SourceId**: System.Hosting
* **Property**: Activation, incluida la versión de lanzamiento.
* **Pasos siguientes**: si el mantenimiento de la aplicación es incorrecto, investigue el motivo del error de la activación.

El ejemplo siguiente muestra una activación correcta:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Descargar
**System.Hosting** notifica un error si se produjo un error al descargar el paquete de aplicación.

* **SourceId**: System.Hosting
* **Property**: **Download:*RolloutVersion***
* **Pasos siguientes**: investigue el motivo del error de descarga en el nodo.

## <a name="deployedservicepackage-system-health-reports"></a>Informes de mantenimiento del sistema DeployedServicePackage
**System.Hosting** es la autoridad en las entidades implementadas.

### <a name="service-package-activation"></a>Activación de paquete de servicio
System.Hosting notifica un estado Correcto si la activación del paquete de servicio en el nodo se ha realizado correctamente. De lo contrario, notifica un error.

* **SourceId**: System.Hosting
* **Property**: Activation.
* **Pasos siguientes**: investigue el motivo del error de la activación.

### <a name="code-package-activation"></a>Activación del paquete de código
**System.Hosting** notifica un estado Correcto para cada paquete de código si la activación se ha realizado correctamente. Si se produce un error en la activación, notifica una advertencia tal y como está configurado. Si **CodePackage** no se puede activar o finaliza con un error mayor que el configurado **CodePackageHealthErrorThreshold**, el hospedaje notifica un error. Si hay varios paquetes de código en un paquete de servicios, se genera un informe de activación para cada uno.

* **SourceId**: System.Hosting
* **Property**: usa el prefijo **CodePackageActivation** y contiene el nombre del paquete de código y el punto de entrada como **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** (por ejemplo, **CodePackageActivation:Code:SetupEntryPoint**).

### <a name="service-type-registration"></a>Registro del tipo de servicio
**System.Hosting** notifica un estado Correcto si el tipo de servicio se ha registrado correctamente. Notifica un error si el registro no se ha realizado a tiempo (tal y como se ha configurado mediante **ServiceTypeRegistrationTimeout**). Si se cierra el tiempo de ejecución, el tipo de servicio no está registrado en el nodo y Hosting notifica una advertencia.

* **SourceId**: System.Hosting
* **Property**: usa el prefijo **ServiceTypeRegistration** y contiene el nombre del tipo de servicio (por ejemplo, **ServiceTypeRegistration:FileStoreServiceType**).

El ejemplo siguiente muestra un paquete de servicio implementado con mantenimiento correcto:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Descargar
**System.Hosting** notifica un error si se produce un error en la descarga del paquete de servicio.

* **SourceId**: System.Hosting
* **Property**: **Download:*RolloutVersion***
* **Pasos siguientes**: investigue el motivo del error de descarga en el nodo.

### <a name="upgrade-validation"></a>Validación de actualización
**System.Hosting** notifica un error si se produce un error de validación durante la actualización o si se produce un error de actualización en el nodo.

* **SourceId**: System.Hosting
* **Property**: usa el prefijo **FabricUpgradeValidation** y contiene la versión de actualización.
* **Description**: señala el error encontrado.

## <a name="next-steps"></a>Pasos siguientes
[Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Supervisión y diagnóstico de los servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)


