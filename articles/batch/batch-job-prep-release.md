<properties
	pageTitle="Preparación y limpieza del trabajo en Lote | Microsoft Azure"
	description="Emplee tareas de preparación en el nivel de trabajo para minimizar la transferencia de datos a nodos de ejecución de Lote de Azure y tareas de liberación para la limpieza del nodo tras la finalización del trabajo."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="06/22/2016"
	ms.author="marsma" />

# Ejecución de tareas de preparación y finalización de trabajos en nodos de ejecución de Lote de Azure

A menudo los trabajos de Lote de Azure precisan algún tipo de configuración antes de su ejecución, así como algún tipo de mantenimiento posterior una vez finalizadas sus tareas. Lote proporciona los mecanismos para estas labores de preparación y mantenimiento en forma de tareas opcionales de preparación del trabajo y liberación del trabajo.

Antes de la ejecución de ninguna otra tarea de un trabajo, la **tarea de preparación del trabajo** se ejecuta en todos los nodos de proceso programados para ejecutar tareas. Cuando el trabajo se ha completado, la **tarea de liberación del trabajo** se ejecuta en cada nodo del grupo que ejecutó al menos una tarea. Al igual que con las tareas normales de Lote, puede especificar una línea de comandos de una tarea de preparación o liberación del trabajo para que se invoque cuando se ejecute esa tarea. Estas tareas especiales ofrecen otras características de tareas familiares tales como descarga de archivos, ejecución con elevación de privilegios, variables de entorno personalizadas, duración máxima de ejecución, número de reintentos y tiempo de retención de archivo.

En las secciones siguientes, encontrará información acerca de cómo usar estos dos tipos especiales de tarea con la clase [JobPreparationTask][net_job_prep] y la clase [JobReleaseTask][net_job_release] en la API de [Lote de .NET][api_net].

> [AZURE.TIP] Las tareas de preparación y liberación de trabajos son especialmente útiles en entornos de "grupo compartido", es decir, entornos en los que un grupo de nodos de proceso persiste entre ejecuciones de trabajo y se comparte entre varios trabajos diferentes.

## Uso de las tareas de preparación y liberación de trabajos

Siempre que necesite preparar nodos con una configuración o unos datos específicos del trabajo (y limpiar o conservar los datos de resultados de tareas), es un buen momento para utilizar las tareas de preparación y liberación de trabajos. Ejemplos de estas situaciones son:

**Transferencia de datos de tareas comunes**

A menudo, los trabajos de Lote requieren un conjunto común de datos como entrada para las tareas del trabajo. Por ejemplo, en cálculos de análisis de riesgos diarios, los datos de mercado son específicos del trabajo, pero comunes a todas las tareas incluidas en él. Estos datos de mercado, a menudo con un tamaño de varios gigabytes, deben descargarse en cada nodo de ejecución una sola vez, para que cualquier tarea que se ejecuta en un nodo pueda usarlos. Puede usar una **tarea de preparación del trabajo** para descargar los datos en cada nodo antes de la ejecución de otras tareas del trabajo.

**Eliminación de datos del trabajo**

En un entorno de grupo compartido en el que los nodos de proceso del grupo no se retiran entre trabajos, podría resultar necesario eliminar los datos del trabajo entre ejecuciones, con el fin de conservar espacio en disco en los nodos o quizás de cumplir con las directivas de seguridad de la organización. Puede usar una **tarea de liberación del trabajo** para eliminar los datos descargados por una tarea de preparación del trabajo o generados durante la ejecución de la tarea.

**Retención de registro**

Puede que desee conservar una copia de los archivos de registro generados por las tareas o quizás los archivos de volcado de memoria generados por aplicaciones con errores. Puede usar una **tarea de liberación del trabajo** en estos casos para comprimir y cargar estos datos en una cuenta de [Almacenamiento de Azure][azure_storage].

## Tarea de preparación del trabajo

Antes de ejecutar las tareas de un trabajo, se ejecuta la tarea de preparación del trabajo en cada nodo de ejecución programado para ejecutar una tarea. De forma predeterminada, el servicio Lote esperará hasta que la tarea de preparación del trabajo se complete antes de ejecutar las tareas programadas para ejecutarse en el nodo. Sin embargo, puede configurar el servicio para que no espere. Si el nodo de ejecución se reinicia, la tarea se ejecutará de nuevo, pero también puede deshabilitar este comportamiento.

La tarea de preparación del trabajo solo se ejecuta en los nodos programados para ejecutar una tarea. Esto impide la ejecución innecesaria de una tarea de preparación en caso de que un nodo no tenga una tarea asignada. Esto puede ocurrir cuando el número de tareas de un trabajo es menor que el número de nodos de un grupo. También se aplica cuando la [ejecución de tareas simultáneas](batch-parallel-node-tasks.md) está habilitada, lo que deja algunos nodos inactivos si el número de tareas es menor que el total de posibles tareas simultáneas. Si no ejecuta la tarea de preparación del trabajo en nodos inactivos, puede ahorrar dinero en gastos de transferencia de datos.

> [AZURE.NOTE] [JobPreparationTask][net\_job\_prep\_cloudjob] difiere de [CloudPool.StartTask][pool_starttask] en que JobPreparationTask se ejecuta al principio de cada trabajo, mientras que StartTask solo se ejecuta cuando un nodo de ejecución se une por primera vez a un grupo o se reinicia.

## Tarea de liberación del trabajo

