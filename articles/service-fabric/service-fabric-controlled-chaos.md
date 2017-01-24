---
title: "Inducción de errores controlados con Caos en clústeres de Service Fabric | Microsoft Docs"
description: "Uso de los servicios de inserción de errores y análisis del clúster para administrar Caos en el clúster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Inducción de errores controlados con Caos en clústeres de Service Fabric
Los sistemas distribuidos a gran escala, como las infraestructuras en la nube, por naturaleza, no son confiables. Azure Service Fabric permite a los desarrolladores crear servicios confiables sobre una infraestructura no confiable. Para crear servicios sólidos, los desarrolladores deben poder inducir errores en esa infraestructura no confiable para probar la estabilidad de sus servicios.

El servicio de inserción de errores y análisis del clúster (también denominado servicio de análisis de errores) proporciona a los desarrolladores la posibilidad de inducir acciones de error para probar los servicios. Sin embargo, hasta ahora se obtendrán solo errores simulados dirigidos. Para realizar pruebas más exhaustivas, se puede utilizar Caos.

Esta característica simula errores continuos intercalados (tanto correctos como incorrectos) en todo el clúster durante períodos prolongados. Después de configurar Caos con la tasa y el tipo de errores, puede iniciarlo o detenerlo mediante las API de C# o PowerShell para generar errores en el clúster y en el servicio.

Mientras se ejecuta Caos, se producen diferentes eventos que obtienen el estado de la ejecución en ese momento. Por ejemplo, ExecutingFaultsEvent contiene todos los errores que se ejecutan en esa iteración. ValidationFailedEvent contiene los detalles de un error que se encontró durante la validación del clúster. Puede invocar la API GetChaosReportAsync para obtener el informe de ejecuciones de Caos.

## <a name="faults-induced-in-chaos"></a>Errores inducidos en Caos
Caos genera errores en todo el clúster de Service Fabric y comprime todos los detectados que se ven en meses o años en tan solo unas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. Este ejercicio de Caos conduce a una mejora considerable en la calidad del código del servicio.

Caos induce errores de las siguientes categorías:

* Reinicio de un nodo
* Reinicio de un paquete de código implementado
* Eliminación de una réplica
* Reinicio de una réplica
* Desplazamiento de una réplica principal (configurable)
* Desplazamiento de una réplica secundaria (configurable)

Caos se ejecuta en varias iteraciones. Cada iteración consta de validaciones de clúster y errores correspondientes al período especificado. Puede configurar el tiempo empleado por el clúster para que la estabilización y la validación sean correctas. Si se detecta un error de validación de clúster, Caos genera y conserva un evento ValidationFailedEvent con la marca de tiempo UTC y los detalles de los errores.

Por ejemplo, analice una instancia de Caos que esté establecida para ejecutarse durante una hora con un máximo de tres errores simultáneos. Caos induce tres errores y después valida el estado del clúster. Itera a través del paso anterior hasta que el proceso se detiene expresamente mediante la API StopChaosAsync o cuando transcurre una hora. Si el clúster pasa a ser incorrecto en cualquier iteración (es decir, no se estabiliza dentro de un tiempo configurado), Caos genera ValidationFailedEvent. Este evento indica que algo salió mal y que podría necesitar más investigación.

En su forma actual, Caos induce únicamente errores seguros. Esto implica que, en ausencia de errores externos, nunca se produce una pérdida de quórum o de datos.

## <a name="important-configuration-options"></a>Opciones de configuración importantes
* **TimeToRun**: Tiempo total durante el cual se ejecutará Caos antes de finalice correctamente. Puede detener Caos antes de que se haya ejecutando durante el período de TimeToRun a través de la API StopChaos.
* **MaxClusterStabilizationTimeout**: Cantidad máxima de tiempo de espera para que el estado del clúster sea correcto antes de comprobarlo de nuevo. Esta espera es para reducir la carga en el clúster mientras se está recuperando. Estas son las comprobaciones que se realizan:
  * Si el estado del clúster es correcto.
  * Si el servicio del clúster es correcto.
  * Si el tamaño del conjunto de réplicas de destino se consigue para la partición del servicio.
  * Que no existan réplicas InBuild.
* **MaxConcurrentFaults**: El número máximo de errores simultáneos que se inducen en cada iteración. Cuanto mayor sea el número, más agresivo será Caos. Esto da como resultado conmutaciones por error y combinaciones de transición más complejas. Caos garantiza que, si no hay errores externos, no habrá pérdida de cuórum o de datos, con independencia de lo elevado del número de esta configuración.
* **EnableMoveReplicaFaults**: Habilita o deshabilita los errores que provocan el movimiento de las réplicas principales o secundarias. Estos errores están deshabilitados de forma predeterminada.
* **WaitTimeBetweenIterations**: Cantidad de tiempo de espera entre iteraciones, es decir, después de una ronda de errores y de su validación correspondiente.
* **WaitTimeBetweenFaults**: Cantidad de tiempo de espera entre dos errores consecutivos en una iteración.

## <a name="how-to-run-chaos"></a>Ejecución de Caos
**C#:**

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
**PowerShell:**

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



<!--HONumber=Nov16_HO3-->


