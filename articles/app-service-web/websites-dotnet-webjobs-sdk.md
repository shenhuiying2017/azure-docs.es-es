<properties 
	pageTitle="Qué es el SDK de WebJobs de Azure" 
	description="Una introducción al SDK de WebJobs de Azure. Se explica qué es el SDK, se describen escenarios habituales en los que resulta útil y se proporcionan códigos de ejemplo." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Qué es el SDK de WebJobs de Azure

## <a id="overview"></a>Información general

Este artículo explica qué es el SDK de Trabajos web, revisa algunos escenarios habituales en los que resulta de utilidad y ofrece información general acerca de cómo utilizarlo en su código.

[WebJobs](websites-webjobs-resources.md) es una característica del Servicio de aplicaciones de Azure que le permite ejecutar un programa o script en el mismo contexto que una aplicación web. El objetivo del SDK de Trabajos web consiste en simplificar la tarea de escribir el código que se ejecuta como Trabajo web y funciona con las colas, los blobs y las tablas de Almacenamiento de Azure y con las colas del Bus de servicio.

El SDK de WebJobs incluye los siguientes componentes:

* **Paquetes NuGet**. Los paquetes NuGet que se agregan a un proyecto de la aplicación de consola en Visual Studio proporcionan un marco de trabajo que usa el código para funcionar con el servicio de Almacenamiento de Azure o con las colas del bus de servicio.   
  
* **Panel**. Parte del SDK de WebJobs se incluye en Servicio de aplicaciones de Azure y proporciona una supervisión y un diagnóstico completos para los programas que utilizan paquetes NuGet. No es necesario escribir código para usar estas características de supervisión y diagnóstico.

## <a id="scenarios"></a>Escenarios

Estos son algunos de los escenarios típicos que puede controlar más fácilmente con el SDK de Azure WebJobs:

* Procesamiento de imágenes u otros trabajos de uso intensivo de CPU. Una característica común de los sitios web es la capacidad para cargar imágenes y vídeos. A menudo, necesitamos manipular el contenido después de cargarlo, pero no queremos que el usuario esté esperando mientras realizamos este proceso.

* Procesamiento de colas. Una de las formas más comunes de establecer comunicación entre un front-end web y un servicio back-end consiste en usar colas. Cuando el sitio web necesita que se realicen tareas en él, inserta un mensaje en una cola. Un servicio back-end extrae los mensajes de la cola y realiza el trabajo. Puede usar colas para procesamiento de imágenes: por ejemplo, después de que el usuario cargue una serie de archivos, coloque los nombres de archivo en un mensaje de cola para que el backend los recoja para su procesamiento. También puede usar las colas para mejorar la capacidad de respuesta del sitio. Por ejemplo, en lugar de escribir directamente en una base de datos SQL, escriba en una cola, indique al usuario que ha finalizado y deje que el servicio back-end controle el trabajo de bases de datos relacionales de alta latencia. Para ver un ejemplo del procesamiento de cola con proceso de imagen, consulte el tutorial [Introducción al SDK de WebJobs](websites-dotnet-webjobs-sdk-get-started.md).

* Agregación de fuentes RSS. Si tiene un sitio que mantiene una lista de fuentes RSS, puede extraer todos los artículos de las fuentes en un proceso en segundo plano.

* Mantenimiento de archivos, como el agregado o limpieza de archivos de registro. Puede tener archivos de registro creados a través de varios sitios o para intervalos de tiempo diferentes que desee combinar con el fin de ejecutar trabajos de análisis en ellos. O puede que desee programar una tarea para limpiar archivos de registro antiguos que se ejecute semanalmente.

* Entradas en tablas de Azure. Es posible que tenga archivos almacenados y blobs y que desee analizarlos y almacenar los datos en tablas. Si la función de entrada fuera escribir muchas filas (millones, en algunos casos), el SDK de Trabajos web haría posible la implementación de esta funcionalidad fácilmente. El SDK también ofrece supervisión en tiempo real de los indicadores de progreso, como el número de filas escritas en la tabla.

