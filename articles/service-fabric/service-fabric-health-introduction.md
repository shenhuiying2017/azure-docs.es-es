<properties
   pageTitle="Introducción a la supervisión del estado de Service Fabric"
   description="En este artículo se describe el modelo de supervisión del estado de Service Fabric, incluidas las entidades de mantenimiento, los informes y la evaluación."
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
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Introducción a la supervisión del estado de Service Fabric
Service Fabric presenta un modelo de estado que ofrece informes y evaluación de estado enriquecido, flexible y extensible. Esto incluye supervisión casi en tiempo real del estado del clúster y de los servicios que se ejecutan en él. Puede obtener con facilidad la información de estado y realizar acciones para corregir posibles problemas antes de que aparezcan en cascada y causen interrupciones masivas. El modelo típico es que los servicios envían informes basados en la vista local y se agrega la información para proporcionar una vista de nivel de clúster general.

Los componentes de Service Fabric usan este modelo de estado para informar de su estado actual. Además, puede usar el mismo mecanismo para informar del estado de sus aplicaciones. La calidad y riqueza de los informes de mantenimiento específicos para sus condiciones personalizadas determinará la facilidad con la que podrá detectar y corregir los problemas de la aplicación en ejecución.

> [AZURE.NOTE]Comenzamos el subsistema de estado como una necesidad para las actualizaciones supervisadas. Service Fabric ofrece actualizaciones supervisadas que saben cómo actualizar un clúster o una aplicación sin tiempo de inactividad, como una intervención del usuario mínima o ninguna intervención, y con disponibilidad completa de clúster y aplicación. Para ello, la actualización comprueba el estado en función de las directivas de actualización configuradas y permite que la actualización continúe solo cuando el estado respeta los umbrales deseados. De lo contrario, la actualización se revierte automáticamente o se pausa para ofrecer a los administradores una oportunidad de solucionar los problemas. Para obtener más información sobre las actualizaciones de aplicaciones, vea [este artículo](service-fabric-application-upgrade.md).

## Almacén de estados
El almacén de estados conserva la información relacionada con estados sobre entidades del clúster para una evaluación y recuperación sencillas. Se implementa como un servicio con estado persistente de Service Fabric para garantizar alta disponibilidad y escalabilidad. Forma parte de la aplicación fabric:/System y está disponible tan pronto como el clúster está en funcionamiento.

## Jerarquía y entidades de mantenimiento
Las entidades de mantenimiento se organizan en una jerarquía lógica que captura las interacciones y las dependencias entre diferentes entidades. Las entidades y la jerarquía se generan automáticamente por el almacén de estados en función de los informes que se reciben de los componentes de Service Fabric.

Las entidades de mantenimiento reflejan las entidades de Service Fabric (por ejemplo, la entidad de la aplicación de estado coincide con una instancia de la aplicación implementada en el clúster, la entidad del nodo de estado coincide con un nodo de clúster de Service Fabric). La jerarquía de estado captura las interacciones de las entidades del sistema y es la base para la evaluación de estado avanzada. Puede aprender los conceptos clave de Service Fabric en la [Información general de Service Fabric](service-fabric-technical-overview.md). Para obtener más información sobre la aplicación, vaya al [modelo de aplicación de Service Fabric](service-fabric-application-model.md).

La jerarquía y las entidades de mantenimiento permite los informes, la depuración y la supervisión efectivos del clúster y las aplicaciones. El modelo de estado permite una representación **granular** y precisa del mantenimiento de las numerosas piezas móviles del clúster.

![Entidades de mantenimiento.][1] Las entidades de mantenimiento, organizadas en una jerarquía basada en relaciones de elementos principales y secundarios.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Las entidades de mantenimiento son:

- **Clúster**. Representa el estado de un clúster de Service Fabric. Los informes de mantenimiento del clúster describen las condiciones que afectan a todo el clúster y no se pueden restringir a uno o más elementos secundarios incorrectos. Ejemplo: cerebro dividido del clúster debido a problemas de creación de particiones de red o comunicación.

- **Nodo**. Representa el estado de un nodo de Service Fabric. Los informes de mantenimiento del nodo describen las condiciones que afectan a la funcionalidad del nodo y normalmente afectan a todas las entidades implementadas que se ejecutan en él. Ejemplo: el nodo se encuentra fuera del espacio del disco (u otra propiedad ancha de máquina como la memoria, las conexiones, etc.) o el nodo está inactivo. La entidad del nodo se identifica por el nombre del nodo (cadena).

