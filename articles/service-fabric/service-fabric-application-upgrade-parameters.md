
<properties
   pageTitle="Actualización de la aplicación: parámetros de actualización | Microsoft Azure"
   description="Describe los parámetros relacionados con la actualización de una aplicación de Service Fabric, incluida la realización de comprobaciones de estado y directivas para deshacer automáticamente la actualización."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Parámetros de actualización de la aplicación

En este artículo se describen los distintos parámetros que se aplican durante la actualización de una aplicación de Service Fabric. Los parámetros incluyen el nombre y la versión de la aplicación, y son mecanismos que controlan los tiempos de expiración y las comprobaciones de estado que se aplican durante la actualización y especifican las directivas que deben aplicarse cuando se produce un error en una actualización.


<br>

| Parámetro | Descripción |
| --- | --- |
| ApplicationName | Nombre de la aplicación que se actualiza. Ejemplos: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | La versión del tipo de aplicación al que se dirige la actualización. |
| FailureAction | La propiedad FailureAction describe la acción que realizará Service Fabric si se produce un error en la actualización. Es posible que la aplicación se revierta a la última versión anterior a la actualización (modo de reversión) o que detenga la actualización de la aplicación en el dominio de actualización actual y cambie el modo de actualización a manual. Los valores permitidos son los modos de reversión y manual. |
| HealthCheckWaitDurationSec | El tiempo de espera (en segundos) una vez completada la actualización en el dominio de actualización antes de que Service Fabric evalúe el estado de la aplicación. Esta duración también puede considerarse como el tiempo que debe tardar en ejecutarse una aplicación antes de que se pueda considerar correcta. Si se supera la comprobación de estado, el proceso de actualización pasa al siguiente dominio de actualización. Si se produce un error en la comprobación de estado, Service Fabric espera un intervalo (UpgradeHealthCheckInterval) antes de reintentar la comprobación de estado de nuevo hasta que se alcance HealthCheckRetryTimeout. El valor predeterminado y recomendado es 0 segundos. |
| HealthCheckRetryTimeoutSec | La duración (en segundos) empleada por Service Fabric para continuar realizando la evaluación de estado antes de declarar que se ha producido un error en la actualización. El valor predeterminado es 600 segundos. Esta duración se inicia tras alcanzarse HealthCheckWaitDuration. Dentro de HealthCheckRetryTimeout, Service Fabric puede realizar varias comprobaciones de estado del estado de la aplicación. El valor predeterminado es 10 minutos y se recomienda su correcta personalización para su aplicación. |
| HealthCheckStableDurationSec | La duración (en segundos) de espera para comprobar la estabilidad de la aplicación antes de pasar al siguiente dominio de actualización o completar la actualización. Esta duración de espera se emplea para evitar cambios de estado no detectados justo después de la realización de la comprobación de estado. El valor predeterminado es 0 segundos y se recomienda su correcta personalización para su aplicación. |
| UpgradeDomainTimeoutSec | Tiempo máximo (en segundos) para actualizar un solo dominio de actualización. Si este tiempo de expiración llega a su fin, la actualización se detendrá y realizará la acción especificada por UpgradeFailureAction. El valor predeterminado es Never (infinito) y se recomienda su correcta personalización para su aplicación. |
| UpgradeTimeout | Un tiempo de ejecución (en segundos) que se aplica a toda la actualización. Si este tiempo de expiración llega a su fin, la actualización se detendrá y UpgradeFailureAction se desencadenará. El valor predeterminado es Never (infinito) y se recomienda su correcta personalización para su aplicación. |
| UpgradeHealthCheckInterval | Especificada en la sección ClusterManager del _manifiesto_ de _clúster_ (este no se especifica como parte del cmdlet de actualización), esta configuración determina la frecuencia con que se comprueba el estado de mantenimiento (el valor predeterminado es 60 segundos). |






<br>
## Evaluación de estado del servicio durante la actualización de la aplicación

<br> Los criterios de evaluación de estado son opcionales. Si no se especifican los criterios de evaluación de estado al iniciar una actualización, Service Fabric usará las directivas de mantenimiento de aplicaciones especificadas en ApplicationManifest.xml de la instancia de aplicación que se actualiza.


<br>

