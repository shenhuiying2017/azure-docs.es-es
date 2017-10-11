---
title: Empezar a crear soluciones de Batch con plantillas de proyecto de Visual Studio - Azure | Microsoft Docs
description: "Descubra cómo las plantillas de proyecto de Visual Studio pueden ayudarlo a implementar y ejecutar cargas de trabajo de proceso intensivo en Azure Batch."
services: batch
documentationcenter: .net
author: fayora
manager: timlt
editor: 
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da77ce827c65deb18d9d84ce5cf768d89788e205
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Uso de plantillas de proyecto de Visual Studio para empezar a crear soluciones de Batch

Las **plantillas de Visual Studio para el administrador de trabajos**y el **procesador de tareas** para Batch proporcionan código para ayudar a implementar y ejecutar las cargas de trabajo de proceso intensivo en Batch con el mínimo esfuerzo. En este documento se describen estas plantillas y se proporcionan instrucciones para utilizarlas.

> [!IMPORTANT]
> En este artículo se describe solo información aplicable a estas dos plantillas, y se supone que está familiarizado con el servicio Batch y con los conceptos clave relacionados con él: grupos, nodos de proceso, trabajos y tareas, tareas del administrador de trabajos, variables de entorno y otra información pertinente. Puede encontrar más información en [Datos básicos de Azure Batch](batch-technical-overview.md), [Información general de las características de Batch para desarrolladores](batch-api-basics.md) e [Introducción a la biblioteca de Azure Batch para .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Información general de alto nivel
Las plantillas del administrador de trabajos y del procesador de tareas pueden utilizarse para crear dos componentes útiles:

* Una tarea del administrador de trabajos que implementa un separador de trabajo que puede desglosar un trabajo en varias tareas que se pueden ejecutar de forma independiente, en paralelo.
* Un procesador de tareas que puede utilizarse para realizar un procesamiento previo y posterior alrededor de una línea de comandos de la aplicación.

Por ejemplo, en un escenario de representación de película, el separador de trabajos podría convertir un trabajo de película único en cientos o miles de tareas independientes que procesan los fotogramas individuales por separado. En consecuencia, el procesador de tareas podría invocar la aplicación de representación y todos los procesos dependientes que se necesitan para representar cada fotograma, así como realizar acciones adicionales (por ejemplo, copiar el fotograma representado en una ubicación de almacenamiento).

> [!NOTE]
> Las plantillas del administrador de trabajos y del procesador de tareas son independientes entre sí, por lo que puede elegir usar ambas, o solo una de ellas, dependiendo de los requisitos del trabajo de proceso y de las preferencias.
> 
> 

Como se muestra en el diagrama siguiente, un trabajo de proceso que usa estas plantillas pasará por tres fases:

1. El código de cliente (por ejemplo, aplicación, servicio web, etc.) envía un trabajo al servicio Azure Batch, en el que se especifica el programa de administrador de trabajos como la tarea del administrador de trabajos.
2. El servicio Batch ejecuta la tarea del administrador de trabajos en un nodo de ejecución, y el separador de trabajos inicia el número especificado de las tareas del procesador de tareas, en tantos nodos de proceso como sea necesario, según los parámetros y las especificaciones del código del separador de trabajos.
3. Las tareas del procesador de tareas se ejecutan de forma independiente, en paralelo, para procesar los datos de entrada y generar los datos de salida.

![Diagrama que muestra cómo interactúa el código de cliente con el servicio Batch][diagram01]

## <a name="prerequisites"></a>Requisitos previos
Para utilizar las plantillas de Batch, necesita lo siguiente:

* Un equipo con Visual Studio 2015 instalado. Las plantillas de proceso por lotes solo se admiten actualmente para Visual Studio 2015.
* Las plantillas de Batch, que están disponibles en la [Galería de Visual Studio][vs_gallery] como extensiones de Visual Studio. Las plantillas se pueden obtener de dos formas:
  
  * Instale las plantillas con el cuadro de diálogo **Extensiones y actualizaciones** de Visual Studio (para obtener más información, vea [Buscar y usar extensiones de Visual Studio][vs_find_use_ext]). En el cuadro de diálogo **Extensiones y actualizaciones** , busque y descargue las dos extensiones siguientes:
    
    * Administrador de trabajos de Azure Batch con separador de trabajos
    * Procesador de tareas de Azure Batch
  * Descargue las plantillas de la galería en línea para Visual Studio: [plantillas de proyecto de Microsoft Azure Batch][vs_gallery_templates]
* Si piensa utilizar la característica [Paquetes de aplicación](batch-application-packages.md) para implementar el administrador de trabajos y el procesador de tareas en los nodos de proceso de Batch, debe vincular una cuenta de almacenamiento a la cuenta de Batch.

## <a name="preparation"></a>Preparación
Se recomienda crear una solución que puede contener el administrador de trabajos, así como el procesador de tareas, ya que esto puede facilitar el uso compartido de código entre el administrador de trabajos y los programas de procesador de tareas. Para crear esta solución, siga estos pasos:

1. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.
2. En **Plantillas**, expanda **Otros tipos de proyectos**, haga clic en **Soluciones de Visual Studio** y, luego, seleccione **Solución en blanco**.
3. Escriba un nombre que describa la aplicación y el propósito de esta solución (por ejemplo, "LitwareBatchTaskPrograms").
4. Para crear una solución nueva, haga clic en **Aceptar**.

## <a name="job-manager-template"></a>Plantilla del administrador de trabajos
La plantilla del administrador de trabajos ayuda a implementar una tarea del administrador de trabajos que puede realizar las siguientes acciones:

* Separar un trabajo en varias tareas.
* Enviar esas tareas para ejecutarlas en Batch.

> [!NOTE]
> Para obtener más información sobre las tareas del administrador de trabajos, vea [Información general de las características de Batch para desarrolladores](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Creación de un administrador de trabajos con la plantilla
Para agregar un administrador de trabajos a la solución creada anteriormente, siga estos pasos:

1. Abra la solución existente en Visual Studio.
2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Agregar** > **Nuevo proyecto**.
3. En **Visual C#**, haga clic en **Nube** y, luego, haga clic en **Administrador de trabajos de Azure Batch con separador de trabajos**.
4. Escriba un nombre que describe la aplicación e identifica este proyecto como el administrador de trabajos (p. ej., "LitwareJobManager").
5. Para crear el proyecto, haga clic en **Aceptar**.
6. Por último, compile el proyecto para obligar a Visual Studio a cargar todos los paquetes NuGet a que se hace referencia y comprobar que es válido el proyecto antes de comenzar a modificarlo.

### <a name="job-manager-template-files-and-their-purpose"></a>Archivos de plantilla del administrador de trabajos y su propósito
Cuando se crea un proyecto mediante la plantilla del administrador de trabajos, genera tres grupos de archivos de código:

* El archivo de programa principal (Program.cs). Contiene el punto de entrada del programa y el control de excepciones de nivel superior. Normalmente no es necesario modificar esto.
* El directorio Framework. Contiene los archivos responsables del trabajo “reutilizable” realizado por el programa del administrador de trabajos: desempaquetar parámetros, agregar tareas al trabajo de Batch, etc. Normalmente no es necesario modificar estos archivos.
* El archivo separador de trabajos (JobSplitter.cs). Aquí es donde colocará la lógica específica de la aplicación para separar un trabajo en tareas.

Por supuesto puede agregar archivos adicionales según sea necesario para admitir el código del separador de trabajos, según la complejidad de la lógica de separación de trabajos.

La plantilla también genera archivos de proyecto estándar de .NET como un archivo .csproj, app.config, packages.config, etc.

En el resto de esta sección se describen los diferentes archivos y su estructura de código y, además, se explica lo que hace cada clase.

![Explorador de soluciones de Visual Studio que muestra la solución de la plantilla del administrador de trabajos][solution_explorer01]

**Archivos de Framework**

* `Configuration.cs`: encapsula la carga de datos de configuración de trabajos, como los detalles de la cuenta de Batch, las credenciales de la cuenta de almacenamiento vinculada, la información sobre el trabajo y la tarea y los parámetros del trabajo. También proporciona acceso a las variables de entorno definidas por Batch (vea Configuración del entorno para las tareas, en la documentación de Batch) mediante la clase Configuration.EnvironmentVariable.
* `IConfiguration.cs`: abstrae la implementación de la clase de configuración, para que pueda realizar una prueba unitaria del separador de trabajos mediante un objeto de configuración falsa o simulada.
* `JobManager.cs`: organiza los componentes del programa del administrador de trabajos. Es responsable de inicializar el separador de trabajos, invocarlo y enviar las tareas devueltas por el separador de trabajos al remitente de la tarea.
* `JobManagerException.cs`: representa un error que requiere la terminación del administrador de trabajos. JobManagerException se utiliza para ajustar los errores “esperados” donde se puede proporcionar información de diagnóstico específica como parte de la finalización.
* `TaskSubmitter.cs`: esta clase es responsable de agregar tareas devueltas por el separador de trabajos al trabajo de Batch. La clase JobManager agrega la secuencia de tareas en lotes para la incorporación eficaz pero oportuna al trabajo; luego llama a TaskSubmitter.SubmitTasks en un subproceso en segundo plano para cada lote.

**Separador de trabajos**

`JobSplitter.cs`: esta clase contiene la lógica específica de la aplicación para separar el trabajo en tareas. El marco de trabajo invoca el método JobSplitter.Split para obtener una secuencia de tareas, que agrega al trabajo a medida que el método las devuelve. Esta es la clase donde se insertará la lógica de su trabajo. Implemente el método de separación para devolver una secuencia de instancias de CloudTask que representan las tareas en las que desea partir el trabajo.

**Archivos de proyecto de línea de comandos de .NET estándar**

* `App.config`: archivo de configuración de la aplicación. NET estándar.
* `Packages.config`: archivo de dependencia de paquetes NuGet estándar.
* `Program.cs`: contiene el punto de entrada del programa y el control de excepciones de nivel superior.

### <a name="implementing-the-job-splitter"></a>Implementación del separador de trabajos
Al abrir el proyecto de plantilla del administrador de trabajos, el proyecto tendrá el archivo JobSplitter.cs abierto de forma predeterminada. Puede implementar la lógica de separación de las tareas en la carga de trabajo mediante el uso del método Split() siguiente:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> La sección anotada en el método `Split()` es la única sección del código de plantilla del administrador de trabajos que está pensada para que se modifique agregando la lógica para separar los trabajos en tareas distintas. Si desea modificar una sección diferente de la plantilla, asegúrese de familiarizarse con el funcionamiento de Batch y pruebe algunos de los [ejemplos de código de Batch][github_samples].
> 
> 

La implementación de Split() dispone de acceso a:

* Los parámetros de trabajo, a través del campo `_parameters` .
* El objeto CloudJob que representa el trabajo, a través del campo `_job` .
* El objeto CloudTask que representa la tarea del administrador de trabajos, a través del campo `_jobManagerTask` .

La implementación de `Split()` no necesita agregar tareas al trabajo directamente. En su lugar, el código debe devolver una secuencia de objetos CloudTask, y se agregarán al trabajo automáticamente mediante las clases de marco de trabajo que invocan el separador de trabajos. Es habitual utilizar la característica del iterador de C# (`yield return`) para implementar los separadores de trabajos, ya que esto permite que las tareas empiecen a ejecutarse lo antes posible, en lugar de esperar a que se calculen todas las tareas.

**Error del separador de trabajos**

Si el separador de trabajos detecta un error, debe:

* Finalizar la secuencia utilizando la instrucción `yield break` de C#, en el caso de que el administrador de trabajos se trate como correcto.
* Producir una excepción, en cuyo caso se contemplarán errores en el administrador de trabajos, que podrá volver a ejecutarse en función de cómo lo haya configurado el cliente.

En ambos casos, se pueden ejecutar todas las tareas que el separador de trabajos ya haya devuelvo y que se hayan agregado al trabajo de Batch. Si no desea que esto ocurra, entonces puede:

* Finalizar el trabajo antes de abandonar el separador de trabajos
* Fomular la colección de tareas completa antes de devolverla (es decir, devolver `ICollection<CloudTask>` o `IList<CloudTask>` instead of implementing your job splitter using a C# iterato)
* Utilizar las dependencias de tareas para realizar todas las tareas que dependen de la finalización correcta del administrador de trabajos

**Reintentos del administrador de trabajos**

Si se produce un error en el administrador de trabajos, el servicio Batch puede reintentarlo en función de la configuración de reintentos del cliente. En general, esto es seguro, porque cuando el marco de trabajo agrega tareas al trabajo, omite cualquier tarea que ya existe. Sin embargo, si la tarea de cálculo es cara, no puede incurrir en el costo de volver a calcular las tareas que ya se han agregado al trabajo; por el contrario, si no se garantiza que volver a realizar la ejecución genere los mismos identificadores de tarea, no se iniciará el comportamiento “Omitir duplicados”. En estos casos, es necesario diseñar el separador de trabajos para detectar el trabajo que se ha hecho ya y no repetirlo, por ejemplo mediante la realización de una tarea CloudJob.ListTasks antes de comenzar a producir tareas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Excepciones y códigos de salida en la plantilla del administrador de trabajos
Las excepciones y los códigos de salida proporcionan un mecanismo para determinar el resultado de ejecutar un programa, y pueden ayudar a identificar los problemas con la ejecución del programa. La plantilla del administrador de trabajos implementa las excepciones y los códigos de salida que se describen en esta sección.

Una tarea del administrador de trabajos que se implementa con la plantilla del administrador de trabajos puede devolver tres códigos de salida posibles:

| Código | Description |
| --- | --- |
| 0 |El administrador de trabajos se ha completado correctamente. El código del separador de trabajos se ejecutó hasta completarse, y todas las tareas se han agregado al trabajo. |
| 1 |Error de la tarea del administrador de trabajos con una excepción en un elemento “esperado” del programa. La excepción se ha traducido a JobManagerException con información de diagnóstico y, cuando sea posible, con sugerencias para resolver el error. |
| 2 |Error de la tarea del administrador de trabajos con una excepción “inesperada”. La excepción se ha registrado en la salida estándar, pero el administrador de trabajos no pudo agregar ninguna información adicional de diagnóstico o corrección. |

En el caso de errores de tareas del administrador de trabajos, algunas tareas aún pueden haberse agregado al servicio antes de producirse el error. Estas tareas se ejecutarán como normales. Vea la sección "Error del separador de trabajos" más arriba para consultar el análisis de esta ruta de acceso del código.

Toda la información devuelta por las excepciones se escribe en archivos stdout.txt y stderr.txt. Para obtener más información, vea [Control de errores](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Consideraciones de cliente
En esta sección se describen algunos requisitos de implementación de cliente al invocar un administrador de trabajos basado en esta plantilla. Vea [cómo pasar parámetros y variables de entorno desde el código de cliente](#pass-environment-settings) para obtener más información sobre cómo pasar los parámetros y la configuración del entorno.

**Credenciales obligatorias**

Para agregar tareas al trabajo de Azure Batch, la tarea del administrador de trabajos requiere la dirección URL y la clave de la cuenta de Azure Batch. Debe pasar estos datos en variables de entorno denominadas YOUR_BATCH_URL y YOUR_BATCH_KEY. También puede establecerlos en la configuración del entorno de tareas del administrador de trabajos. Por ejemplo, en un cliente de C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciales de almacenamiento**

Normalmente, el cliente no necesita proporcionar las credenciales de la cuenta de almacenamiento vinculada a la tarea del administrador de trabajos porque a) la mayoría de los administradores de trabajos no necesitan acceder de manera explícita a la cuenta de almacenamiento vinculada y b) la cuenta de almacenamiento vinculada suele proporcionarse a todas las tareas como una configuración de entorno común para el trabajo. Si no se proporciona la cuenta de almacenamiento vinculada a través de la configuración de entorno común, y el administrador de trabajos requiere acceso al almacenamiento vinculado, debe proporcionar las credenciales de almacenamiento vinculado como sigue:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Configuración de tareas del administrador de trabajos**

El cliente debe establecer la marca *killJobOnCompletion* del administrador de trabajos en **false**.

Normalmente es seguro que el cliente establezca *runExclusive* como **false**.

El cliente debe utilizar la colección *resourceFiles* o *applicationPackageReferences* para que el ejecutable del administrador de trabajos (y sus DLL necesarios) se implementen en el nodo de proceso.

De forma predeterminada, no se ejecutará ningún reintento del administrador de trabajos si se produce algún error. Según la lógica del administrador de trabajos, es posible que el cliente desee habilitar los reintentos mediante *constraints*/*maxTaskRetryCount*.

**Configuración de trabajos**

Si el separador de trabajos emite tareas con dependencias, el cliente debe establecer usesTaskDependencies del trabajo en true.

En el modelo del separador de trabajos, es poco común que los clientes deseen agregar tareas a trabajos más allá de lo que crea el separador de trabajos. El cliente, por tanto, normalmente debe establecer la opción *onAllTasksComplete* del trabajo en **terminatejob**.

## <a name="task-processor-template"></a>Plantilla del procesador de tareas
La plantilla del procesador de tareas ayuda a implementar un procesador de tareas que puede realizar las siguientes acciones:

* Configurar la información requerida por cada tarea de Batch para ejecutarla.
* Ejecutar todas las acciones requeridas por cada tarea de Batch.
* Guardar los resultados de las tareas en el almacenamiento persistente.

Aunque no es necesario que un procesador de tareas ejecute tareas en Batch, la principal ventaja de usar un procesador de tareas es que proporciona un contenedor para implementar todas las acciones de ejecución de tareas en una ubicación. Por ejemplo, si necesita ejecutar varias aplicaciones en el contexto de cada tarea, o si tiene que copiar datos al almacenamiento persistente después de completar cada tarea.

Las acciones realizadas por el procesador de tareas pueden ser simples o complejas, o bien muchas o pocas, según sea necesario para la carga de trabajo. Además, mediante la implementación de todas las acciones de las tareas en un procesador de tareas, puede actualizar o agregar acciones fácilmente en función de los cambios de los requisitos de las aplicaciones o cargas de trabajo. Sin embargo, en algunos casos un procesador de tareas puede no ser la solución óptima para la implementación, ya que puede agregar una complejidad innecesaria, por ejemplo cuando se ejecutan trabajos que se pueden iniciar rápidamente desde una línea de comandos simple.

### <a name="create-a-task-processor-using-the-template"></a>Creación de un procesador de tareas con la plantilla
Para agregar un procesador de tareas a la solución creada anteriormente, siga estos pasos:

1. Abra la solución existente en Visual Studio.
2. En el Explorador de soluciones, haga clic con el botón derecho en la solución; luego, en **Agregar** y en **Nuevo proyecto**.
3. En **Visual C#**, haga clic en **Nube** y, luego, haga clic en **Procesador de tareas de Azure Batch**.
4. Escriba un nombre que describe la aplicación e identifica este proyecto como el procesador de tareas (p. ej., "LitwareTaskProcessor").
5. Para crear el proyecto, haga clic en **Aceptar**.
6. Por último, compile el proyecto para obligar a Visual Studio a cargar todos los paquetes NuGet a que se hace referencia y comprobar que es válido el proyecto antes de comenzar a modificarlo.

### <a name="task-processor-template-files-and-their-purpose"></a>Archivos de plantilla del procesador de tareas y su propósito
Cuando se crea un proyecto mediante la plantilla del procesador de tareas, genera tres grupos de archivos de código:

* El archivo de programa principal (Program.cs). Contiene el punto de entrada del programa y el control de excepciones de nivel superior. Normalmente no es necesario modificar esto.
* El directorio Framework. Contiene los archivos responsables del trabajo “reutilizable” realizado por el programa del administrador de trabajos: desempaquetar parámetros, agregar tareas al trabajo de Batch, etc. Normalmente no es necesario modificar estos archivos.
* El archivo de procesador de tareas (TaskProcessor.cs). Es donde colocará la lógica específica de la aplicación para la ejecución de una tarea (normalmente llama a un archivo ejecutable existente). Código de procesamiento previo y posterior, como descargar datos adicionales o cargar archivos de resultados, que también va aquí.

Por supuesto puede agregar archivos adicionales según sea necesario para admitir el código del procesador de tareas, según la complejidad de la lógica de separación de trabajos.

La plantilla también genera archivos de proyecto estándar de .NET como un archivo .csproj, app.config, packages.config, etc.

En el resto de esta sección se describen los diferentes archivos y su estructura de código y, además, se explica lo que hace cada clase.

![Explorador de soluciones de Visual Studio que muestra la solución de la plantilla del procesador de tareas][solution_explorer02]

**Archivos de Framework**

* `Configuration.cs`: encapsula la carga de datos de configuración de trabajos, como los detalles de la cuenta de Batch, las credenciales de la cuenta de almacenamiento vinculada, la información sobre el trabajo y la tarea y los parámetros del trabajo. También proporciona acceso a las variables de entorno definidas por Batch (vea Configuración del entorno para las tareas, en la documentación de Batch) mediante la clase Configuration.EnvironmentVariable.
* `IConfiguration.cs`: abstrae la implementación de la clase de configuración, para que pueda realizar una prueba unitaria del separador de trabajos mediante un objeto de configuración falsa o simulada.
* `TaskProcessorException.cs`: representa un error que requiere la terminación del administrador de trabajos. TaskProcessorException se utiliza para ajustar los errores “esperados” donde se puede proporcionar información de diagnóstico específica como parte de la finalización.

**Procesador de tareas**

* `TaskProcessor.cs`: ejecuta la tarea. El marco de trabajo invoca el método TaskProcessor.Run. Esta es la clase donde se insertará la lógica específica de la aplicación de la tarea. Implemente el método de ejecución para:
  * Analizar y validar todos los parámetros de la tarea
  * Crear la línea de comandos para cualquier programa externo que desea invocar
  * Registra cualquier información de diagnóstico que necesita para fines de depuración
  * Iniciar un proceso mediante la línea de comandos
  * Esperar a que el proceso termine
  * Capturar el código de salida del proceso para determinar si se realizó correctamente o no
  * Guardar los archivos de salida que desea conservar en el almacenamiento persistente

**Archivos de proyecto de línea de comandos de .NET estándar**

* `App.config`: archivo de configuración de la aplicación. NET estándar.
* `Packages.config`: archivo de dependencia de paquetes NuGet estándar.
* `Program.cs`: contiene el punto de entrada del programa y el control de excepciones de nivel superior.

## <a name="implementing-the-task-processor"></a>Implementación del procesador de tareas
Al abrir el proyecto de plantilla del procesador de tareas, el proyecto tendrá el archivo TaskProcessor.cs abierto de forma predeterminada. Puede implementar la lógica de ejecución de las tareas en la carga de trabajo con el método Run() que se muestra a continuación:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> La sección anotada en el método Run() es la única sección del código de plantilla del procesador de tareas que está pensada para que se modifique agregando la lógica de ejecución de las tareas a la carga de trabajo. Si desea modificar una sección diferente de la plantilla, primero consulte la documentación de Batch para familiarizarse con el funcionamiento de Batch y pruebe algunos de los ejemplos de código de Batch.
> 
> 

El método Run() es responsable de ejecutar la línea de comandos, iniciar uno o varios procesos, esperar a que todos los procesos se completen, guardar los resultados y, por último, devolver un código de salida. El método Run() es donde se implementa la lógica de procesamiento de sus tareas. El marco de trabajo del procesador de tareas invoca el método Run(); no es necesario llamarlo usted mismo.

La implementación de Run() dispone de acceso a:

* Los parámetros de tareas, a través del campo `_parameters`.
* Los identificadores de trabajos y tareas, a través de los campos `_jobId`y `_taskId`.
* La configuración de la tarea, a través del campo `_configuration` .

**Error de tarea**

En caso de error, puede salir del método Run() iniciando una excepción, pero esto deja al controlador de excepciones de nivel superior el control del código de salida de la tarea. Si necesita controlar el código de salida para que pueda distinguir los diferentes tipos de error, por ejemplo con fines de diagnóstico o porque algunos modos de error deben finalizar el trabajo y otros no, debe salir del método Run() devolviendo un código de salida distinto de cero. Esto se convierte en el código de salida de la tarea.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Excepciones y códigos de salida en la plantilla del procesador de tareas
Las excepciones y los códigos de salida proporcionan un mecanismo para determinar el resultado de ejecutar un programa, y pueden ayudar a identificar los problemas con la ejecución del programa. La plantilla del procesador de tareas implementa las excepciones y los códigos de salida que se describen en esta sección.

Una tarea del procesador de tareas que se implementa con la plantilla del procesador de tareas puede devolver tres códigos de salida posibles:

| Código | Descripción |
| --- | --- |
| [Process.ExitCode][process_exitcode] |El procesador de tareas se ejecutó hasta completarse. Tenga en cuenta que esto no implica que el programa invocado sea correcto, solo que el procesador de tareas lo invocó correctamente y realizó cualquier procesamiento posterior sin excepciones. El significado de los códigos de salida depende del programa invocado, normalmente el código de salida 0 significa que el programa se ha ejecutado correctamente y cualquier otro código de salida significa que se ha producido algún error en el programa. |
| 1 |Error del procesador de tareas con una excepción en un elemento “esperado” del programa. La excepción se ha traducido a `TaskProcessorException` con información de diagnóstico y, cuando sea posible, con sugerencias para resolver el error. |
| 2 |Error del procesador de tareas con una excepción “inesperada”. La excepción se ha registrado en la salida estándar, pero el procesador de tareas no pudo agregar ninguna información adicional de diagnóstico o corrección. |

> [!NOTE]
> Si el programa que se invoca utiliza códigos de salida de 1 y 2 para indicar modos de error específicos, resulta ambiguo utilizar los códigos de salida 1 y 2 para los errores del procesador de tareas. Puede cambiar estos códigos de error del procesador de tareas por códigos de salida distintos mediante la edición de los casos de excepción en el archivo Program.cs.
> 
> 

Toda la información devuelta por las excepciones se escribe en archivos stdout.txt y stderr.txt. Para más información, vea Control de errores en la documentación de Batch.

### <a name="client-considerations"></a>Consideraciones de cliente
**Credenciales de almacenamiento**

Si el procesador de tareas usa Azure Blob Storage para conservar las salidas, por ejemplo, mediante la biblioteca auxiliar de convenciones de archivo, necesita acceso a *cualquiera* de las credenciales de la cuenta de almacenamiento en nube *o* a una URL de contenedor de blobs que incluye una firma de acceso compartido (SAS). La plantilla incluye compatibilidad para proporcionar credenciales a través de variables de entorno común. El cliente puede pasar las credenciales de almacenamiento como sigue:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

La cuenta de almacenamiento está disponible en la clase TaskProcessor a través de la propiedad `_configuration.StorageAccount` .

Si prefiere usar una dirección URL de contenedor con SAS, también puede pasar esto a través de una configuración de entorno de trabajo común, pero la plantilla del procesador de tareas no incluyen actualmente compatibilidad integrada para esto.

**Configuración de almacenamiento**

Se recomienda que la tarea del administrador de clientes o de trabajos cree los contenedores requeridos por las tareas antes de agregar las tareas al trabajo. Esto es obligatorio si se utiliza una dirección URL de contenedor con SAS, ya que dicha dirección URL no incluye permisos para crear el contenedor. Se recomienda incluso si pasa las credenciales de la cuenta de almacenamiento, puesto que se guarda cada tarea que tiene que llamar a CloudBlobContainer.CreateIfNotExistsAsync en el contenedor.

## <a name="pass-parameters-and-environment-variables"></a>Transferencia de parámetros y variables de entorno
### <a name="pass-environment-settings"></a>Configuración del entorno de transferencia
Un cliente puede transferir información a la tarea del administrador de trabajos en el formulario de configuración del entorno. La tarea del administrador de trabajos puede usar esta información al generar las tareas del procesador de tareas que se ejecutarán como parte del trabajo de proceso. Ejemplos de la información que se puede transferir como configuración del entorno:

* Claves de cuenta y nombre de cuenta de Storage
* Dirección URL de la cuenta de Batch
* Clave de cuenta de Batch

El servicio Batch tiene un mecanismo sencillo para transferir la configuración del entorno a una tarea del administrador de trabajos mediante la propiedad `EnvironmentSettings` en [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por ejemplo, para obtener la instancia `BatchClient` para una cuenta de Batch, puede transferir como variables de entorno del código de cliente la dirección URL y las credenciales de claves compartidas para la cuenta de Batch. Del mismo modo, para tener acceso a la cuenta de almacenamiento que está vinculada a la cuenta de Batch, puede transferir el nombre de la cuenta de almacenamiento y la clave de la cuenta de almacenamiento como variables de entorno.

### <a name="pass-parameters-to-the-job-manager-template"></a>Transferencia de parámetros a la plantilla del administrador de trabajos
En muchos casos, resulta útil transferir los parámetros de cada trabajo a la tarea del administrador de trabajos, para controlar el proceso de separación de trabajos o para configurar las tareas del trabajo. Puede hacerlo mediante la carga de un archivo JSON llamado parameters.json como un archivo de recursos para la tarea del administrador de trabajos. Los parámetros pueden estar disponibles después en el campo `JobSplitter._parameters` de la plantilla del administrador de trabajos.

> [!NOTE]
> El controlador de parámetros integrado admite solamente los diccionarios de cadena a cadena. Si desea transferir valores JSON complejos como valores de parámetro, debe transferir estos elementos como cadenas y analizarlos en el separador de trabajos o modificar el método `Configuration.GetJobParameters` del marco de trabajo.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Transferencia de parámetros a la plantilla del procesador de tareas
También puede transferir parámetros a tareas individuales que se implementan utilizando la plantilla del procesador de tareas. Al igual que con la plantilla del administrador de trabajos, la plantilla del procesador de tareas busca un archivo de recursos denominado

parameters.json y, si lo encuentra, lo carga como el diccionario de parámetros. Hay un par de opciones sobre cómo transferir parámetros a las tareas del procesador de tareas:

* Volver a usar los parámetros del trabajo JSON. Esto funciona bien si solo los parámetros son todo el trabajo (por ejemplo, una representación de anchura y altura). Para ello, al crear una tarea CloudTask en el separador de trabajos, agregue una referencia al objeto de archivo de recursos parameters.json desde ResourceFiles de la tarea del administrador de trabajos (`JobSplitter._jobManagerTask.ResourceFiles`) a la colección ResourceFiles de CloudTask.
* Generar y cargar un documento parameters.json específico de la tarea como parte de la ejecución del separador de trabajos y hacer referencia a ese blob en la colección de archivos de recursos de la tarea. Esto es necesario si diferentes tareas tienen parámetros distintos. Un ejemplo podría ser un escenario de representación 3D en que el índice de fotograma se transfiere a la tarea como un parámetro.

> [!NOTE]
> El controlador de parámetros integrado admite solamente los diccionarios de cadena a cadena. Si desea transferir valores JSON complejos como valores de parámetro, debe transferir estos elementos como cadenas y analizarlos en el procesador de tareas o modificar el método `Configuration.GetTaskParameters` del marco de trabajo.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
### <a name="persist-job-and-task-output-to-azure-storage"></a>Guardar salidas de trabajos y tareas en Azure Storage
Otra herramienta útil en el desarrollo de soluciones de Batch es [Azure Batch File Conventions][nuget_package] (Convenciones de archivos de Azure Batch). Utilice esta biblioteca de clases .NET (que actualmente se encuentra disponible en versión preliminar) para almacenar y recuperar fácilmente las salidas de las tareas en o desde Azure Storage. [Almacenamiento de la salida de trabajos y tareas de Azure Batch](batch-task-output.md) se incluye una descripción completa de la biblioteca y su uso.

### <a name="batch-forum"></a>Foro de Batch
El [foro de Azure Batch][forum] en MSDN es un lugar excelente para debatir y formular preguntas sobre el servicio. Lea los mensajes útiles publicados y envíe sus preguntas a medida que surjan mientras compila sus soluciones del servicio Batch.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