- **Aplicación**. Representa el estado de una instancia de aplicación que se ejecuta en el clúster. Los informes de mantenimiento de aplicación describen las condiciones que afectan al estado general de la aplicación y no se pueden restringir a sus elementos secundarios individuales (servicios o aplicaciones implementadas). Ejemplo: la interacción descentralizada entre los diferentes servicios de la aplicación. La entidad de la aplicación se identifica mediante el nombre de la aplicación (URI).

- **Servicio**. Representa el estado de un servicio que se ejecuta en el clúster. Los informes de mantenimiento de servicio describen las condiciones que afectan al estado general del servicio y no se pueden restringir a una partición o una réplica. Ejemplo: una configuración del servicio (como un puerto o recurso de compartido de archivo externo) que está causando problemas para todas las particiones. La entidad de servicio se identifica por el nombre del servicio (URI).

- **Partición**. Representa el estado de una partición de servicio. Los informes de mantenimiento de la partición describen las condiciones que afectan al conjunto completo de réplicas. Ejemplo: el número de réplicas es inferior al recuento de destino o la partición tiene una pérdida de cuórum. La entidad de la partición se identifica por el id. de partición (GUID).

- **Réplica**. Representa el estado de una réplica de servicio con estado o una instancia de servicio sin estado. Se trata del guardián de unidad más pequeño y los componentes del sistema puede informar para una aplicación. Ejemplo: para los servicios con estado, la réplica principal puede informar si no puede replicar operaciones a elementos secundarios o si la replicación no sigue el ritmo esperado. Una instancia sin estado puede informar de si se está quedando sin recursos o si tiene problemas de conectividad. La entidad de réplica se identifica por el id. de partición (Guid) y el id. de réplica o instancia (largo).

- **DeployedApplication**. Representa el estado de a *aplicación que se ejecuta en un nodo*. Los informes de mantenimiento de la aplicación implementada describen las condiciones específicas de la aplicación en el nodo que no se pueden restringir a paquetes de servicio implementados en el mismo nodo. Ejemplo: no se puede descargar el paquete de aplicación en ese nodo o hay un problema al configurar las entidades de seguridad de la aplicación en el nodo. La aplicación implementada se identifica por el nombre de la aplicación (URI) y el nombre del nodo (cadena).

- **DeployedServicePackage**. Representa el estado de un paquete de servicio de una aplicación que se ejecuta en un nodo del clúster. Describe las condiciones específicas de un paquete de servicio que no afectan a los demás paquetes de servicio del mismo nodo para la misma aplicación. Ejemplo: un paquete de código del paquete de servicio no se puede iniciar o no se puede leer el paquete de configuración. El paquete de servicio implementado se identifica por el nombre de la aplicación (URI), el nombre de nodo (cadena) y el nombre de manifiesto de servicio (cadena).

La granularidad del modelo de mantenimiento facilita la detección y corrección de problemas. Por ejemplo, si un servicio no responde, es posible informar de que la instancia de aplicación es incorrecto; sin embargo, esto no es lo idóneo porque puede que el problema no afecte a todos los servicios dentro de esa aplicación. El informe se debe aplicar en el servicio incorrecto, o bien, si más información señala a una partición secundaria concreta, en esa partición. Los datos aparecerán automáticamente por la jerarquía: una partición incorrecta se hará visible en los niveles de servicio y de aplicación. Esto le ayudará a identificar y resolver la causa raíz de los problemas con mayor rapidez.

La jerarquía de estado se compone de relaciones de elementos principales y secundarios. El clúster se compone de nodos y aplicaciones; las aplicaciones tienen servicios y aplicaciones implementadas; las aplicaciones implementadas tienen paquetes de servicio implementados. Los servicios tienen particiones y cada partición tiene una o más réplicas. Hay una relación especial entre los nodos y las entidades implementadas. Si un nodo es incorrecto según lo notifica su componente de sistema de autoridad (servicio del administrador de conmutación por error), afectará a las aplicaciones implementadas, a los paquetes de servicios y a las aplicaciones implementadas en él.

La jerarquía de estado representa el estado más reciente del sistema basándose en los informes de mantenimiento más recientes, que es información casi en tiempo real. Los guardianes internos y externos pueden informar de las mismas entidades según la lógica específica de la aplicación o las condiciones supervisadas personalizadas. Los informes de usuario coexisten con los informes del sistema.

