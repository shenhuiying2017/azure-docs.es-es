<properties linkid="develop-media-services-how-to-guides-check-job-progress" urlDisplayName="Check Job Progress" pageTitle="How to Check Job Progress in Media Services - Azure" metaKeywords="" description="Learn how to use event handler code to track job progress and send status updates. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Check Job Progress" authors="migree" solutions="" manager="" editor="" />

Comprobación del progreso del trabajo
=====================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de la [Codificación de un recurso](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409).

Al ejecutar trabajos, muchas veces se requiere una forma de hacer un seguimiento al progreso del trabajo. El siguiente ejemplo de código define el controlador de eventos StateChanged. Este controlador de eventos hace un seguimiento del progreso del trabajo y proporciona un estado actualizado, según el estado. El código define también el método LogJobStop. Este método auxiliar registra los detalles del error.

``` {}
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("El estado del trabajo cambió el evento:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("El trabajo ha finalizado".);
            Console.WriteLine("Espere mientras se realizan las tareas o descargas locales...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Espere...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Convertir remitente como trabajo.
            IJob job = (IJob)sender;
            // Mostrar o registrar los detalles del error según sea necesario.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    builder.AppendLine("Media Services account location: " + _accountLocation);
    // Registrar errores del trabajo, si los hay.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Mensaje de error: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Escribir la salida en un archivo local y en la consola. La plantilla 
    // de un archivo de salida de error es:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
```

Pasos siguientes
================

Ahora que ya sabe cómo crear un trabajo y hacer un seguimiento de su progreso, el paso siguiente es proteger los recursos. Para obtener más información, consulte [Protección de un recurso con los Servicios multimedia de Azure](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409).

