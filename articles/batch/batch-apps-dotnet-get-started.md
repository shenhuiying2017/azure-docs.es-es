<properties
	pageTitle="Tutorial: Introducción a la biblioteca de aplicaciones de Lote de Azure para .NET"
	description="Aprenda los conceptos básicos sobre las aplicaciones de Lote de Azure y cómo realizar el desarrollo con un escenario simple"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# Introducción a la biblioteca de aplicaciones de Lote de Azure para .NET
Este tutorial muestra cómo ejecutar cargas de trabajo de proceso paralelas en Lote de Azure mediante el servicio de Aplicaciones de Lote.

Aplicaciones de Lote es una característica de Lote de Azure que proporciona una manera de administrar y ejecutar las cargas de trabajo de Lote centrada en las aplicaciones. Con el SDK de Aplicaciones de Lote, puede crear paquetes para habilitar una aplicación para el uso de Lote e implementarlos en su propia cuenta o bien ponerlos a disposición de otros usuarios de Lote. Lote también proporciona administración de datos, supervisión de trabajos, diagnóstico y registro integrado y soporte para dependencias entre tareas. Además, incluye un portal de administración donde puede administrar trabajos, ver registros y descargar resultados sin tener que escribir su propio cliente.

En el caso de Aplicaciones de Lote, se escribe código utilizando el SDK de la nube de Aplicaciones de Lote para dividir trabajos en tareas paralelas, se describen las dependencias entre estas tareas y se especifica cómo ejecutar cada tarea. Este código se implementa en la cuenta de Lote. Los clientes, a continuación, pueden ejecutar trabajos de manera sencilla especificando el tipo de trabajo y los archivos de entrada en una API de REST.

>[AZURE.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/). Puede usar NuGet para obtener el ensamblado <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Nube de Aplicaciones de Lote</a> y el ensamblado <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Cliente de Aplicaciones de Lote</a>. Después de crear el proyecto en Visual Studio, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. También puede descargar aquí la extensión de Visual Studio para Aplicaciones de Lote que incluye una plantilla de proyecto para habilitar las aplicaciones para la nube y la posibilidad de implementar una aplicación <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">aquí</a> o bien utilizar la función de búsqueda de **Aplicaciones de Lote** en Visual Studio mediante el elemento de menú de extensiones y actualizaciones. También puede encontrar <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">ejemplos completos en MSDN.</a>
>

###Aspectos básicos de Aplicaciones de Lote de Azure
Lote está diseñado para trabajar con las aplicaciones existentes de proceso intensivo. Aprovecha el código de la aplicación existente y se ejecuta en un entorno dinámico, virtualizado y de uso general. Para habilitar una aplicación para que funcione con Aplicaciones de Lote hay un par de cosas que necesita hacer:

1.	Prepare un archivo comprimido de los ejecutables de aplicación existentes, los mismos ejecutables que se ejecutarían en una granja o un clúster de servidores tradicional, y los archivos de soporte técnico que necesita. Este archivo comprimido, a continuación, se carga en la cuenta de Lote mediante el portal de administración o la API de REST.
2.	Cree un archivo comprimido con los "ensamblados de nube" que distribuyen las cargas de trabajo en la aplicación, y cárguelo a través del portal de administración o la API de REST. Un ensamblado de nube contiene dos componentes que se generan con el SDK de nube:
	1.	Divisor de trabajo: desglosa el trabajo en tareas que se pueden procesar de forma independiente. Por ejemplo, en un escenario de animación, el divisor de trabajo podría tomar un trabajo de película y desglosarlo en fotogramas individuales.
	2.	Procesador de tareas: invoca el ejecutable de la aplicación para una tarea determinada. Por ejemplo, en un escenario de animación, el procesador de tareas invocaría un programa de representación para representar el fotograma único especificado por la tarea en cuestión.
3.	Proporcione una manera de enviar trabajos a la aplicación habilitada en Azure. Podría tratarse de un complemento en la interfaz de usuario o un portal web de la aplicación, o incluso un servicio no atendido como parte de la canalización de ejecución. Consulte los <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">ejemplos</a> de MSDN como referencia.



###Conceptos clave de Aplicaciones de Lote
El modelo de programación y uso de Aplicaciones de Lote gira en torno a los conceptos siguientes:

