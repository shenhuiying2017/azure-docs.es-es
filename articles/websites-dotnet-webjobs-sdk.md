<properties pageTitle="Qu&eacute; es el SDK de Trabajos web de Azure" metaKeywords="Azure Azure WebJobs SDK, Azure storage, Azure queues, Azure tables" description="Una introducci&oacute;n al SDK de Trabajos web de Azure. Se explica qu&eacute; es el SDK, se describen escenarios habituales en los que resulta &uacute;til y se proporcionan c&oacute;digos de ejemplo." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Qu&eacute; es el SDK de Trabajos web de Azure" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="tdykstra" />

# Qué es el SDK de Trabajos web de Azure

Este artículo explica qué es el SDK de Trabajos web, revisa algunos escenarios habituales en los que resulta de utilidad y ofrece información general acerca de cómo utilizarlo en su código.

## Tabla de contenido

-   [Información general][Información general]
-   [Escenarios][Escenarios]
-   [Ejemplos de código][Ejemplos de código]
-   [Uso del SDK de Trabajos web fuera de Trabajos web][Uso del SDK de Trabajos web fuera de Trabajos web]
-   [Uso del SDK de Trabajos web para invocar cualquier función][Uso del SDK de Trabajos web para invocar cualquier función]
-   [Pasos siguientes][Pasos siguientes]

## <span id="overview"></span></a>Información general

[Trabajos web][Trabajos web] es una característica de Sitios web Azure que permite ejecutar un programa o script en el mismo contexto que un sitio web. El objetivo del SDK de Trabajos web consiste en simplificar la tarea de escribir el código que se ejecuta como Trabajo web y funciona con las colas, los blobs y las tablas de Almacenamiento de Azure y con las colas del Bus de servicio.

El SDK de WebJobs incluye los siguientes componentes:

-   **Paquetes NuGet**. Los paquetes de NuGet que se agregan a un proyecto de aplicación de consola de Visual Studio proporcionan un marco que usa el código para funcionar con el servicio de Almacenamiento de Azure o con las colas del Bus de servicio.

-   **Panel**. Parte del SDK de Trabajos web se incluye en Sitios web Azure y ofrece una supervisión y un diagnóstico completos para los programas que utilizan paquetes de NuGet. No es necesario escribir código para usar estas características de supervisión y diagnóstico.

## <span id="scenarios"></span></a>Escenarios

Estos son algunos de los escenarios típicos que puede controlar más fácilmente con el SDK de Azure WebJobs:

-   Procesamiento de imágenes u otros trabajos de uso intensivo de CPU. Una característica común de los sitios web es la capacidad para cargar imágenes y vídeos. A menudo, necesitamos manipular el contenido después de cargarlo, pero no queremos que el usuario esté esperando mientras realizamos este proceso.

-   Procesamiento de colas. Una de las formas más comunes de establecer comunicación entre un front-end web y un servicio back-end consiste en usar colas. Cuando el sitio web necesita que se realicen tareas en él, inserta un mensaje en una cola. Un servicio back-end extrae los mensajes de la cola y realiza el trabajo. Puede usar colas para el procesamiento de imágenes: por ejemplo, después de que el usuario cargue una serie de archivos, coloque los nombres de archivo en un mensaje de cola para que el back-end los recoja para su procesado. También puede usar las colas para mejorar la capacidad de respuesta del sitio. Por ejemplo, en lugar de escribir directamente en una base de datos SQL, escriba en una cola, indique al usuario que ha finalizado y deje que el servicio back-end controle el trabajo de bases de datos relacionales de alta latencia. Para ver un ejemplo de procesado de cola con proceso de imagen, consulte el [tutorial de introducción al SDK de Trabajos web][tutorial de introducción al SDK de Trabajos web].

-   Agregación de fuentes RSS. Si tiene un sitio que mantiene una lista de fuentes RSS, puede extraer todos los artículos de las fuentes en un proceso en segundo plano.

-   Mantenimiento de archivos, como el agregado o limpieza de archivos de registro. Puede tener archivos de registro creados a través de varios sitios o para intervalos de tiempo diferentes que desee combinar con el fin de ejecutar trabajos de análisis en ellos. O puede que desee programar una tarea para limpiar archivos de registro antiguos que se ejecute semanalmente.

-   Proporcione entradas en Tablas de Azure. Es posible que tenga archivos almacenados y blobs y que desee analizarlos y almacenar los datos en tablas. Si la función de entrada fuera escribir muchas filas (millones, en algunos casos), el SDK de Trabajos web haría posible la implementación de esta funcionalidad fácilmente. El SDK ofrece también supervisión en tiempo real de los indicadores de progreso, como el número de filas escritas en la tabla.

-   Otras tareas con una ejecución prolongada que desee ejecutar en un subproceso en segundo plano, como el [envío de correos electrónicos][envío de correos electrónicos].

## <span id="code"></span></a> Ejemplos de código

El código para controlar las tareas comunes que se realizan con Almacenamiento de Azure es muy sencillo. En una aplicación de consola, escriba los métodos para las tareas en segundo plano que desee ejecutar y represéntelas con atributos del SDK de WebJobs. El método `Main` permite crear un objeto `JobHost` que coordina las llamadas a los métodos que escriba. El marco de trabajo del SDK de WebJobs sabrá cuándo llamar a los métodos en función de los atributos del SDK de WebJobs que use en ellos.

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

El objeto `JobHost` es un contenedor de un conjunto de funciones en segundo plano. El objeto `JobHost` supervisa las funciones, inspecciona los eventos que las desencadenan y ejecuta las funciones cuando se producen los efectos desencadenantes. La llamada a un método `JobHost` permite indicar si desea ejecutar el proceso del contenedor en el subproceso actual o en un subproceso de segundo plano. En el ejemplo, el método `RunAndBlock` ejecuta el proceso de forma continua en el subproceso actual.

