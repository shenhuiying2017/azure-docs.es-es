<properties
   pageTitle="Inducción de errores controlados con Caos en clústeres de Service Fabric | Microsoft Azure"
   description="Uso de los servicios de inserción de errores y análisis del clúster para administrar Caos en el clúster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# Inducción de errores controlados con Caos en clústeres de Service Fabric
Los sistemas distribuidos a gran escala, como las infraestructuras en la nube, por naturaleza, no son confiables. Azure Service Fabric permite a los desarrolladores crear servicios confiables sobre una infraestructura no confiable. Para crear servicios sólidos, los desarrolladores deben poder inducir errores en esa infraestructura no confiable para probar la estabilidad de sus servicios.

El servicio de inserción de errores y análisis del clúster (también denominado "FAS") proporciona a los desarrolladores la posibilidad de inducir acciones de error para probar los servicios. Sin embargo, hasta ahora se obtendrán solo errores simulados dirigidos. Para realizar pruebas más exhaustivas, se puede utilizar Caos. Esta característica simulan errores continuos intercalados, tanto correctos como incorrectos, en todo el clúster durante periodos prolongados. Una vez configurada una prueba con la tasa y el tipo de errores, se puede iniciar mediante las API de C# o de PowerShell para generar errores en el clúster y en el servicio. Mientras se ejecuta Caos, se producen diferentes eventos que obtienen el estado de la ejecución en ese momento. Por ejemplo, ExecutingFaultsEvent contiene todos los errores que se están produciendo en esa iteración; ValidationFailedEvent contiene los detalles de un error detectado durante la validación del clúster. Puede invocar la API GetChaosReportAsync para obtener el informe de ejecuciones de Caos.

## Errores inducidos en Caos
Caos genera errores en todo el clúster de Service Fabric y comprime todos los detectados en meses o años en tan solo unas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. Esto conduce a una mejora considerable en la calidad del código del servicio. Caos induce errores de las siguientes categorías:

 - Reinicio de un nodo
 - Reinicio de un paquete de código implementado
 - Eliminación de una réplica
 - Reinicio de una réplica
 - Desplazamiento de una réplica principal (configurable)
 - Desplazamiento de una réplica secundaria (configurable)

Caos se ejecuta en varias iteraciones y cada una de ellas consta de validaciones de clúster y errores correspondientes al periodo especificado. También se puede configurar el tiempo empleado por el clúster para que la estabilización y la validación sean correctas. Si se detecta un error de validación de clúster, Caos genera y conserva un evento ValidationFailedEvent con la marca de tiempo UTC y los detalles de los errores.

Por ejemplo, analice una instancia de Caos establecida para ejecutarse durante una hora con un máximo de tres errores simultáneos. Caos induce tres errores y, después, valida el estado del clúster y lo itera a través del paso anterior hasta que el proceso se detenga expresamente mediante la API StopChaosAsync o cuando transcurra una hora. Si el clúster pasa a tener un estado incorrecto en cualquier iteración, es decir, no se estabiliza dentro de un tiempo configurado, Caos genera un evento ValidationFailedEvent, que indica que se produjo un error y que hay que realizar más investigaciones.

En la versión actual, Caos solo induce errores seguros; es decir, si no hay errores externos, nunca se producirá una pérdida de cuórum o de datos.

## Opciones de configuración importantes
 - **TimeToRun**: tiempo total durante el cual se ejecutará Caos antes de finalizar el proceso con éxito. Caos puede detenerse antes de que se haya ejecutando durante el periodo de TimeToRun a través de la API StopChaos.
 - **MaxClusterStabilizationTimeout**: cantidad máxima de tiempo de espera para que el clúster pase a tener un estado correcto antes de volver a realizar la comprobación. El objetivo de esta espera es reducir la carga en el clúster mientras se está recuperando. Estas son las comprobaciones que se realizan:
    - Si el estado del clúster es correcto.
    - Si estado del servicio es correcto.
    - El tamaño del conjunto de réplicas de destino conseguido para la partición del servicio.
    - No hay réplicas InBuild.
 - **MaxConcurrentFaults**: número máximo de errores simultáneos inducidos en cada iteración. Cuanto mayor sea el número, más agresiva será la acción de Caos. Por lo tanto, dará como resultado combinaciones de conmutaciones por error y de transición más complejas. Caos garantiza que, si no hay errores externos, no habrá pérdida de cuórum o de datos, con independencia de lo elevado del número de esta configuración.
 - **EnableMoveReplicaFaults**: habilita o deshabilita los errores que provocan el movimiento de las réplicas principales o secundarias. Estos errores están deshabilitados de forma predeterminada.
 - **WaitTimeBetweenIterations**: cantidad de tiempo de espera entre iteraciones; es decir, después de una ronda de errores y de su validación correspondiente.
 - **WaitTimeBetweenFaults**: cantidad de tiempo de espera entre dos errores consecutivos en una iteración.

## Ejecución de Caos
Ejemplo de C#

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
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

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

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If StoppedEvent is found, exit the loop.
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
PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

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
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```

<!---HONumber=AcomDC_0921_2016-->