Invertir tiempo en la planeación de cómo informar y responder al estado mientras se diseña el servicio facilita la depuración, la supervisión y el posterior manejo de servicios en la nube de mayor tamaño.

## Estados de mantenimiento
Service Fabric usa tres estados de mantenimiento para describir si una entidad es correcta o no: Correcto, Advertencia y Error. Cualquier informe enviado al almacén de estados debe especificar uno de estos estados. El resultado de la evaluación de estado es uno de estos estados.

Los estados posibles de mantenimiento son:

- Correcto: la entidad es correcta. No hay ningún problema conocido notificado en ella o en sus elementos secundarios (en los casos aplicables).

- Advertencia: la entidad tiene algunos problemas pero todavía no es incorrecta (por ejemplo, el retraso inesperado que no causa ningún problema funcional). En algunos casos, la condición de advertencia puede corregirse por sí sola sin ninguna intervención especial y resulta útil para proporcionar visibilidad en lo que está ocurriendo. En otros casos, la condición de advertencia puede dar lugar a un problema grave sin intervención del usuario.

- Error: la entidad es incorrecta. Se debe llevar a cabo una acción para corregir el estado de la entidad, ya que no puede funcionar correctamente.

- Desconocido: la entidad no existe en el almacén de estados. Este resultado se puede obtener a partir de consultas distribuidas como obtener las aplicaciones y los nodos de Service Fabric. Estas consultas combinan los resultados de varios componentes del sistema. Si otro componente del sistema tiene una entidad que no ha alcanzado el almacén de estados todavía o que se ha limpiado del almacén de estados, la consulta combinada rellenará el resultado de mantenimiento con el estado de mantenimiento 'Desconocido'.

## Directivas de mantenimiento
El almacén de estados se aplica a las directivas de mantenimiento para determinar si una entidad es correcta en función de sus informes y sus elementos secundarios.

> [AZURE.NOTE]Las directivas de mantenimiento se pueden especificar en el manifiesto de clúster (para la evaluación de estado de los clústeres y los nodos) o en el manifiesto de aplicación (para la evaluación de la aplicación y cualquiera de sus elementos secundarios). Las solicitudes de evaluación de estado también se pueden pasar en directivas de evaluación de estado personalizadas, que solo se usarán para dicha evaluación.

De forma predeterminada, Service Fabric aplica reglas estrictas (todo debe ser correcto) para la relación jerárquica de elementos principales y secundarios; siempre que uno de los elementos secundarios tenga un evento incorrecto, el elemento secundario se considera incorrecto.

### Directiva de mantenimiento de clústeres
La directiva de mantenimiento de clústeres se usa para evaluar el estado del mantenimiento del clúster y los estados de mantenimiento del nodo. Se puede definir en el manifiesto de clúster. Si no está presente, toma como valor predeterminado la directiva predeterminada (0 errores tolerados). Contiene:

- **ConsiderWarningAsError**. Especifica si se deben tratar los informes de mantenimiento de advertencia como errores durante la evaluación de estado. Valor predeterminado: false.

- **MaxPercentUnhealthyApplications**. El porcentaje máximo tolerado de aplicaciones que pueden ser incorrectas antes de que el clúster se considere erróneo.

- **MaxPercentUnhealthyNodes**. El porcentaje máximo tolerado de nodos que pueden ser incorrectos antes de que el clúster se considera erróneo. En clústeres de gran tamaño, siempre habrá nodos inactivos o excluidos para reparaciones, por lo que este porcentaje debe configurarse para tolerar ese hecho.

Lo siguiente es un extracto de un manifiesto de clúster:

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### Directiva de mantenimiento de la aplicación
En la directiva de mantenimiento de la aplicación se describe cómo se realiza la evaluación de la agregación de los estados de los elementos secundarios y los eventos para la aplicación y sus elementos secundarios. Se puede definir en el manifiesto de aplicación, ApplicationManifest.xml, en el paquete de aplicación. Si no se especifica, Service Fabric supone que la entidad es incorrecta si tiene un informe de mantenimiento o un elemento secundario en el estado de mantenimiento de Advertencia o Error. Las directivas configurables son:

- **ConsiderWarningAsError**. Especifica si se deben tratar los informes de mantenimiento de advertencia como errores durante la evaluación de estado. Valor predeterminado: false.

