<properties
   pageTitle="Adición de informes de mantenimiento de Service Fabric personalizados"
   description="Describe cómo enviar informes de estado personalizados a entidades de estado de Azure Service Fabric. Proporciona recomendaciones para diseñar e implementar informes de estado de calidad."
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

# Adición de informes de mantenimiento de Service Fabric personalizados
Service Fabric presenta un [modelo de estado](service-fabric-health-introduction.md) diseñado para marcar condiciones de clúster o de aplicación incorrectos de entidades específicas. Esto se logra mediante el uso de **Generadores de informes de estado** (componentes del sistema y guardianes). El objetivo es obtener un diagnóstico y reparación sencillo y rápido. Los escritores de servicio deben pensar por adelantado sobre el estado. Debe efectuarse un informe de cualquier condición que pueda afectar al estado, sobre todo si puede ayudar a marcar los problemas más cerca de la raíz. Esto puede ahorrar una gran cantidad de investigación y de depuración, una vez que el servicio está en funcionamiento a escala en la nube (privada o Azure).

Los generadores de informes de Service Fabric identificaron las condiciones de interés. Informan sobre esas condiciones en función de su vista local. El [almacenamiento de estado](service-fabric-health-introduction.md#Health-Store) agrega datos de estado enviados por todos los generadores de informes para determinar si las entidades tienen un buen estado en general. El modelo está pensado para ser rico, flexible y fácil de usar. La calidad de los informes de estado determina el grado de precisión de la vista de estado del clúster. Los falsos positivos que muestran problemas de mal estado incorrectamente pueden afectar negativamente a las actualizaciones o a otros servicios que utilizan datos de estado, como servicios de reparación o mecanismos de alerta. Por lo tanto, se necesita cierto grado de reflexión para proporcionar informes que capturen condiciones que resulten interesantes de la mejor manera posible.

Para diseñar e implementar informes sobre el estado, los guardianes y componentes del sistema deben:

- Definir el estado en el que estén interesados, la forma en que se supervisa y el impacto en la funcionalidad del clúster o la aplicación. Esto define la propiedad de informe de estado y el estado de mantenimiento.

- Determinar la [entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy) a la que se aplica el informe.

- Determinar desde dónde se efectúan los informes, desde el guardián de servicio, interno o externo.

- Definir un origen utilizado para identificar el generador de informes.

- Elegir una estrategia de generación de informes, periódicamente o en las transiciones. La manera recomendada es periódicamente, ya que requiere un código más simple y, por tanto, menos propenso a errores.

- Determinar cuánto tiempo debe permanecer en el almacén de estado y cómo debe borrarse el informe de condiciones incorrectas. Esto define el tiempo de duración y eliminación del informe en el comportamiento de caducidad.

Tal y como se mencionó anteriormente, los informes pueden realizarse desde:

- La réplica de Service Fabric supervisado.

- Guardianes internos implementados como Service Fabric. Por ejemplo, un servicio sin estado de Service Fabric que supervisa los informes de problemas y condiciones. Los guardianes pueden implementarse un todos los nodos o pueden afinarse en el servicio supervisado.

- Los guardianes internos que se ejecutan en los nodos de Service Fabric pero que **no** se implementan como servicios de Service Fabric.

- Los guardianes externos que están sondeando el recurso desde **fuera** del clúster de Service Fabric. Por ejemplo, Gomez como servicio de supervisión.

> [AZURE.NOTE]De fábrica, el clúster está rellenado con informes de estado enviados por los componentes del sistema. Obtenga más información en [Uso de informes de estado del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Los informes de usuario deben enviarse en [entidades de estado](service-fabric-health-introduction.md#health-entities-and-hierarchy) ya creadas por el sistema.

Una vez que el diseño de informes de estado está vacío, el envío de informes de estado resulta sencillo. Puede realizarse a través de la API mediante FabricClient.HealthManager.ReportHealth, Powershell o a través de REST. Internamente, todos los métodos utilizan un cliente de estado contenido dentro de un cliente de Fabric. Hay botones de configuración para generar lotes de informes para mejorar el rendimiento.

> [AZURE.NOTE]El estado del informe se sincroniza y solamente representa el trabajo de validación del cliente. El hecho de que el informe sea aceptado por el cliente de mantenimiento no significa que se aplique en el almacén; se enviará de forma asincrónica, posiblemente por lotes con otros informes y el procesamiento en el servidor puede producir un error (por ejemplo, número de secuencia obsoleto, la entidad en la que se debe aplicar el informe ha sido eliminada, etc.).

## Cliente de mantenimiento
Los informes de estado se envían al almacén de estado mediante un cliente de mantenimiento, que reside en el cliente de Fabric. El cliente de mantenimiento puede configurarse con lo siguiente:

- HealthReportSendInterval. El retraso entre el momento en que el informe se agrega al cliente y el momento en que se envía al almacén de estado. Esto se utiliza para procesar por lotes los informes en un único mensaje en lugar de enviar un mensaje por cada informe, para mejorar el rendimiento. Valor predeterminado: 30 segundos.

- HealthReportRetrySendInterval. Intervalo en el que el cliente de mantenimiento vuelve a enviar informes de estado acumulado al almacén de estado. Valor predeterminado: 30 segundos.

- HealthOperationTimeout. Tiempo de espera de un mensaje de informe enviado al almacén de estado. Si un mensaje supera el tiempo de espera, el cliente de mantenimiento vuelve a intentarlo hasta que el almacén de estado confirma que se han procesado los informes. Valor predeterminado: 2 minutos.

> [AZURE.NOTE]Cuando se procesan por lotes de los informes, el cliente de Fabric debe mantenerse activo durante al menos el HealthReportSendInterval para asegurarse de que se envían. Si el mensaje se pierde o el almacén de estado no es capaz de aplicarlos debido a errores transitorios, el cliente de Fabric debe mantenerse activo durante más tiempo para darle una oportunidad de volver a intentarlo.

El almacenamiento en búfer en el cliente toma en consideración el carácter único de los informes. Por ejemplo, si un generador de informes determinado incorrecto notifica 100 informes por segundo en la misma propiedad de la misma entidad, los informes se reemplazarán por la versión más reciente. A lo sumo existirá un informe de este tipo en la cola de cliente. Si se configura el procesamiento por lotes, el número de informes que se envían al almacén de estado es simplemente de uno por intervalo de envío, el último informe agregado, que refleja el estado más reciente de la entidad. Todos los parámetros de configuración pueden especificarse al crear el FabricClient, pasando FabricClientSettings con los valores deseados para las entradas relacionadas con el estado.

Lo siguiente crea un cliente de Fabric y especifica que se deben enviar los informes en cuanto se agregan. Con errores o tiempos de espera que se pueden reintentar, los reintentos se producen cada 40 segundos.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Pueden especificarse los mismos parámetros al crear una conexión a un clúster a través de Powershell. Lo siguiente inicia una conexión a un clúster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]Para asegurarse de que servicios no autorizados no puedan notificar el estado de las entidades del clúster, el servidor puede configurarse para que acepte solamente las solicitudes de los clientes protegidos. Debido a que los informes se realizan a través de FabricClient, esto significa que el FabricClient debe tener la seguridad habilitada para poder comunicarse con el clúster, por ejemplo, con la autenticación Kerberos o de certificados.

## Informes de estado del diseño
El primer paso en la generación de informes de alta calidad es identificar las condiciones que pueden afectar al estado del servicio. Cualquier condición que puede ayudar a marcar problemas en el servicio o el clúster cuando se inician o mejor aún, antes de que ocurran, pueden ahorrar miles de millones de dólares. Piense que sufrirá un menor tiempo de inactividad, pasará menos horas de noche invertidas investigando y reparando problemas y obtendrá una alta satisfacción del cliente.

Una vez identificadas las condiciones, los escritores de guardianes necesitan averiguar cuál es la mejor forma de supervisarlos para obtener un mejor equilibrio entre coste y utilidad. Por ejemplo, considere un servicio que realice algunos cálculos complejos utilizando algunos archivos temporales en un recurso compartido. Un guardián podría supervisar el recurso compartido para asegurarse de que hay suficiente espacio disponible. Podría escuchar las notificaciones de cambios de archivo o directorio. Es posible que notifique una advertencia si se alcanza un umbral por adelantado y un error si el recurso compartido está lleno. Con una advertencia, un sistema de reparación podría iniciar la limpieza de archivos antiguos en el recurso compartido. En caso de error, un sistema de reparación podría mover la réplica de servicio a otro nodo. Tenga en cuenta cómo se describen los estados de condición en términos de mantenimiento: ¿cuál es el estado de la condición que se pueden considerar correcta o incorrecta (advertencia o error).

Una vez que se establecen los detalles de supervisión, el escritor del guardián debe determinar cómo implementar el guardián. Si las condiciones se pueden determinar desde dentro del servicio, el guardián puede formar parte del propio servicio supervisado. Por ejemplo, el código de servicio puede comprobar el uso del recurso compartido y el informe utilizando un cliente de Fabric local cada vez que intenta escribir un archivo. La ventaja de este enfoque es que la generación de informes es sencilla. Debe tener cuidado de evitar que los errores de vigilancia afecten a la funcionalidad del servicio.

La generación de informes desde dentro del servicio supervisado no es siempre una opción. Es posible que un guardián situado dentro del servicio no sea capaz de detectar las condiciones: bien porque no tiene la lógica o los datos para realizar la determinación, o bien el coste de la supervisión de las condiciones es elevado o las condiciones no son específicas de un servicio, pero afecta a las interacciones entre servicios. Otra opción es disponer de los guardianes del clúster como procesos independientes. Los guardianes simplemente supervisan las condiciones y el informe, sin que afecte a los servicios principales de ninguna manera. Por ejemplo, estos guardianes podrían implementarse como servicios sin estado en la misma aplicación, implementados en todos los nodos o en los mismos nodos que el servicio.

A veces, ejecutar un guardián en el clúster tampoco es una opción. Si las condiciones supervisadas son la disponibilidad o la funcionalidad del servicio como lo ven los usuarios, es mejor tener los guardianes en el mismo lugar que los clientes de usuario, probando las operaciones de la misma manera en que los usuarios las llaman. Por ejemplo, podemos hacer que un guardián salga al exterior del clúster y emita solicitudes al servicio, comprobando la latencia y la corrección del resultado (para un servicio de calculadora, ¿2 + 2 es igual a 4 en un tiempo razonable?).

Una vez finalizados los detalles del guardián, determine un identificador de origen que lo identifique de forma única. Si varios guardianes del mismo tipo viven en el clúster, deben informar sobre entidades diferentes, o si informan sobre la misma entidad, asegúrese de que el identificador de origen o la propiedad es diferente, para que los informes pueden coexistir. La propiedad del informe de estado debe capturar la condición supervisada. Por ejemplo, en el ejemplo anterior, la propiedad puede ser ShareSize. Si se aplican varios datos a la misma situación, la propiedad debe contener cierta información dinámica para permitir que los informes pueden coexistir. Por ejemplo, si hay varios recursos compartidos que deben supervisarse, el nombre de propiedad puede ser TamañodeRecursoCompartido-nombredeRecursoCompartido.

> [AZURE.NOTE]El almacén de estado **no** debe usarse para mantener la información de estado. Solo debe informarse de la información relacionada con la salud como estado, la información que afecta a la evaluación del estado de una entidad. El almacén de estado no se diseñó como almacén de fines generales. Utiliza una lógica de evaluación de estado para agregar todos los datos al estado de mantenimiento. Enviar información no relacionada con el estado (por ejemplo, estado de informes con estado correcto) no afectará al estado de mantenimiento agregado, pero puede afectar negativamente al rendimiento del almacén de estado.

El siguiente punto de decisión es en qué entidad generar el informe. La mayoría de veces, esto es obvio basándose en la condición. Elija la entidad con la mejor granularidad posible. Si una condición afecta a todas las réplicas de una partición, haga un informe sobre la partición, no sobre el servicio. Hay casos excepcionales en los que se necesita dedicar más esfuerzo. Si la condición afecta a una entidad (por ejemplo, réplica) pero el deseo es marcar la condición durante un período superior a la duración de la réplica, deberían notificarse en la partición. De lo contrario, cuando se elimina la réplica, todos los informes asociados a ella se limpian del almacén. Esto significa que los escritores de guardián también necesitan pensar en la duración de la entidad y del informe. Debe quedar claro cuándo se debe limpiar un informe desde un almacén (por ejemplo, cuándo se deja de aplicar un error en una entidad).

Veamos un ejemplo para poner en común los puntos anteriores. Considere una aplicación de Service Fabric compuesta de un servicio con estado maestro persistente y servicios sin estado esclavos implementados en todos los nodos (un tipo de servicio de esclavo para un tipo de tarea). El patrón tiene una cola de procesamiento con comandos a ejecutar por esclavos. Los esclavos ejecutan las solicitudes entrantes y devuelven reconocimientos. Una condición que puede supervisarse es la longitud de la cola de procesamiento maestra. Si la longitud de la cola maestra alcanza un umbral, notifique una advertencia, ya que ello significa que los esclavos no pueden controlar la carga. Si la cola alcanza la longitud máxima y se quitan los comandos, informe de un error, ya que no se puede recuperar el servicio. Los informes pueden estar en la propiedad "QueueStatus". El guardián reside dentro del servicio y se envía periódicamente en la réplica principal maestra. El TTL es de 2 minutos y se envía periódicamente cada 30 segundos. Si el principal deja de funcionar, el informe se limpia automáticamente del almacén. Si la réplica de servicio está activa pero interbloqueada o tiene otros problemas, el informe caducará en el almacén de estado y la entidad se evaluará con error.

Otra condición que se pueden supervisar es el tiempo de ejecución de la tarea. El maestro distribuye las tareas a los esclavos según el tipo de tarea. Dependiendo del diseño, el patrón de podría sondear el estado de las tareas de los esclavos o podría esperar que los esclavos devuelvan ACK cuando hayan terminado. En el segundo caso, se debe tener cuidado de detectar situaciones en las que los esclavos mueren o se pierden los mensajes. Una opción es que el maestro envíe una solicitud de ping al mismo esclavo, que devuelve el estado. Si no se recibe ningún estado, considere la posibilidad de que se haya producido un error y de volver a programar la tarea. Eso asume que las tareas son idempotentes. Podemos traducir la condición supervisada como advertencia si la tarea no se realiza en un determinado tiempo t1 (por ejemplo, 10 minutos); y de error si no se completa la tarea a tiempo t2 (por ejemplo, 20 minutos). Estos informes pueden realizarse de varias maneras:

- La réplica principal maestra informa periódicamente sobre sí misma. Podemos tener una propiedad para todas las tareas pendientes en la cola: si al menos una tarea tarda más, informe sobre el error o la advertencia "PendingTasks" de la propiedad, según corresponda. Si no hay ninguna tarea pendiente o se acaban de iniciar todas, informe de que está todo correcto. Las tareas se conservan, por lo que si principal deja de funcionar, el nuevo elemento principal promocionado puede continuar generando informes correctamente.

- Otro proceso guardián (en la nube o externo) comprueba las tareas (desde fuera, en función del resultado de la tarea deseado) para ver si se completan. Si no respetan los umbrales, informe sobre el servicio principal. Informe sobre cada tarea e incluya el identificador de tareas (por ejemplo, PendingTask + taskid). Informe solo sobre estados incorrectos. Establezca TTL en unos minutos y marque los informes para ser eliminados cuando caduquen para garantizar la limpieza.

- El esclavo que está ejecutando una tarea está informando de si tarda más tiempo del esperado en ejecutarse. Informa sobre la instancia de servicio en la propiedad "PendingTasks". Esto indica la instancia de servicio que tiene problemas, pero no captura la situación en la que se bloquea la instancia. Los informes se limpian en ese momento. Podría informar acerca del servicio esclavo; si el esclavo completa la tarea, la instancia esclava borra el informe del almacén. Esto no captura la situación en la que se pierde el mensaje de confirmación y la tarea no se considera terminada desde el punto de vista del maestro.

Sin embargo, los informes se realizarán en los casos que se han descrito anteriormente, se capturarán en el estado de la aplicación cuando se evalúe el estado.

## Informar periódicamente frente a en transiciones
Con el modelo de informes de estado, los guardianes pueden enviar informes periódicamente o en las transiciones. La manera recomendada es periódicamente, ya que el código es mucho más sencillo y, por lo tanto, menos propenso a errores. Los guardianes deben esforzarse por ser los más simples posible para evitar errores que desencadenen informes incorrectos. Un informe de mal estado incorrecto afectará a los escenarios y a la evaluación del estado en función del estado, como las actualizaciones. Los informes de buen estados incorrectos ocultan problemas en el clúster, lo cual no es deseable.

Para los informes periódicos, el guardián puede implementarse con un temporizador. En la devolución de llamada del temporizador, el guardián puede comprobar el estado y enviar un informe según el estado actual. No es necesario comprobar qué informe se ha enviado anteriormente ni realizar ninguna optimización en términos de mensajería. El cliente de estado tiene una lógica de procesamiento por lotes para ayudarle con eso. Siempre que el cliente de estado se mantenga activo, volverá a intentarlo internamente hasta que el informe sea confirmado por parte del almacén de estado o el guardián genere un informe más reciente con la misma entidad, propiedad y origen.

La generación de informes sobre la transición requiere un tratamiento del estado cuidadoso. El guardián supervisa algunas condiciones y solo informa cuando cambian las condiciones. El lado positivo es que se necesitan menos informes. El lado negativo es que la lógica del guardián es compleja. Las condiciones o los informes deben mantenerse para poder ser inspeccionados para determinar los cambios de estado. Con la conmutación por error, se debe tener cuidado de enviar un informe que es posible que no se haya enviado anteriormente (que esté en cola, pero que no se han enviado todavía al almacén de estado). El número de secuencia debe ser siempre creciente o los informes se rechazarán debido a la obsolescencia. En los casos poco frecuentes en los que se produce la pérdida de datos, puede que resulte necesaria efectuar la sincronización entre el estado del generador de informes y el estado del almacén de estado.

## Implementación de la generación de informes de estado
Una vez que los detalles de entidad e informes son claros, el envío de informes de estado puede realizarse a través de API, Powershell o REST.

### API
Para informar a través de API, los usuarios necesitan crear un informe de estado específico del tipo de entidad del que desean informar y, a continuación, facilitárselo a un cliente de estado.

El ejemplo siguiente muestra informes periódico de un guardián desde dentro del clúster. El guardián comprueba si se puede acceder a un recurso externo desde dentro de un nodo. El recurso es necesario para un manifiesto de servicio de dentro de la aplicación. Si el recurso no está disponible, los demás servicios de dentro de la aplicación pueden funcionar correctamente. Por lo tanto, el informe se envía en la entidad de paquete de servicio implementada de forma periódica, cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
Los usuarios pueden enviar informes de estado mediante Send-ServiceFabric*EntityType*HealthReport.

El ejemplo siguiente muestra informes periódicos sobre valores de la CPU en un nodo. Los informes se deben enviar cada 30 segundos, y tienen un TTL de 2 minutos. Si caducan, significa que el generador de informes tiene problemas, por lo que el nodo se ha evaluado por error. Cuando la CPU está por encima de un umbral, el informe presenta una advertencia de estado de mantenimiento, si la CPU está por encima del umbral durante un tiempo superior al configurado, se notifica como Error. De lo contrario, el generador de informes envía Aceptar.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

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

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

El ejemplo siguiente muestra una advertencia transitoria en una réplica. Primero obtiene el Id. de partición y el id. de réplica del servicio en el que está interesado y, a continuación, envía un informe de PowershellWatcher sobre la propiedad ResourceDependency. El informe solo es de interés durante 2 minutos y se quitará automáticamente del almacén.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Pasos siguientes

Según los datos del estado, los escritores del servicio y los administradores de clúster/aplicación pueden pensar en maneras de utilizar la información. Por ejemplo, pueden establecer alertas basadas en el estado de mantenimiento para detectar problemas graves antes de provocar interrupciones. Pueden configurar sistemas de reparación automática para solucionar problemas de forma automática.

[Introducción a la supervisión del estado de Service Fabric](service-fabric-health-introduction.md)

[Cómo ver los informes de mantenimiento de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Supervisión y diagnóstico de servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->