Cuando un trabajo se marca como completado, se ejecuta la tarea de liberación del trabajo en cada nodo del grupo que ejecutó al menos una tarea. El trabajo se marca como completado mediante la emisión de una solicitud de finalización. A continuación, el servicio Lote establece el estado del trabajo en *terminando*, finaliza las tareas activas o en ejecución asociadas con el trabajo y ejecuta la tarea de liberación del trabajo. A continuación, el trabajo se mueve al estado *completado*.

> [AZURE.NOTE] La eliminación de un trabajo también ejecuta la tarea de liberación del trabajo. Sin embargo, si un trabajo ya se ha terminado, la tarea no se ejecuta una segunda vez si se elimina más adelante el trabajo.

## Tareas de preparación y liberación de trabajos con Lote para .NET

Para usar una tarea de preparación del trabajo, cree y configure el objeto [JobPreparationTask][net_job_prep] y asígnelo a la propiedad [CloudJob.JobPreparationTask][net_job_prep_cloudjob] del trabajo. De forma similar, para establecer la tarea de liberación del trabajo, inicialice [JobReleaseTask][net_job_release] y asígnelo a la propiedad [CloudJob.JobReleaseTask][net_job_prep_cloudjob] del trabajo.

En este fragmento de código, `myBatchClient` es una instancia totalmente inicializada de [BatchClient][net_batch_client] y `myPool` es un grupo existente dentro de la cuenta de Lote.

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

Como se mencionó anteriormente, la tarea de liberación se ejecuta cuando se finaliza o se elimina un trabajo. La finalización de un trabajo con la API de .NET de Lote se realiza mediante una llamada a [JobOperations.TerminateJobAsync][net_job_terminate]. La eliminación del trabajo se realiza con [JobOperations.DeleteJobAsync][net_job_delete]. Ambas acciones suelen realizarse cuando se han completado las tareas de un trabajo o cuando se ha alcanzado el tiempo de espera que haya definido.

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## Código de ejemplo en GitHub

Consulte el proyecto de ejemplo [JobPrepRelease][job_prep_release_sample] en GitHub para ver cómo funcionan las tareas de preparación y liberación del trabajo. Esta aplicación de consola hace lo siguiente:

1. Crea un grupo con dos nodos "pequeños".
2. Crea un trabajo con las tareas de preparación y de liberación del trabajo, además de las estándar.
3. Ejecuta la tarea de preparación del trabajo, que en primer lugar escribe el identificador de nodo en un archivo de texto en el directorio "shared" de un nodo.
4. Ejecuta una tarea en cada nodo que escribe su identificador de tarea en el mismo archivo de texto.
5. Una vez que se han completado todas las tareas (o se alcanza el tiempo de espera), imprime el contenido del archivo de texto de cada nodo en la consola.
6. Cuando se ha completado el trabajo, ejecuta la tarea de liberación del trabajo para eliminar el archivo del nodo.
6. Imprime los códigos de salida de las tareas de preparación y liberación del trabajo para cada nodo donde se ejecutaron.
7. Pausa la ejecución para permitir la confirmación de la eliminación del trabajo o el grupo.

La salida de la aplicación de ejemplo es similar a la siguiente:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Debido a la creación de variables y a la hora de inicio de los nodos en un nuevo grupo (algunos nodos están listos para las tareas antes que otros), puede que la salida sea diferente. En concreto, como las tareas se realizan rápidamente, uno de los nodos del grupo podría ejecutar todas las tareas del trabajo. Si esto sucede, observará que las tareas de preparación y liberación del trabajo no existen para el nodo que no ha ejecutado ninguna tarea.

### Inspección de las tareas de preparación y liberación en el Portal de Azure

Cuando ejecute la aplicación de ejemplo anterior, puede usar el [Portal de Azure][portal] para ver las propiedades del trabajo y sus tareas, o incluso descargar el archivo de texto compartido modificado por las tareas del trabajo.

La captura de pantalla siguiente muestra la **hoja de tareas de preparación** en el Portal de Azure después de una ejecución de la aplicación de ejemplo. Vaya a las propiedades *JobPrepReleaseSampleJob* después de que sus tareas se hayan completado (pero antes de eliminar el trabajo y el grupo) y haga clic en **Preparation tasks** (Tareas de preparación) o **Release tasks** (Tareas de liberación) para ver sus propiedades.

![Propiedades de preparación del trabajo en el Portal de Azure][1]

## Pasos siguientes

### Paquetes de aplicación

Además de la tarea de preparación, puede usar también la característica de [paquetes de aplicación](batch-application-packages.md) de Lote para preparar los nodos de proceso de cara a la ejecución de tareas. Esta característica es especialmente útil para implementar aplicaciones que no requieren que se ejecute un instalador, aplicaciones que contienen muchos archivos (más de 100) o aplicaciones que requieren un control estricto de la versión.

### Instalación de aplicaciones y datos provisionales

Consulte el artículo [Installing applications and staging data on Batch compute nodes][forum_post] (Instalación de aplicaciones y datos provisionales en nodos de proceso de Lote) en el foro de Lote de Azure para ver información general de los diversos métodos de preparar los nodos para la ejecución de tareas. Este artículo ha sido escrito por uno de los miembros del equipo de Lote de Azure, y constituye una buena toma de contacto con las diferentes maneras de introducir archivos (tanto aplicaciones como datos de entrada de tareas) en los nodos de proceso. Incluye también algunas consideraciones especiales que se deben tener en cuenta para cada método.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/es-ES/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net\_job\_prep\_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!---HONumber=AcomDC_0629_2016-->