- **MaxPercentUnhealthyDeployedApplications**. El porcentaje máximo tolerado de aplicaciones implementadas que pueden ser incorrectas antes de que la aplicación se considere erróneo. Esto se calcula dividiendo el número de aplicaciones implementadas incorrectas sobre el número de nodos en que las aplicaciones están implementadas actualmente en el clúster. El cálculo se redondea hacia arriba para tolerar un error en un número reducido de nodos. Valor predeterminado: 0 %.

- **DefaultServiceTypeHealthPolicy**. Especifica la directiva de mantenimiento del tipo de servicio predeterminada, que reemplazará la directiva de mantenimiento predeterminada para todos los tipos de servicio de la aplicación.

- **ServiceTypeHealthPolicyMap**. Asigne con directivas de mantenimiento de servicio por tipo de servicio, que reemplazan la directiva de mantenimiento del tipo de servicio predeterminado para los tipos de servicio especificados. Por ejemplo, en una aplicación que contiene un tipo de servicio de pasarela sin estado y un tipo de servicio del motor con estado, la directiva de mantenimiento para el servicio con estado y sin estado puede configurarse de forma diferente. La especificación de la directiva por tipos de servicio permite un control más pormenorizado del estado del servicio.

### Directiva de mantenimiento del tipo de servicio
La directiva de mantenimiento del tipo de servicio especifica cómo evaluar y agregar elementos secundarios del servicio. Contiene:

- **MaxPercentUnhealthyPartitionsPerService**. Porcentaje máximo tolerado de particiones incorrectas antes de que un servicio se considere incorrecto. Valor predeterminado: 0 %.

- **MaxPercentUnhealthyReplicasPerPartition**. Porcentaje máximo tolerado de réplicas incorrectas antes de que una partición se considere incorrecta. Valor predeterminado: 0 %.

- **MaxPercentUnhealthyServices**. Porcentaje máximo tolerado de servicios incorrectos antes de que la aplicación se considere incorrecta. Valor predeterminado: 0 %

Lo siguiente es un extracto de un manifiesto de aplicación:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Evaluación de estado
Los usuarios o servicios automatizados pueden evaluar el estado de cualquier entidad en cualquier momento en el tiempo. Para evaluar el estado de una entidad, el almacén de estados agrega todos los informes de mantenimiento en la entidad y evalúa todos sus elementos secundarios (si procede). El algoritmo de agregación de estado usa las directivas de mantenimiento que especifican cómo evaluar informes de mantenimiento, además de cómo agregar estados de mantenimiento de elementos secundarios (en los casos aplicables).

### Agregación de informes de mantenimiento
Una entidad puede tener varios informes de mantenimiento enviados por informadores diferentes (componentes del sistema o guardianes) en diferentes propiedades. La agregación usa las directivas de mantenimiento asociados, en concreto, el miembro ConsiderWarningAsError de aplicación o la directiva de mantenimiento del clúster, que especifica cómo evaluar las advertencias.

El estado de mantenimiento agregado se desencadena por los **peores** informes de mantenimiento sobre la entidad. Si hay al menos un informe de mantenimiento de Error, el estado de mantenimiento agregado es Error.

![Agregación de informes de estado con informe de errores.][2]

El informe de mantenimiento de error desencadena la entidad de mantenimiento para que esté en el estado de Error.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Si no hay ningún informe de Error, y una o más advertencias, el estado de mantenimiento agregado es Advertencia o Error, en función de la marca de la directiva ConsiderWarningAsError.

![Agregación del informe de estado con informe de advertencias y ConsiderWarningAsError falso.][3]

Agregación del informe de mantenimiento con informe de advertencia y falso de ConsiderWarningAsError (valor predeterminado).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Agregación de estado de elementos secundarios
El estado de mantenimiento agregado de una entidad refleja los estados de mantenimiento de los elementos secundarios (en los casos aplicables). El algoritmo para la agregación de los estados de mantenimiento de los elementos secundarios usa las directivas de mantenimiento aplicables según el tipo de entidad.

![Agregación de estado de elementos secundarios.][4]

Agregación de elementos secundarios basada en las directivas de mantenimiento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Tras evaluar todos los elementos secundarios, el almacén de estados agrega los estados de mantenimiento en función del porcentaje máximo configurado tomado de la directiva en función del tipo de entidad y elemento secundario.

- Si todos los elementos secundarios tienen estados Correcto, el estado de mantenimiento agregado de los elementos secundarios es correcto.

- Si los elementos secundarios tienen los estados Correcto y Advertencia, el estado de mantenimiento agregado de los elementos secundarios es Advertencia.