####Trabajos
Un **trabajo** es un elemento de trabajo enviado por el usuario. Cuando se envía un trabajo, el usuario especifica el tipo de trabajo, cualquier configuración de ese trabajo y los datos requeridos para el trabajo. La implementación habilitada puede ocuparse de estos detalles en nombre del usuario o, en algunos casos, el usuario puede proporcionar esta información explícitamente a través del cliente. Un trabajo tiene resultados que se devuelven. Cada trabajo tiene una salida principal y, opcionalmente, un resultado en vista previa. Los trabajos también pueden devolver resultados adicionales si lo desea.

####Tareas
Una **tarea** es un elemento de trabajo que se lleva a cabo como parte de un trabajo. Cuando un usuario envía un trabajo, este se desglosa en tareas más pequeñas. El servicio procesa estas tareas individuales y, a continuación, ensambla los resultados de la tarea en una salida de trabajo global. La naturaleza de las tareas depende del tipo de trabajo. El divisor de trabajo define cómo se desglosa un trabajo en tareas, guiado por el conocimiento de aquellos fragmentos de trabajo que la aplicación va a procesar según su diseño. Los resultados de tarea también se pueden descargar individualmente y pueden resultar útiles en algunos casos; por ejemplo, cuando un usuario desea descargar tareas individuales de un trabajo de animación.

####Tareas de combinación
Una **tarea de combinaciones** un tipo especial de tarea que ensambla los resultados de tareas individuales en los resultados del trabajo final. En el caso de un trabajo de representación de película, la tarea de combinación podría ensamblar los fotogramas representados en una película o comprimir todos los fotogramas representados en un único archivo. Todos los trabajos tienen una tarea de combinación, incluso si no se requiere una "combinación" real.

####Archivos
Un **archivo** es un elemento de datos que se utiliza para suministrar información a un trabajo. Un trabajo puede no tener asociado ningún archivo de entrada o bien tener uno o varios. El mismo archivo puede utilizarse en varios trabajos, por ejemplo, para trabajo de procesamiento de películas, archivos que podrían tener texturas, modelos, etc. Para un trabajo de análisis de datos, los archivos pueden ser un conjunto de observaciones o mediciones.

###Habilitación de la aplicación en la nube
La aplicación debe contener una propiedad o un campo estático que contenga todos los detalles de la aplicación. Este especifica el nombre de la aplicación y los tipos de trabajo controlados por la aplicación. Se proporciona cuando se utiliza la plantilla en el SDK que se puede descargar a través de la Galería de Visual Studio.

Este es un ejemplo de una declaración de aplicación de nube para una carga de trabajo paralela:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementación del divisor de trabajo y el procesador de tareas
En el caso de las cargas de trabajo embarazosamente paralelas, debe implementar un divisor de trabajo y un procesador de tareas.

####Implementación de JobSplitter.SplitIntoTasks
La implementación de SplitIntoTasks debe devolver una secuencia de tareas. Cada tarea representa un elemento de trabajo independiente que se pondrá en cola para su procesamiento por un nodo de cálculo. Cada tarea debe ser independiente y debe configurarse con toda la información que necesitará el procesador de tareas.

Las tareas especificadas por el divisor de trabajo se representan como objetos TaskSpecifier. TaskSpecifier tiene varias propiedades que puede configurar antes de devolver la tarea.


-	TaskIndex se omite, pero está disponible para los procesadores de tareas. Puede utilizarlo para pasar un índice al procesador de tareas. Si no necesita pasar un índice, no es necesario configurar esta propiedad.
-	Parámetros es una colección vacía de forma predeterminada. Puede agregarle elementos o reemplazarla por una nueva colección. Puede copiar entradas de la colección de parámetros de trabajo utilizando el método WithJobParameters o WithAllJobParameters.  


RequiredFiles es una colección vacía de forma predeterminada. Puede agregarle elementos o reemplazarla por una nueva colección. Puede copiar entradas de la colección de archivos de trabajo utilizando el método RequiringJobFiles o RequiringAllJobFiles.

Puede especificar una tarea que depende de otra tarea determinada. Para ello, configure la propiedad TaskSpecifier.DependsOn, pasando el identificador de la tarea de la que depende:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

La tarea no se ejecutará hasta que esté disponible la salida de la tarea de la que depende.

También puede especificar que todo un grupo de tareas no debe comenzar a procesarse hasta que otro grupo haya finalizado completamente. En este caso puede configurar la propiedad TaskSpecifier.Stage. Las tareas con un valor determinado de Stage no comenzarán a procesarse hasta que hayan terminado todas las tareas con valores más bajos de Stage; Por ejemplo, las tareas con un valor de Stage de 3 no empezarán a procesarse hasta que hayan terminado todas las tareas con un valor de Stage de 0, 1 o 2. Las etapas deben comenzar en 0, la secuencia de etapas no debe tener espacios y SplitIntoTasks debe devolver las tareas por orden de Stage: por ejemplo, es un error devolver una tarea de Stage 0 después de una tarea de Stage 1.

