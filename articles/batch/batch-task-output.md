---
title: "Conservación de la salida de trabajos y tareas: Azure Batch | Microsoft Docs"
description: "Obtenga información acerca de cómo usar el Almacenamiento de Azure como almacén duradero para las salidas de tareas y trabajos de Lote de Azure y cómo habilitar la visualización de estas salidas almacenadas en el Portal de Azure."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 01/23/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: ffba988bd8cd3896816118afde979c7067fced79
ms.openlocfilehash: e5231970b772f7cc043441954ebab6cb1bb6ed8b


---
# <a name="persist-azure-batch-job-and-task-output"></a>Almacenamiento de la salida de trabajos y tareas de Lote de Azure
Las tareas que se ejecutan normalmente en Lote producen salidas que se deben almacenar y que posteriormente otras tareas del trabajo, o la aplicación cliente que ejecutó el mismo o ambas, recuperarán. Esta salida puede consistir en archivos creados mediante el procesamiento de datos de entrada o en archivos de registro asociados con la ejecución de la tarea. Este artículo presenta una biblioteca de clases .NET que usa una técnica basada en convenciones para guardar esta salida de las tareas en el Almacenamiento de blobs de Azure, lo cual hace que esté disponible incluso después de eliminar los grupos, los trabajos y los nodos de proceso.

Mediante la técnica descrita en este artículo, puede ver la salida de las tareas en **Archivos de salida guardados** y en **Registros guardados** en el [Azure Portal][portal].

![Selectores de archivos de salida guardados y registros guardados en el portal][1]

> [!NOTE]
> La biblioteca de clases .NET de [Azure Batch File Conventions][nuget_package] (Convenciones de archivos para Azure Batch) que se trata en este artículo actualmente se encuentra en versión preliminar. Algunas de las características que se describen aquí pueden cambiar antes de tener disponibilidad general.
> 
> 

## <a name="task-output-considerations"></a>Consideraciones sobre la salida de las tareas
Al diseñar la solución Batch, debe tener en cuenta varios factores relacionados con las salidas de los trabajos y las tareas.

* **Duración del nodo de proceso**: los nodos de proceso a menudo son transitorios, especialmente en los grupos con escalado automático habilitado. Las salidas de las tareas que se ejecutan en un nodo solo están disponibles mientras existe el nodo y solo durante el tiempo de retención del archivo que se haya establecido para la tarea. Para asegurarse de que se guarda la salida de la tarea, las tareas deben cargar los archivos de salida en un almacén duradero como, por ejemplo, el Almacenamiento de Azure.
* **Almacenamiento de salida**: para guardar los datos de salida de la tarea en un almacenamiento duradero, puede usar el [SDK de Almacenamiento de Azure](../storage/storage-dotnet-how-to-use-blobs.md) en el código de tarea para cargar la salida de la tarea en un contenedor de Almacenamiento de blobs. Si implementa un contenedor y una convención de nomenclatura de archivos, la aplicación cliente u otras tareas del trabajo pueden, a continuación, buscar y descargar este resultado basándose en esta convención.
* **Recuperación de la salida**: puede recuperar la salida de la tarea directamente desde los nodos de proceso del grupo o desde el Almacenamiento de Azure si las tareas guardan su salida. Para recuperar la salida de una tarea directamente desde un nodo de proceso, necesita el nombre del archivo y la ubicación de la salida en el nodo. Si guarda la salida en Azure Storage, las tareas de bajada o la aplicación cliente deben tener la ruta de acceso completa al archivo en Azure Storage para poder descargarlo mediante el SDK de este servicio.
* **Visualización de la salida**: si navega a una tarea de Batch en Azure Portal y selecciona **Archivos en el nodo**, se mostrarán todos los archivos asociados a la tarea y no solo los archivos de salida que le interesan. Es más, los archivos de un nodo de proceso solo están disponibles mientras existe el nodo y solo durante el tiempo de retención del archivo que se haya establecido para la tarea. Para ver la salida de la tarea que ha almacenado en Azure Storage en el portal o en una aplicación como el [Explorador de Azure Storage][storage_explorer], debe conocer su ubicación y navegar hasta el archivo directamente.

## <a name="help-for-persisted-output"></a>Ayuda sobre salidas almacenadas
Para ayudarle a guardar más fácilmente la salida de trabajos y tareas, el equipo de Batch ha definido e implementado un conjunto de convenciones de nomenclatura, así como una biblioteca de clases .NET, la biblioteca de [Azure Batch File Conventions][nuget_package], que puede utilizar en las aplicaciones de Batch. Además, el Portal de Azure reconoce estas convenciones de nomenclatura de forma que podrá encontrar fácilmente los archivos que haya guardado mediante la biblioteca.