| Parámetro | Descripción |
| --- | --- |
| ConsiderWarningAsError | El valor predeterminado es False. Trate los eventos de estado de advertencia para la aplicación como error al evaluar el estado de la aplicación durante la actualización. De forma predeterminada, Service Fabric no evalúa los eventos de estado de advertencia en los que se produce un fallo (error), por lo que puede continuar la actualización incluso si hay eventos de advertencia. |
| MaxPercentUnhealthyDeployedApplications | El valor predeterminado y recomendado es 0. Especifique el número máximo de aplicaciones implementadas (consulte la [sección Estado](service-fabric-health-introduction.md)) que puede ser incorrecto antes de que la aplicación se considere incorrecta y haga que se produzca un error en la actualización. Este es el estado de la aplicación empaquetada que se encuentra en el nodo, siendo útil para detectar problemas de forma inmediata durante la actualización y donde el paquete de aplicación implementado en el nodo es incorrecto (bloqueo, etc.). En un caso típico, la carga de las réplicas de la aplicación se equilibrará en el otro nodo, haciendo que la aplicación figure como correcta, lo que permitirá la continuación de la actualización. Al especificar un estado MaxPercentUnhealthyDeployedApplications estricto, Service Fabric puede detectar un problema con el paquete de aplicación de forma rápida y hacer que se produzca un error en la actualización rápida. |
| MaxPercentUnhealthyServices | El valor predeterminado y recomendado es 0. Especificar el número máximo de servicios en la instancia de aplicación puede ser incorrecto antes de que la aplicación se considere incorrecta y haga que se produzca un error en la actualización. |
| MaxPercentUnhealthyPartitionsPerService | El valor predeterminado y recomendado es 0. Especificar el número máximo de particiones en un servicio puede ser incorrecto antes de que el servicio se considere incorrecto. |
| MaxPercentUnhealthyReplicasPerPartition | El valor predeterminado y recomendado es 0. Especifique el número máximo de réplicas en la partición que puede ser incorrecto antes de que esta se considere incorrecta. |
| UpgradeReplicaSetCheckTimeout | Servicio sin estado: dentro de un único dominio de actualización, Service Fabric intenta garantizar la disponibilidad de instancias adicionales del servicio. Si el recuento de instancias de destino es superior a uno, Service Fabric espera la disponibilidad de más de una instancia, hasta un valor de tiempo de expiración máximo (especificado por la propiedad UpgradeReplicaSetCheckTimeout). Si se agota el tiempo de expiración, Service Fabric continúa con la actualización, independientemente del número de instancias de servicio. Si el recuento de instancias de destino es uno, Service Fabric no espera e inmediatamente continúa con la actualización. Servicio con estado: dentro de un único dominio de actualización, Service Fabric intenta garantizar que el conjunto de réplicas tenga un quórum. Service Fabric espera la disponibilidad de un quórum, hasta un valor de tiempo de expiración máximo (especificado por la propiedad UpgradeReplicaSetCheckTimeout). Si se agota el tiempo de expiración, Service Fabric continúa con la actualización, independientemente del quórum. Esta configuración se establece como Never (infinito) durante su aplicación (cuando la actualización continúa del modo esperado) y como 900 segundos durante su reversión (cuando la actualización genera errores y se revierte). |
| ForceRestart | Si actualiza un paquete de datos o configuración, sin actualizar el código de servicio, Service Fabric no reiniciará el servicio a menos que la propiedad ForceRestart se establezca en true como parte de la llamada API. Una vez completada la actualización, Service Fabric notifica al servicio que un nuevo paquete de configuración o datos está disponible. El servicio es responsable de aplicar los cambios. Si es necesario, el servicio puede reiniciarse automáticamente. |



<br> <br> Los criterios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService y MaxPercentUnhealthyReplicasPerPartition se pueden especificar por tipo de servicio para una instancia de aplicación. Así se garantiza que una aplicación que contiene tipos de servicios diferentes pueda tener directivas de evaluación distintas para cada uno de los tipos de servicio. Por ejemplo, un tipo de servicio de pasarela sin estado puede tener otro MaxPercentUnhealthyPartitionsPerService distinto de un tipo de servicio del motor con estado para una instancia de aplicación determinada.

## Pasos siguientes


[Tutorial de actualización](service-fabric-application-upgrade-tutorial.md)

[Temas avanzados](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de una actualización de aplicación ](service-fabric-application-upgrade-troubleshooting.md)

[Serialización de datos](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=Nov15_HO2-->