Cada trabajo paralelo finaliza con una tarea especial denominada tarea de combinación. El trabajo de la tarea de combinación es ensamblar los resultados de las tareas de procesamiento en paralelo en un resultado final. Aplicaciones de Lote crea automáticamente la tarea de combinación.

En raras ocasiones, puede que desee controlar explícitamente la tarea de combinación; por ejemplo, para personalizar sus parámetros. En este caso, puede especificar la tarea de combinación devolviendo un TaskSpecifier con la propiedad IsMerge establecida en true. Esto anulará la tarea de combinación automática. Si crea una tarea de combinación explícita:

-	Puede crear solo una tarea de combinación explícita
-	Debe ser la última tarea de la secuencia
-	Debe tener IsMerge establecido en true, y el resto de tareas deben tener IsMerge establecido en false  


Sin embargo, recuerde que normalmente no necesitará crear explícitamente la tarea de combinación.

El código siguiente muestra una implementación sencilla de SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementación ParallelTaskProcessor.RunExternalTaskProcess

Se llama a RunExternalTaskProcess para cada tarea de combinación devuelta desde el divisor de trabajo. Debería invocar la aplicación con los argumentos adecuados y devolver un conjunto de resultados que deben conservarse para su uso posterior.

El siguiente fragmento muestra cómo llamar a un programa denominado application.exe. Tenga en cuenta que puede utilizar el método auxiliar ExecutablePath para crear rutas de archivo absolutas.

La clase ExternalProcess en el SDK de Nube proporciona una útil lógica auxiliar para los archivos ejecutables de la aplicación. ExternalProcess puede ocuparse de la cancelación, la conversión de códigos de salida en excepciones, la captura de la salida estándar y el error estándar y la configuración de las variables de entorno. Además, también puede utilizar la clase .NET Process directamente para ejecutar programas si lo prefiere.

El método RunExternalTaskProcess devuelve un TaskProcessResult, que incluye una lista de archivos de salida. Esto debe incluir como mínimo todos los archivos necesarios para la combinación; opcionalmente, también puede devolver archivos de registro, archivos de vista previa y archivos intermedios (por ejemplo, para fines de diagnóstico si se produjo un error en la tarea). Tenga en cuenta que el método devuelve las rutas de acceso de los archivos, no el contenido de los archivos.

Cada archivo debe identificarse con el tipo de salida que contiene: salida (es decir, la parte de la salida del trabajo eventual), vista previa, registro o intermedios. Estos valores proceden de la enumeración TaskOutputFileKind. El siguiente fragmento devuelve una salida de tarea única, y no vistas previas o registros. El método TaskProcessResult.FromExternalProcessResult simplifica el escenario común de captura del código de salida, el resultado del procesador y los archivos de salida desde un programa de línea de comandos:

El código siguiente muestra una implementación sencilla de ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementación ParallelTaskProcessor.RunExternalMergeProcess

Se le llama para la tarea de combinación. Debe invocar la aplicación para combinar los resultados de las tareas anteriores, de la forma adecuada para la aplicación, y devolver el resultado combinado.

La implementación de RunExternalMergeProcess es muy similar a RunExternalTaskProcess, salvo que:

-	RunExternalMergeProcess consume las salidas de las tareas anteriores, en lugar de archivos de entrada del usuario. Por lo tanto, debería resolver los nombres de los archivos que desea procesar basándose en el identificador de tarea, y no en la colección Task.RequiredFiles.
-	RunExternalMergeProcess devuelve un archivo JobOutput y, opcionalmente, un archivo JobPreview.


El código siguiente muestra una implementación sencilla de ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Envío de trabajos a Aplicaciones de Lote
Un trabajo describe una carga de trabajo que se ejecutará y debe incluir toda la información sobre la carga de trabajo excepto el contenido del archivo. Por ejemplo, el trabajo contiene opciones de configuración que fluyen desde el cliente a través del divisor de trabajo y las tareas. Los ejemplos proporcionados en MSDN muestran cómo enviar trabajos y proporcionan varios clientes, incluyendo un portal web y un cliente de línea de comandos.

<!---HONumber=August15_HO6-->