## <a name="using-the-file-conventions-library"></a>Uso de la biblioteca de convenciones de archivo
[Azure Batch File Conventions][nuget_package] es una biblioteca de clases .NET que pueden utilizar las aplicaciones de .NET para Batch para almacenar y recuperar fácilmente las salidas de las tareas en Azure Storage o desde él. Está diseñada para su uso en el código de tarea y en el de cliente. En el código de tarea, para almacenar los archivos y en el código de cliente, para mostrarlos y recuperarlos. Las tareas también pueden usar la biblioteca para recuperar las salidas de las tareas precedentes de la cadena, como en un escenario con [dependencias entre tareas](batch-task-dependencies.md).

La biblioteca de convenciones se encarga de garantizar que los contenedores de almacenamiento y los archivos de salida de las tareas se nombran según las convenciones y se cargan en el lugar correcto al guardarlas en el Almacenamiento de Azure. Cuando intente recuperarlas, podrá encontrar fácilmente las salidas de un determinado trabajo o tarea mostrando o recuperando los resultados según el identificador y el propósito, en lugar de tener que conocer los nombres de archivo o su ubicación en el almacenamiento.

Por ejemplo, puede usar la biblioteca para "mostrar todos los archivos intermedios de la tarea 7," u "obtener la vista previa en miniatura del trabajo *mymovie*", sin necesidad de conocer los nombres de archivo o su ubicación dentro de la cuenta de almacenamiento.

### <a name="get-the-library"></a>Obtención de la biblioteca
Puede obtener la biblioteca, que contiene clases nuevas y amplía las clases [CloudJob][net_cloudjob] y [CloudTask][net_cloudtask] con nuevos métodos, desde [NuGet][nuget_package]. Puede agregarla al proyecto de Visual Studio mediante el [Administrador de paquetes de la biblioteca NuGet][nuget_manager].

> [!TIP]
> Puede encontrar el [código fuente][github_file_conventions] de la biblioteca de convenciones de archivos de Azure Batch en GitHub en el repositorio del Microsoft Azure SDK para .NET.
> 
> 