* Otras tareas con una ejecución prolongada que desee ejecutar en un subproceso en segundo plano, como el [envío de correos electrónicos](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

En muchos de estos escenarios, es posible que desee escalar una aplicación web para que ejecute varias máquinas virtuales, las que ejecutarán varios WebJobs de manera simultánea. En algunos escenarios, esto podría hacer que los mismos datos se procesen varias veces, pero esto no supone un problema cuando se usan los desencadenadores de colas, blobs y bus de servicio integrados del SDK de WebJobs. El SDK garantiza que las funciones se procesarán una sola vez para cada mensaje o blob.

## <a id="code"></a>Ejemplos de código

El código para controlar las tareas comunes que se realizan con Almacenamiento de Azure es sencillo. En una aplicación de consola, escriba los métodos para las tareas en segundo plano que desee ejecutar y represéntelas con atributos del SDK de WebJobs. El método `Main` crea un objeto `JobHost` que coordina las llamadas a los métodos que escriba. El marco de trabajo del SDK de WebJobs sabrá cuándo llamar a los métodos en función de los atributos del SDK de WebJobs que use en ellos.

Este es un simple programa que sondea una cola y crea un blob para cada mensaje de la cola recibido:

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

El objeto `JobHost` es un contenedor para un conjunto de funciones en segundo plano. El objeto `JobHost` supervisa las funciones, inspecciona los eventos que las desencadenan y ejecuta las funciones cuando se producen los efectos desencadenantes. La llamada a un método `JobHost` permite indicar si desea ejecutar el proceso del contenedor en el subproceso actual o en un subproceso de segundo plano. En el ejemplo, el método `RunAndBlock` ejecuta el proceso de forma continua en el subproceso actual.

Dado que el método `ProcessQueueMessage` de este ejemplo contiene un atributo `QueueTrigger`, el desencadenador de esa función es la recepción de un nuevo mensaje de cola. El objeto `JobHost` inspecciona los mensajes de cola nuevos de la cola especificada ("webjobsqueue" en este ejemplo) y cuando encuentra uno, llama a `ProcessQueueMessage`.

El atributo `QueueTrigger` enlaza el parámetro `inputText` con el valor del mensaje en la cola. Además, el atributo `Blob` enlaza un objeto `TextWriter` con un blob llamado "blobname" en un contenedor llamado "containername".

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
		    [Blob("containername/blobname")]TextWriter writer)

A continuación, la función usa estos parámetros para escribir el valor del mensaje de cola en el blob:

		writer.WriteLine(inputText);

Las características de desencadenamiento y enlace del SDK de WebJobs simplifican enormemente el código que se debe escribir para trabajar con colas de Almacenamiento de Azure y de Bus de servicio. El marco de trabajo del SDK de WebJobs se encarga de escribir el código de bajo nivel necesario para controlar el procesamiento de colas y de blobs, es decir, el marco de trabajo crea colas que no existen aún, abre la cola, lee sus mensajes, los elimina cuando se ha completado el procesamiento, crea contenedores de blobs que no existen aún, escribe en los blobs, etc.

El SDK de WebJobs ofrece diversas formas de trabajar con Almacenamiento de Azure. Por ejemplo, si el parámetro que representa con el atributo `QueueTrigger` es una matriz de bytes o un tipo personalizado, se deserializa automáticamente desde JSON. Además, puede usar un atributo `BlobTrigger` para desencadenar un proceso cada vez que se cree un blob nuevo en su cuenta de Almacenamiento de Azure. (Tenga en cuenta que mientras que `QueueTrigger` encuentra nuevos mensajes de cola en tan solo unos segundos, `BlobTrigger` puede tardar hasta 20 minutos en detectar un blob nuevo. `BlobTrigger` busca blobs siempre que se inicia `JobHost` y, después, comprueba periódicamente los registros de Almacenamiento de Azure para detectar blobs nuevos).

## <a id="workerrole"></a>Uso del SDK de WebJobs fuera de WebJobs

Un programa que utiliza el SDK de WebJobs es una aplicación de consola estándar y puede ejecutarse en cualquier lugar, no necesariamente como un trabajo web. Puede probar el programa localmente en el equipo de desarrollo y, en producción, puede ejecutarlo en un rol de trabajo de Servicio en la nube o en un servicio de Windows si prefiere uno de estos entornos.

Sin embargo, el panel solo está disponible como una extensión para una aplicación web del Servicio de aplicaciones de Azure. Si desea ejecutarlo fuera de un trabajo web y seguir utilizando el Panel, puede configurar una aplicación web para que utilice la misma cuenta de almacenamiento a la que hace referencia la cadena de conexión del Panel del SDK de WebJobs, y el Panel de WebJobs de esa aplicación web mostrará entonces información acerca de la ejecución de la función de su programa que se está ejecutando fuera. Puede obtener acceso al Panel utilizando la dirección URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para obtener más información, consulte [Cómo obtener un panel para desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), pero tenga en cuenta que la entrada del blog muestra un nombre de cadena de conexión antiguo.

## <a id="nostorage"></a>Uso del SDK de WebJobs para invocar cualquier función

El SDK de Trabajos web proporciona varias ventajas, incluso si no necesita trabajar directamente con colas, tablas o blobs de Almacenamiento de Azure o colas del Bus de servicio:

* Puede invocar funciones desde el Panel.
* Puede reproducir funciones desde el Panel.
* Puede ver los registros en el Panel, vinculados al trabajo web específico (registros de aplicación, escritos usando Console.Out, Console.Error, Trace, etc.) o vinculados a la invocación de función determinada que los generó (registros escritos usando un objeto `TextWriter` que el SDK pasa a la función como parámetro). 

* Para obtener más información, consulte [Invocación de una función manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) y [Escritura de registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información acerca del SDK de WebJobs, consulte [Recursos recomendados de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=Oct15_HO3-->