- Si hay elementos secundarios con estados de Error que no respetan el porcentaje máximo permitido de los elementos secundarios incorrectos, el estado de mantenimiento agregado es error.

- Si los elementos secundarios con estados de Error respetan el porcentaje máximo permitido de los elementos secundarios incorrectos, el estado de mantenimiento agregado es Advertencia.

## Informes de mantenimiento
Los componentes del sistema y guardianes internos o externos pueden informar con las entidades de Service Fabric. El *informadores* realizan una determinación **local** del estado de la entidad supervisada según algunas condiciones que están supervisando. No es necesario mirar ningún estado global ni agregar datos. Esto no es lo que se desea, ya que convertiría a los informadores en organismos complejos que deben examinar muchas cuestiones para deducir qué información enviará.

Para enviar los datos de estado al almacén de estados, los informadores necesitan identificar la entidad afectada y crear un informe de mantenimiento. Luego, el informe se podrá enviar mediante API con FabricClient.HealthManager.ReportHealth, a través de Powershell o a través de REST.

### Informes de mantenimiento
Los informes de mantenimiento para cada una de las entidades del clúster contienen la siguiente información:

- SourceId. Cadena que identifica de forma única el informador del evento de estado.

- Identificador de la entidad. Identifica la entidad en la que se aplica el informe. Es diferente según el [tipo de entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Clúster: ninguno

  - Nodo: nombre de nodo (cadena).

  - Aplicación: nombre de aplicación (URI). Representa el nombre de la instancia de aplicación implementada en el clúster.

  - Servicio: nombre de servicio (URI). Representa el nombre de la instancia de servicio implementada en el clúster.

  - Partición: id. de partición (GUID). Representa el identificador único de la partición.

  - Réplica: el id. de réplica de servicio con estado o el id. de instancia de servicio sin estado (Int64).

  - DeployedApplication: nombre de la aplicación (URI) y nombre del nodo (cadena).

  - DeployedServicePackage: nombre de la aplicación (URI), nombre del nodo (cadena) y nombre de manifiesto de servicio (cadena).

- Propiedad. Un *cadena* (no una enumeración fija) que permite al informador clasificar el evento de estado para una propiedad específica de la entidad. Por ejemplo, el informador A puede informar del estado en la propiedad de "almacenamiento" de Node01 y el informador B puede informar del estado en la propiedad de "conectividad" de Node01. Ambos informes se tratan como eventos de estado independientes en el almacén de estados para la entidad Node01.

- Descripción. Una cadena que permite al informador ofrecer información detallada sobre el evento de estado. SourceId, Property y HealthState deben describir el informe completamente. La descripción agrega información de lenguaje natural sobre el informe para facilitar su comprensión a los usuarios y administradores.

- HealthState. Una [enumeración](service-fabric-health-introduction.md#health-states) que describe el estado de mantenimiento del informe. Los valores aceptados son Correcto, Advertencia y Error.

- TimeToLive. Intervalo de tiempo que indica cuánto tiempo es válido el informe de mantenimiento. Combinado con RemoveWhenExpired, permite a HealthStore saber cómo evaluar los eventos expirados. De forma predeterminada, el valor es infinito y el informe es válido para siempre.

- RemoveWhenExpired. Valor booleano. Si se establece en true, el informe de mantenimiento expirado se elimina automáticamente del almacén de estados y no afecta a la evaluación de estado de la entidad. Se usa cuando el informe es válido durante un período de tiempo solo y el informador no necesita vaciarlo de manera explícita. También sirve para eliminar informes del almacén de estados. Por ejemplo, se cambia un guardián y deja de enviar informes con la propiedad y el origen anterior. Por tanto, puede enviar un informe con TTL pequeño y RemoveWhenExpired para borrar cualquier estado anterior del almacén de estados. Si se establece en false, el informe expirado se trata como un error en la evaluación de estado. Señala al almacén de estados que el origen debería notificar periódicamente sobre esta propiedad; de no ser así, debe haber algún problema con el guardián. El estado del guardián se captura considerando el evento como error.

- SequenceNumber. Un entero positivo que deba ir siempre en aumento, ya que representa el orden de los informes. Se usa por el almacén de estado para detectar informes obsoletos, recibidos tarde debido a retrasos en la red u otros problemas. Los informes se rechazan si el número de secuencia es menor o igual que la más reciente aplicada para la misma entidad, origen y propiedad. El número de secuencia se genera automáticamente si no se especifica. Solo es necesario colocar el número de secuencia al informar sobre las transiciones de estado: el origen necesita recordar qué informes envió y conservar la información para recuperación en la conmutación por error.

SourceId, el identificador de la entidad, Property y HealthState son necesarios para cada informe de mantenimiento. No se permite que la cadena de SourceId empiece por el prefijo "System.", que se reserva para los informes del sistema. Para la misma entidad, solo hay un informe para el mismo origen y propiedad; si se generan varios informes para el mismo origen y propiedad, se invalidan entre sí, en el lado del cliente de estado (si se procesan por lotes) o en el lado del almacén de estados. El reemplazo se realiza basándose en el número de secuencia: informes más recientes (con el número de secuencia más elevando) reemplazan a informes anteriores.

### Eventos de estado
Internamente, el almacén de estados conserva eventos de estado, que contienen toda la información de los informes más metadatos adicionales, como la hora en que se dio el informe especificado al cliente de estado y la hora en que se modificó en el lado servidor. Los eventos de estado se devuelven por las [consultas de estado](service-fabric-view-entities-aggregated-health.md#health-queries).

Los metadatos agregados contienen:

- SourceUtcTimestamp: la hora en que el informe se dio al cliente de mantenimiento (UTC)

- LastModifiedUtcTimestamp: la hora en que el informe se modificó por última vez en el lado servidor (UTC)

- IsExpired: marca para indicar si el informe expiró en el momento en que el almacén de estados ejecutó la consulta. Se puede expirar un evento solo si RemoveWhenExpired es false; de lo contrario, el evento no se devuelve por la consulta, se quita del almacén.

- LastOkTransitionAt, LastWarningTransitionAt, LastErrorTransitionAt: última hora para las transiciones de Correcto, Advertencia o Error. Estos campos muestran el historial de la transición de los estados de mantenimiento para el evento.

Los campos de la transición de estado se pueden usar para alertas más inteligentes o información de eventos de estado "histórica". Permiten escenarios como:

- Alertar cuando una propiedad ha estado en el estado Advertencia/Error durante más de X minutos. Esto evita la generación de alertas sobre condiciones temporales. Por ejemplo, la alerta si el estado de mantenimiento ha sido Advertencia durante más de 5 minutos se puede traducir en (HealthState == Advertencia y ahora: LastWarningTransitionTime
> 5 minutes).

- Alertar solo sobre las condiciones que han cambiado en los últimos X minutos. Si el estado de un informe es Error desde antes de eso, se puede omitir (porque ya se había señalado anteriormente).

- Si una propiedad está alternando entre Advertencia y Error, determine cuánto tiempo ha sido incorrecta (es decir, el estado no ha sido Correcto). Por ejemplo, la alerta si la propiedad no era correcta durante más de 5 minutos se pueden traducir en: (HealthState != Correcto y ahora - LastOkTransitionTime > 5 minutos).

## Ejemplo: informar y evaluar del estado de la aplicación
En el ejemplo siguiente se envía un informe de estado a través de Powershell en la aplicación denominada fabric:/WordCount desde el MyWatchdog de origen. El informe de estado contiene información acerca de la disponibilidad de la propiedad de estado en un estado de mantenimiento de Error, con TTL infinito. Luego consulta el estado de aplicación, que devolverá el error de estado de mantenimiento agregado y el evento de estado notificado como parte de la lista de eventos de estado.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Uso del modelo de estado
El modelo de estado permite escalar a los servicios en la nube y a la plataforma subyacente de Service Fabric subyacente, dado que la supervisión y la determinación del estado se distribuyen entre los distintos monitores dentro del clúster. Otros sistemas tienen un servicio centralizado único en el nivel de clúster analizando toda la información *potencialmente* útil emitida por los servicios. Esto impide su escalabilidad y no les permite recopilar información muy específica para ayudar a identificar problemas y posibles problemas tan cerca de la causa raíz como sea posible.

El modelo de estado se usa mucho para la supervisión y el diagnóstico, para evaluar el estado del clúster y la aplicación, y para actualizaciones supervisadas. Otros servicios usan los datos de estado para realizar reparaciones automáticas, para generar el historial de mantenimiento de clúster y para emitir alertas en determinadas condiciones.

## Pasos siguientes
[Cómo ver los informes de mantenimiento de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adición de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)

[Cómo supervisar y diagnosticar servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=August15_HO6-->