## <a name="requirement-linked-storage-account"></a>Requisito: cuenta de almacenamiento vinculada
Para almacenar las salidas en un almacenamiento duradero mediante la biblioteca de convenciones de archivos y verlas en el Portal de Azure, debe [vincular una cuenta de almacenamiento de Azure](batch-application-packages.md#link-a-storage-account) a su cuenta de Lote. Si aún no lo ha hecho, vincule una cuenta de almacenamiento a la cuenta de Lote mediante el Portal de Azure:

Hoja **Cuenta de Batch** > **Configuración** > **Cuenta de almacenamiento** > **Cuenta de almacenamiento** (ninguna) > seleccione una cuenta de almacenamiento en la suscripción

Para obtener un tutorial más detallado sobre la vinculación de una cuenta de almacenamiento, consulte [Implementación de aplicaciones con paquetes de aplicación de Lote de Azure](batch-application-packages.md).

## <a name="persist-output"></a>Guardado de salidas
Hay dos acciones principales que debe realizar al guardar las salidas de trabajos y tareas con la biblioteca de convenciones de archivos: crear el contenedor de almacenamiento y guardar la salida en el contenedor.

> [!WARNING]
> Dado que todas las salidas de trabajos y tareas se almacenan en el mismo contenedor, se pueden aplicar [límites de almacenamiento](../storage/storage-performance-checklist.md#blobs) si un gran número de tareas intenta guardar archivos al mismo tiempo.
> 
> 

### <a name="create-storage-container"></a>Creación de contenedores de almacenamiento
Antes de que las tareas empiecen a guardar salidas en el almacenamiento, debe crear un contenedor de Blob Storage en el que se carguen las salidas. Hágalo llamando a [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Este método de extensión adopta un objeto [CloudStorageAccount][net_cloudstorageaccount] como parámetro y crea un contenedor denominado de tal forma que Azure Portal y los métodos de recuperación descritos más adelante en el artículo pueden detectar su contenido.

Normalmente se coloca este código en la aplicación cliente, la aplicación que crea los grupos, los trabajos y las tareas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Almacenamiento de las salidas de tarea
Ahora que ha preparado un contenedor en Blob Storage, las tareas pueden guardar las salidas en el contenedor mediante la clase [TaskOutputStorage][net_taskoutputstorage] que se encuentra en la biblioteca de convenciones de archivos.

En el código de la tarea, cree primero un objeto [TaskOutputStorage][net_taskoutputstorage] y, luego, cuando la tarea haya finalizado su trabajo, llame al método [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] para guardar su salida en Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

El parámetro de "variante de salida" clasifica los archivos guardados. Hay cuatro tipos predefinidos de [TaskOutputKind][net_taskoutputkind]: "TaskOutput", "TaskPreview", "TaskLog" y "TaskIntermediate". También puede definir variantes personalizadas si estas fueran útiles para su flujo de trabajo.

Estos tipos de salidas le permiten especificar qué tipo de salidas se deben mostrar cuándo se realiza una consulta a Lote relacionada con las salidas guardadas de una tarea dada. En otras palabras, cuando muestra las salidas de una tarea, puede filtrar la lista por uno de los tipos de salida. Por ejemplo, "dame la salida *preview* de la tarea *109*". Aparece más información sobre la presentación y recuperación de salidas en la sección [Recuperación de salidas](#retrieve-output) más adelante en el artículo.

> [!TIP]
> La variante de salida también designa en qué lugar de Azure Portal aparece un archivo determinado. Los archivos categorizados como *TaskOutput* aparecen en Archivos de salida de tarea, y los archivos *TaskLog*, en Registros de tareas.
> 
> 

### <a name="store-job-outputs"></a>Almacenamiento de salidas de trabajos
Además de almacenar las salidas de tareas, puede almacenar las salidas asociadas a un trabajo completo. Por ejemplo, en la tarea de combinación de un trabajo de representación de una película, podría guardar la película totalmente representada como una salida de trabajo. Cuando se completa el trabajo, la aplicación cliente puede simplemente mostrar y recuperar las salidas del trabajo y no es necesario consultar las tareas individuales.

Almacene la salida del trabajo mediante una llamada al método [JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] y especifique el [JobOutputKind][net_joboutputkind] y el nombre de archivo:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Al igual que con TaskOutputKind para las salidas de tareas, utilice el parámetro [JobOutputKind][net_joboutputkind] para clasificar los archivos almacenados de un trabajo. Este parámetro permite realizar consultas posteriores (mostrar) para un tipo específico de salida. El parámetro JobOutputKind incluye los tipos de salida y de vista previa y admite la creación de tipos personalizados.

### <a name="store-task-logs"></a>Almacenamiento de los registros de tareas
Además de guardar un archivo en un almacenamiento duradero cuando se completa una tarea o trabajo, es posible que necesite guardar los archivos que se actualizan durante la ejecución de una tarea, los archivos de registro, o `stdout.txt` y `stderr.txt`, por ejemplo. Para ello, la biblioteca Azure Batch File Conventions proporciona el método [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync]. Con [SaveTrackedAsync][net_savetrackedasync], puede realizar un seguimiento de las actualizaciones de un archivo del nodo (durante un intervalo especificado) y almacenar esas actualizaciones en Azure Storage.

En el siguiente fragmento de código, utilizamos [SaveTrackedAsync][net_savetrackedasync] para actualizar `stdout.txt` en Azure Storage cada 15 segundos durante la ejecución de la tarea:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)` es simplemente un marcador de posición para el código que normalmente realizaría la tarea. Por ejemplo, es posible que tenga código que descargue datos desde Azure Storage y realice alguna transformación o cálculo en él. La parte importante de este fragmento de código demuestra cómo puede encapsular ese código en un bloque `using` para actualizar periódicamente un archivo con [SaveTrackedAsync][net_savetrackedasync].

`Task.Delay` es necesario al final de este bloque `using` para garantizar que el agente de nodo tiene tiempo para vaciar el contenido de la salida estándar al archivo stdout.txt en el nodo (el agente de nodo es un programa que se ejecuta en cada nodo del grupo y proporciona la interfaz de comando y control entre el nodo y el servicio Batch). Sin este retraso, es posible que se pierdan los últimos segundos de salida. Este retraso puede no ser necesario para todos los archivos.

> [!NOTE]
> Si habilita el seguimiento de archivos con SaveTrackedAsync, solo se guardan en el Almacenamiento de Azure los *anexos* al archivo de seguimiento. Utilice este método solo para el seguimiento de archivos de registro no rotatorios u otros archivos anexos, es decir, los datos solo se agregan al final del archivo cuando este se actualiza.
> 
> 

## <a name="retrieve-output"></a>Recuperación de salidas
Cuando recupera la salida guardada mediante la biblioteca de convenciones de archivos de Lote de Azure, lo hace basándose en las tareas y trabajos. Puede solicitar la salida de una tarea o trabajo determinado sin necesidad de conocer su ruta de acceso en el Almacenamiento de blobs o incluso sin conocer el nombre del archivo. Puede simplemente indicar "Dame los archivos de salida de la tarea *109*".

El fragmento de código siguiente procesa una iteración de todas las tareas de un trabajo, imprime información sobre los archivos de salida de la tarea y luego descarga los archivos desde el almacenamiento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Salidas de tareas y el Portal de Azure
Azure Portal muestra las salidas y los registros de las tareas que se almacenan en una cuenta de Azure Storage vinculada mediante las convenciones de nomenclatura descritas en el [archivo LÉAME sobre convenciones de archivos de Azure Batch][github_file_conventions_readme]. Puede implementar usted mismo estas convenciones en un lenguaje de su elección, o puede usar la biblioteca de convenciones de archivos en las aplicaciones. NET.

### <a name="enable-portal-display"></a>Habilitación de la presentación del portal
Para habilitar la presentación de las salidas en el portal, debe cumplir con los siguientes requisitos:

1. [Vincular una cuenta de Almacenamiento de Azure](#requirement-linked-storage-account) a la cuenta de Lote.
2. Cumplir las convenciones de nomenclatura predefinidas para los contenedores y archivos de almacenamiento al guardar salidas. Puede encontrar la definición de estas convenciones en el archivo [LÉAME][github_file_conventions_readme] de la biblioteca de convenciones de archivos. Si utiliza la biblioteca [Azure Batch File Conventions][nuget_package] para guardar la salida, este requisito se cumple.

### <a name="view-outputs-in-the-portal"></a>Visualización de salidas en el portal
Para ver las salidas y registros de las tareas en Azure Portal, navegue hasta la tarea en cuya salida está interesado y, después, haga clic en **Archivos de salida guardados** o **Registros guardados**. Esta imagen muestra los **archivos de salida guardados** para la tarea con identificador "007":

![Hoja de salidas de tareas de Azure Portal][2]

## <a name="code-sample"></a>Código de ejemplo
El proyecto de ejemplo [PersistOutputs][github_persistoutputs] es uno de los ejemplos de código de [Azure Batch][github_samples] de GitHub. Esta solución de Visual Studio 2015 muestra cómo utilizar la biblioteca de convenciones de archivos de Lote de Azure para guardar la salida de las tareas en un almacenamiento duradero. Para ejecutar el ejemplo, siga estos pasos:

1. Abra el proyecto en **Visual Studio 2015**.
2. Agregue las **credenciales de cuenta** de Batch y Storage a **AccountSettings.settings** al proyecto Microsoft.Azure.Batch.Samples.Common.
3. **Compile** (pero no ejecute) la solución. Si se le solicita, restaure los paquetes NuGet.
4. Use el Portal de Azure para cargar un [paquete de aplicación](batch-application-packages.md) para **PersistOutputsTask**. Incluya el archivo `PersistOutputsTask.exe` y los ensamblados dependientes en el paquete zip, establezca el identificador de la aplicación en "PersistOutputsTask" y la versión del paquete de aplicación en "1.0".
5. **Inicie** (ejecute) el proyecto**PersistOutputs**.

## <a name="next-steps"></a>Pasos siguientes
### <a name="application-deployment"></a>Implementación de la aplicación
La característica [paquetes de aplicación](batch-application-packages.md) de Lote proporciona una manera sencilla de implementar y de cambiar la versión de las aplicaciones que las tareas ejecutan en los nodos de proceso.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo
Consulte el artículo [Installing applications and staging data on Batch compute nodes][forum_post] (Instalación de aplicaciones y datos de ensayo en nodos de proceso de Batch) en el foro de Azure Batch para ver la información general sobre los diversos métodos de preparación de los nodos para ejecutar tareas. Este artículo lo ha escrito uno de los miembros del equipo de Azure Batch y constituye una buena toma de contacto con las diferentes maneras de introducir archivos (tanto aplicaciones como datos de entrada de tareas) en los nodos de proceso. Incluye también algunas consideraciones especiales para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Selectores de archivos de salida guardados y registros guardados en el portal"
[2]: ./media/batch-task-output/task-output-02.png "Hoja de salidas de tareas de Azure Portal"



<!--HONumber=Jan17_HO4-->


