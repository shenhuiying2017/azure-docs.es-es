<properties
   pageTitle="Procedimiento para invocar la pérdida de datos en los servicios de Service Fabric | Microsoft Azure"
   description="En este documento se describe cómo utilizar la API de pérdida de datos."
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# Procedimiento para invocar la pérdida de datos en los servicios

>[AZURE.WARNING] En este documento se describe cómo provocar la pérdida de datos en los servicios. Debe utilizar con precaución este procedimiento.

## Introducción
Puede invocar la pérdida de datos en una partición de su servicio de Service Fabric realizando una llamada a StartPartitionDataLossAsync(). Esta API utiliza el servicio de análisis e inserción de errores para provocar condiciones de pérdida de datos.

## Uso del servicio de análisis e inserción de errores

En estos momentos, el servicio de análisis e inserción de errores admite las siguientes API descritas en el gráfico siguiente. En la parte derecha del gráfico se muestra el cmdlet de PowerShell correspondiente. Consulte la documentación de MSDN de cada API para obtener más información sobre cada una de ellas.

| API de C# | Cmdlet de PowerShell |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
|[StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
|[StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## Información general conceptual de la ejecución de un comando

El servicio de análisis e inserción de errores emplea un modelo asincrónico en el que se utiliza el comando con una API, a la que denominaremos "API de inicio" en este documento. Después, se comprueba el progreso de este comando con una API GetProgress hasta que se haya alcanzado un estado terminal o se cancele. Para iniciar un comando, llame a la API de inicio de la API correspondiente. Esta API se devuelve cuando el servicio de análisis e inserción de errores haya aceptado la solicitud. Sin embargo, no indica hasta qué punto se ha ejecutado un comando ni tampoco si se ha iniciado. Para comprobar el progreso de un comando, llame a la API GetProgress que corresponde a la API de inicio que llamamos anteriormente. La API GetProgress devolverá un objeto que indica el estado actual del comando dentro de su propiedad State. El comando se ejecuta indefinidamente hasta que se produzcan uno de los siguientes eventos:

1.	Se completa correctamente. Si, en este caso, se llama a GetProgress en ella, la propiedad State del objeto de progreso tendrá el valor Completado.
2.	Se produce un error irrecuperable. Si, en este caso, se llama a GetProgress en ella, la propiedad State del objeto de progreso tendrá el valor Error.
3.	La operación puede cancelarse mediante la API [CancelTestCommandAsync][cancel] o el cmdlet de PowerShell [Stop ServiceFabricTestCommand][cancelps]. Si, en este caso, se llama a GetProgress en ella, la propiedad State del objeto de progreso tendrá el valor Cancelado o ForceCancelled, en función de cuál sea el argumento para dicha API. Consulte la documentación de [CancelTestCommandAsync][cancel] para obtener más información.


## Detalles de la ejecución de un comando

Para iniciar un comando, llame a la API de inicio con los argumentos esperados. Todas las API de inicio tiene un argumento de Guid denominado "operationId". Se debe realizar un seguimiento del argumento operationId, ya que se utiliza para seguir el progreso de este comando. Este argumento debe pasarse a la API GetProgress con el fin de realizar un seguimiento del progreso del comando. El argumento operationId debe ser único.

Después de llamar correctamente a la API de inicio, la API GetProgress debe llamarse en un bucle hasta que la propiedad State del objeto de progreso devuelto tenga el valor Completado. Se debe volver a tratar de inicializar todas las clases [FabricTransientException][fte] y OperationCanceledException. Cuando el comando haya alcanzado un estado terminal (Completado, Error o Cancelado), la propiedad Result del objeto de progreso devuelto contendrá más información. Si el estado es Completado, Result.SelectedPartition.PartitionId incluirá el id. de partición que se seleccionó. El valor de Result.Exception será NULL. Si el estado es Error, Result.Exception incluirá la razón por la que el servicio de análisis e inserción de errores no pudo ejecutar el comando. Result.SelectedPartition.PartitionId tendrá el id. de partición que se haya seleccionado. En algunos casos, es posible que el comando no haya podido elegir correctamente una partición. En ese caso, el valor de PartitionId será 0. Si el estado es Cancelado, el valor de Result.Exception será NULL. Al igual que en el caso del estado Error, Result.SelectedPartition.PartitionId tendrá el id. de partición que se seleccionó, pero si el comando no ha podido elegir correctamente la partición, el valor será 0. Consulte también el siguiente ejemplo.

En el código de ejemplo siguiente se muestra cómo iniciar un comando y, luego, comprobar su progreso para provocar una pérdida de datos en una partición concreta.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

En el ejemplo siguiente se muestra cómo utilizar PartitionSelector para elegir una partición aleatoria de un servicio especificado:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## Historial y truncamiento

Cuando un comando haya alcanzado un estado terminal, sus metadatos permanecerán en el servicio de análisis e inserción de errores durante un periodo determinado antes de que se quiten para ahorrar espacio. Si se llama a GetProgress con el argumento operationId de un comando después de que se haya quitado, devolverá una excepción FabricException con un código de error de KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0921_2016-->