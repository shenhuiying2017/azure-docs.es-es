---
title: "Inducción de errores controlados con Caos en clústeres de Service Fabric | Microsoft Docs"
description: "Uso de los servicios de inserción de errores y análisis del clúster para administrar Caos en el clúster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: motanv
ms.openlocfilehash: 9475774b99ee6bc01fb43ffc6fcddea025779c05
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Inducción de errores controlados con Caos en clústeres de Service Fabric
Los sistemas distribuidos a gran escala, como las infraestructuras en la nube, por naturaleza, no son confiables. Azure Service Fabric permite a los desarrolladores crear servicios distribuidos confiables en una infraestructura no confiable. Para escribir servicios distribuidos sólidos en una infraestructura no confiable, los desarrolladores necesitan poder probar la estabilidad de sus servicios, mientras que la infraestructura no confiable subyacente pasa por transiciones de estado complicadas debido a los errores.

El [servicio de inserción de errores y análisis de clúster](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (también denominado servicio de análisis de errores) proporciona a los desarrolladores la posibilidad de inducir errores para probar sus servicios. Estos errores simulados de destino, como [reiniciar una partición](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), pueden ayudar a ejercer las transiciones de estado más comunes. Sin embargo, los errores simulados de destino están sesgados por definición y, por consiguiente, pueden perder errores que se muestran solo en una secuencia de transiciones de estado larga, complicada y difícil de predecir. Para una prueba no sesgada, se puede usar Chaos.

Chaos simula errores periódicos intercalados (tanto correctos como incorrectos) en todo el clúster durante períodos prolongados. Un error correcto consta de un conjunto de llamadas API de Service Fabric, por ejemplo, reiniciar un error de réplica es un error correcto porque se trata de un cierre seguido de una apertura en una réplica. Quitar la réplica, mover la réplica principal y mover la réplica secundaria de movimiento son los otros errores correctos que desempeña Chaos. Los errores incorrectos son salidas de procesos, como reiniciar el nodo y reiniciar el paquete de código. 

Una vez haya configurado Chaos con la velocidad y el tipo de errores, puede iniciarlo mediante API de REST, C# o Powershell para empezar a generar errores en el clúster y en los servicios. Puede configurar Chaos para que se ejecute durante un período especificado (por ejemplo, una hora), tras el que se detiene de forma automática, o bien puede llamar a la API de StopChaos (REST, C# o Powershell) para detenerlo en cualquier momento.

> [!NOTE]
> En su forma actual, Chaos solo induce errores seguros, lo que implica que, si no hay errores externos, nunca se producirá una pérdida de cuórum o de datos.
>

Mientras se ejecuta Caos, se producen diferentes eventos que obtienen el estado de la ejecución en ese momento. Por ejemplo, ExecutingFaultsEvent contiene todos los errores que Chaos ha decidido ejecutarse en esa iteración. ValidationFailedEvent contiene los detalles de un error de validación (problemas de estabilidad o de mantenimiento) que se ha encontrado durante la validación del clúster. Puede invocar la API de GetChaosReportAsync (REST, C# o Powershell) para obtener el informe de las ejecuciones de Chaos. Estos eventos se guardan en un [diccionario confiable](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tiene una directiva de truncamiento dictada por dos configuraciones: **MaxStoredChaosEventCount** (el valor predeterminado es 25 000) y **StoredActionCleanupIntervalInSeconds** (el valor predeterminado es 3600). Todas las instancias de *StoredActionCleanupIntervalInSeconds* que Chaos comprueba y todos los eventos de *MaxStoredChaosEventCount*, salvo los más recientes, se purgan del diccionario confiable.

## <a name="faults-induced-in-chaos"></a>Errores inducidos en Caos
Caos genera errores en todo el clúster de Service Fabric y comprime todos los detectados que se ven en meses o años en tan solo unas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pueden pasar desapercibidos. Esto conduce a una mejora considerable en la calidad del código del servicio.

Caos induce errores de las siguientes categorías:

* Reinicio de un nodo
* Reinicio de un paquete de código implementado
* Eliminación de una réplica
* Reinicio de una réplica
* Desplazamiento de una réplica principal (configurable)
* Desplazamiento de una réplica secundaria (configurable)

Caos se ejecuta en varias iteraciones. Cada iteración consta de validaciones de clúster y errores correspondientes al período especificado. Puede configurar el tiempo empleado por el clúster para que la estabilización y la validación sean correctas. Si se detecta un error de validación de clúster, Caos genera y conserva un evento ValidationFailedEvent con la marca de tiempo UTC y los detalles de los errores. Por ejemplo, analice una instancia de Caos que esté establecida para ejecutarse durante una hora con un máximo de tres errores simultáneos. Caos induce tres errores y después valida el estado del clúster. Itera a través del paso anterior hasta que el proceso se detiene expresamente mediante la API StopChaosAsync o cuando transcurre una hora. Si el clúster pasa a ser incorrecto en cualquier iteración (es decir, no se estabiliza o no pasa a ser correcto dentro de la instancia de MaxClusterStabilizationTimeout pasada), Chaos genera una instancia de ValidationFailedEvent. Este evento indica que algo salió mal y que podría necesitar más investigación.

Para obtener los errores que ha provocado Chaos, puede usar la API de GetChaosReport (REST, Powershell o C#). La API obtiene el siguiente segmento del informe de Chaos basado en el token de continuación pasado o en el intervalo de tiempo pasado. Se puede especificar ContinuationToken para obtener el siguiente segmento del informe de Chaos, o bien se puede especificar el intervalo a través de StartTimeUtc y EndTimeUtc, pero no se pueden especificar ContinuationToken y el intervalo de tiempo en la misma llamada. Cuando hay más de 100 eventos de Chaos, el informe de Chaos se devuelve en segmentos y ningún segmento contiene más de 100 eventos de Chaos.

## <a name="important-configuration-options"></a>Opciones de configuración importantes
* **TimeToRun**: Tiempo total durante el cual se ejecutará Caos antes de finalice correctamente. Puede detener Caos antes de que se haya ejecutando durante el período de TimeToRun a través de la API StopChaos.

* **MaxClusterStabilizationTimeout**: cantidad máxima de tiempo que se espera para que el estado del clúster sea correcto antes de generar una instancia de ValidationFailedEvent. Esta espera es para reducir la carga en el clúster mientras se está recuperando. Estas son las comprobaciones que se realizan:
  * Si el estado del clúster es correcto.
  * Si el servicio del clúster es correcto.
  * Si el tamaño del conjunto de réplicas de destino se consigue para la partición del servicio.
  * Que no existan réplicas InBuild.
* **MaxConcurrentFaults**: El número máximo de errores simultáneos que se inducen en cada iteración. Cuanto mayor sea el número, más agresivo será Chaos y tanto las conmutaciones por error como las combinaciones de las transiciones de estado por las que pasa el clúster también serán más complejas. 

> [!NOTE]
> Independientemente de lo alto que sea el valor de *MaxConcurrentFaults* Chaos garantiza que, si no hay errores externos, no habrá pérdida de cuórum ni de datos.
>

* **EnableMoveReplicaFaults**: Habilita o deshabilita los errores que provocan el movimiento de las réplicas principales o secundarias. Estos errores están deshabilitados de forma predeterminada.
* **WaitTimeBetweenIterations**: cantidad de tiempo que se espera entre iteraciones. Es decir, la cantidad de tiempo que Chaos estará en pausa después de haber ejecutado una ronda de errores y haber finalizó la validación correspondiente del estado del clúster. Cuanto mayor sea el valor, menor será la tasa media de inserción de errores.
* **WaitTimeBetweenFaults**: cantidad de tiempo que se espera entre dos errores consecutivos en una sola iteración. Cuanto mayor sea el valor, menor será la simultaneidad de los errores (o la superposición entre ellos).
* **ClusterHealthPolicy**: la directiva de mantenimiento de clúster se utiliza para validar el mantenimiento del clúster entre las iteraciones de Chaos. Si el mantenimiento del clúster está en estado de error o si se produce una excepción inesperada durante la ejecución del error, Chaos esperará 30 minutos para realizar la siguiente comprobación de estado (para proporcionar el clúster un tiempo para recuperarse).
* **Context**: colección de (cadena, cadena) pares de tipo clave-valor. El mapa se puede usar para registrar información acerca de la ejecución de Chaos. No puede haber más de 100 de dicho pares y cada cadena (clave o valor) puede tener una longitud máxima de 4095. Este mapa lo establece el inicio de la ejecución de Chaos para almacenar, opcionalmente, el contexto de la ejecución específica.

## <a name="how-to-run-chaos"></a>Ejecución de Caos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };
            
            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}

```