Dado que el método `ProcessQueueMessage` de este ejemplo contiene un atributo `QueueTrigger`, el desencadenador de esa función es la recepción de un nuevo mensaje de cola. El objeto `JobHost` inspecciona los mensajes de cola nuevos de la cola especificada ("colatrabajosweb" en este ejemplo) y cuando encuentra uno, llama a `ProcessQueueMessage`. El atributo `QueueTrigger` también notifica el marco de trabajo para enlazar el parámetro `inputText` al valor del mensaje de cola:

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;colatrabajosweb&quot;)]] string inputText, 
    [Blob(&quot;nombrecontenedor/nombreblob&quot;)]TextWriter writer)
</pre>

El marco de trabajo también enlaza un objeto `TextWriter` a un blob llamado "nombreblob" en un contenedor llamado "nombrecontenedor":

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;colatrabajosweb&quot;)]] string inputText, 
    [Blob(&quot;nombrecontenedor/nombreblob&quot;)]TextWriter writer)
</pre>

A continuación, la función usa estos parámetros para escribir el valor del mensaje de cola en el blob:

        writer.WriteLine(inputText);

Las características de desencadenamiento y enlace del SDK de Trabajos web simplifican enormemente el código que se debe escribir para trabajar con colas de Almacenamiento de Azure y Bus de servicio. El marco de trabajo del SDK de WebJobs se encarga de escribir el código de bajo nivel necesario para controlar el procesamiento de colas y de blobs, es decir, el marco de trabajo crea colas que no existen aún, abre la cola, lee sus mensajes, los elimina cuando se ha completado el procesamiento, crea contenedores de blobs que no existen aún, escribe en los blobs, etc.

El SDK de WebJobs ofrece diversas formas de trabajar con Almacenamiento de Azure. Por ejemplo, si el parámetro que representa con el atributo `QueueTrigger` es una matriz de bytes o un tipo personalizado, se deserializa automáticamente desde el JSON. Además, puede usar un atributo `BlobTrigger` para desencadenar un proceso cada vez que se cree un blob nuevo en su cuenta de Almacenamiento de Azure. (Tenga en cuenta que mientras que `QueueTrigger` encuentra nuevos mensajes de cola en tan solo unos segundos, `BlobTrigger` puede tardar hasta 20 minutos en detectar un blob nuevo. `BlobTrigger` busca blobs siempre que se inicia `JobHost` y, después, comprueba periódicamente los registros de Almacenamiento de Azure para detectar blobs nuevos.)

## <span id="workerrole"></span></a>Uso del SDK de Trabajos web fuera de Trabajos web

Un programa que utiliza el SDK de Trabajos web es una aplicación de consola estándar y se puede ejecutar en cualquier lugar, no tiene que ejecutarse como Trabajo web. Puede probar el programa localmente en su equipo de desarrollo, y en producción puede ejecutarlo en un rol de trabajo de Servicio en la nube o un servicio Windows si prefiere uno de estos entornos.

No obstante, el panel solo está disponible como una extensión de un sitio web de Azure. Si desea ejecutarlo fuera de un Trabajo web y seguir utilizando el Panel, puede configurar un sitio web de Azure para que utilice la misma cuenta de almacenamiento a la que hace referencia la cadena de conexión del Panel del SDK de Trabajos web, y el Panel de Trabajos web de ese sitio mostrará entonces información acerca de la ejecución de la función de su programa que se está ejecutando fuera. Puede consultar el Panel mediante la URL https://*{nombresitioweb}*.scm.azurewebsites.net/azurejobs/\#/functions. Para obtener más información, consulte [cómo obtener un panel para desarrollo local con el SDK de Trabajos web][cómo obtener un panel para desarrollo local con el SDK de Trabajos web], pero tenga en cuenta que la entrada del blog muestra un nombre de cadena de conexión antiguo.

## <span id="nostorage"></span></a>Uso del SDK de Trabajos web para invocar cualquier función

El SDK de Trabajos web proporciona varias ventajas, incluso si no necesita trabajar directamente con colas, tablas o blobs de Almacenamiento de Azure o colas del Bus de servicio:

-   Puede invocar funciones desde el Panel.
-   Puede reproducir funciones desde el Panel.
-   Puede ver registros en el Panel, vinculados al Trabajo web particular (registros de aplicación) o vinculados a la invocación de función particular que los generó (registros de parámetro `TextWriter`).

-   Para obtener más información, consulte [Invocación manual de una función][Invocación manual de una función] y [Escritura de registros][Escritura de registros].

## <span id="nextsteps"></span></a>Pasos siguientes

Para obtener más información acerca del SDK de Trabajos web, consulte [Recursos recomendados de Trabajos web de Azure][Recursos recomendados de Trabajos web de Azure].

  [Información general]: #overview
  [Escenarios]: #scenarios
  [Ejemplos de código]: #code
  [Uso del SDK de Trabajos web fuera de Trabajos web]: #workerrole
  [Uso del SDK de Trabajos web para invocar cualquier función]: #nostorage
  [Pasos siguientes]: #nextsteps
  [Trabajos web]: /es-es/documentation/articles/web-sites-create-web-jobs/
  [tutorial de introducción al SDK de Trabajos web]: ../websites-dotnet-webjobs-sdk-get-started/
  [envío de correos electrónicos]: https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure
  [cómo obtener un panel para desarrollo local con el SDK de Trabajos web]: http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx
  [Invocación manual de una función]: ../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual
  [Escritura de registros]: ../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs
  [Recursos recomendados de Trabajos web de Azure]: http://go.microsoft.com/fwlink/?linkid=390226
