<properties linkid="" pageTitle="Cómo trabajar con almacenamiento de cola de Azure mediante el SDK de WebJobs"metaKeywords ="almacenamiento de cola de Azure SDK de WebJobs C# .NET" description="Aprenda a trabajar con almacenamiento en cola de Azure en el SDK de WebJobs. Crear y eliminar colas; insertar, inspeccionar, obtener y eliminar mensajes de la cola, etc." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Cómo trabajar con almacenamiento de cola de Azure mediante el SDK de WebJobs

Esta guía muestra cómo escribir código C# para escenarios comunes con el
Servicio de almacenamiento de cola de Azure y Azure WebJobs SDK, versión 1.0.0.

La guía asume que ya conoce [lo que es el SDK de Webjobs](../websites-webjobs-sdk-storage-queues-how-to) y [cómo realizar tareas básicas](../websites-dotnet-webjobs-sdk-get-started/), como instalar el paquete NuGet de SDK de WebJobs, crear una cuenta de almacenamiento de Azure y crear cadenas de conexión para el SDK de WebJobs que señalan a la cuenta de almacenamiento.

La mayoría de los fragmentos de código muestran solo las funciones, no el código que crea el objeto `JobHost` como en este ejemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Tabla de contenido

-   [Cómo activar una función cuando se recibe un mensaje en cola](#trigger)
	- Mensajes en cola de cadena
	- Mensajes en cola de objetos POCO
	- Funciones de Async
	- Algoritmo de sondeo
	- Ejecución en paralelo
	- Obtener cola o metadatos del mensaje en cola
	- Apagado correcto
-   [Cómo crear un mensaje en cola mientras se procesa un mensaje en cola](#createqueue)
	- Mensajes en cola de cadena
	- Mensajes en cola de objetos POCO
	- Crear varios mensajes
	- Usar atributos de la cola en el cuerpo de una función
-   [Cómo leer y escribir datos BLOB al procesar un mensaje en cola](#blobs)
	- Mensajes en cola de cadena
	- Mensajes en cola de objetos POCO
	- Usar atributos de Blob en el cuerpo de una función
-   [Cómo administrar los mensajes dudosos](#poison)
	- Control automático de mensajes dudosos
	- Control manual de mensajes dudosos
-   [Cómo establecer las opciones de configuración](#config)
	- Definir cadenas de conexión de SDK en código
	- Configurar QueueTrigger
	- Obtener nombres de cola de configuración
-   [Cómo desencadenar manualmente una función](#manual)
-   [Cómo escribir registros](#logs)
-   [Pasos siguientes](#nextsteps)

## <a id="trigger"></a> Cómo activar una función cuando se recibe un mensaje en cola

Para escribir una función que el SDK llama cuando se recibe un mensaje de la cola, utilice el atributo `QueueTrigger` con una cadena o un parámetro POCO, dependiendo de lo que espera obtener en el mensaje. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la cola para sondear. También puede [establecer dinámicamente el nombre de cola](#config).

Si su sitio web se ejecuta en varias máquinas virtuales, WebJob se ejecutará en cada equipo y cada equipo esperará para desencadenadores e intenta ejecutar funciones. En algunos escenarios puede ocurrir que algunas funciones procesen los mismos datos dos veces, por lo que las funciones deben ser idempotentes (escritas de tal forma que al llamarlas repetidamente con los mismos datos de entrada no se generen resultados duplicados).  

### Mensajes en cola de cadena

En el ejemplo siguiente, la cola contiene un mensaje de cadena, por lo que `QueueTrigger` se aplica a un parámetro de cadena denominado `logMessage` que contiene el contenido del mensaje en cola. La función [escribe un mensaje de registro en el panel](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Además de `string`, el parámetro puede ser una matriz de bytes, un objeto `CloudQueueMessage` o un objeto POCO que defina.

### Mensajes en cola de objetos POCO

En el ejemplo siguiente, el mensaje en cola contiene JSON para un objeto `BlobInformation` que incluye una propiedad `BlobName`. El SDK automáticamente deserializa el objeto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

En el ejemplo siguiente se muestra cómo crear un mensaje en cola POCO sin usar el SDK de WebJobs, que puede analizar el SDK. El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funciones de Async

La siguiente función async [escribe un registro en el panel](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Las funciones Async pueden tomar un token de cancelación, tal como se muestra en el ejemplo siguiente, que copia un blob. (Para obtener una explicación del marcador de posición `queueTrigger`, consulte la sección [Blobs](#blobs) .)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Algoritmo de sondeo

El SDK implementa un algoritmo de retroceso aleatorio exponencial para reducir el efecto de sondeo de cola inactiva de los costes de transacción de almacenamiento.  Cuando se encuentra un mensaje, el SDK espera dos segundos y, a continuación, comprueba si hay otro mensaje; cuando no se encuentra ningún mensaje, espera unos cuatro segundos antes de intentarlo de nuevo. Después de varios intentos fallidos para obtener un mensaje de la cola, el tiempo de espera sigue aumentando hasta que alcanza el tiempo de espera máximo, predeterminado en un minuto. [El tiempo de espera máximo es configurable](#config).

### Ejecución en paralelo

Si tiene varias funciones escuchando en diferentes colas, el SDK los llamará en paralelo cuando se reciben mensajes simultáneamente. 

Esto también se da cuando se reciben varios mensajes de una sola cola. De forma predeterminada, el SDK obtiene un lote de 16 mensajes de la cola a la vez y ejecuta la función que se procesa en paralelo. [El tamaño del lote es configurable](#config). Cuando el número que se está procesando llegue a la mitad del tamaño del lote, el SDK obtiene otro lote y empieza a procesar los mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es uno y medio por el tamaño del lote. Este límite se aplica por separado para cada función que tiene un atributo `QueueTrigger`. Si no desea la ejecución en paralelo de los mensajes en una cola, establezca el tamaño del lote en 1.

### <a id="queuemetadata"></a>Obtener cola o metadatos del mensaje en cola

Puede obtener las siguientes propiedades de mensaje agregando parámetros a la firma del método:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contiene texto de mensaje)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si desea trabajar directamente con la API de almacenamiento de Azure, también puede agregar un parámetro `CloudStorageAccount`.

En el siguiente ejemplo escribirá todos estos metadatos en un registro de aplicación de la información. En el ejemplo, logMessage y queueTrigger contienen el contenido del mensaje en cola.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Este es un registro de ejemplo escrito por el código de ejemplo:

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>Apagado correcto

Una función que se ejecuta en un WebJob continuo puede aceptar un parámetro `CancellationToken`, que permite al sistema operativo notificar a la función cuándo WebJob está a punto de terminar. Puede utilizar esta notificación para asegurarse de que la función no se termina inesperadamente en una forma que deje los datos en un estado incoherente.

En el ejemplo siguiente se muestra cómo comprobar la finalización de WebJob inminente en una función.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Nota:** el panel no puede mostrar correctamente el estado y la salida de las funciones que se han cerrado.
 
Para obtener más información, consulte [Cierre estable de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Cómo crear un mensaje en cola mientras se procesa un mensaje en cola

Para escribir una función que cree un nuevo mensaje en cola, utilice el atributo `Cola` en una cadena de salida o parámetro POCO. Al igual que `QueueTrigger`, se pasa el nombre de la cola como una cadena, o bien puede [establecer dinámicamente el nombre de cola](#config).

### Mensajes en cola de cadena

En el ejemplo siguiente se crea un nuevo mensaje en cola en la cola llamada "outputqueue", con el mismo contenido que el mensaje en cola recibido en la cola llamada "inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Si el tipo de parámetro de salida es uno de los siguientes tipos y el objeto no es null cuando finaliza la función, el SDK crea un mensaje en cola:

* `string` 
* `byte[]`
* Un tipo serializable POCO que defina
* `CloudQueueMessage`

Para crear varios mensajes, consulte **Crear varios mensajes** más adelante en esta sección.

También puede utilizar `CloudQueue` como el tipo de parámetro de salida si desea enviar mensajes manualmente.

### Mensajes en cola de objetos POCO

Para crear un mensaje de la cola que contiene un objeto POCO en lugar de una cadena, pase el tipo POCO como un parámetro de salida al constructor de atributo `Queue`. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

El SDK serializa automáticamente el objeto en JSON. Siempre se crea un mensaje en cola, incluso si el objeto es null.

### Crear varios mensajes

Para crear varios mensajes, convierta el tipo de parámetro para la cola de salida `ICollector<T>` o `IAsyncCollector`, como se muestra en el siguiente ejemplo.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensaje de la cola se crea inmediatamente cuando se llama al método `Add`.

### <a id="queuebody"></a>Usar atributos de la cola en el cuerpo de una función

Si necesita realizar algún trabajo en la función antes de utilizar el atributo `Queue` para crear un mensaje en cola, puede utilizar la interfaz `IBinder` para obtener un objeto `CloudQueue` que le permita trabajar directamente con una cola. 

En el siguiente ejemplo se toma un mensaje de la cola de entrada y se crea un mensaje nuevo con el mismo contenido en una cola de salida. El nombre de la cola de salida se establece por el código en el cuerpo de la función.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Cómo leer y escribir los blobs y tablas al procesar un mensaje en cola

Los atributos `Blob` y `Table` permiten leer y escribir blobs y tablas. Los ejemplos en esta sección se aplican a los blobs.

Algunos de los tipos con los que se puede usar el atributo `Blob` son `Stream`, `TextReader`, `TextWriter` y `CloudBlockBlob`. El constructor de atributo toma un parámetro `blobPath` que especifica el contenedor y el nombre de blob; y cuando el atributo decora un objeto `Stream`, otro parámetro de constructor especifica el modo `FileAccess` como lectura, escritura o lectura/escritura. Si necesita realizar algún trabajo en la función antes de enlazar un blob a un objeto, puede usar el atributo en el cuerpo de la función, [como se ha mostrado anteriormente para el atributo de cola](#queuebody).

### Mensajes en cola de cadena

Para un mensaje de la cola que contiene una cadena, `queueTrigger` es un marcador de posición que se puede utilizar en el parámetro `blobPath` del atributo `Blob` con el contenido del mensaje. 

En el ejemplo siguiente utiliza objetos `Stream` para leer y escribir blobs. El mensaje en cola proporciona el nombre de un blob que se encuentra en el contenedor textblobs. Se creará una copia del blob con "-new" anexado al nombre en el mismo contenedor. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

En el ejemplo siguiente se utiliza un objeto `CloudBlockBlob` para eliminar un blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Mensajes en cola de objetos POCO

Para objetos POCO almacenados como JSON en el mensaje de la cola, puede utilizar marcadores de posición que denominen propiedades del objeto en el parámetro `blobPath` del atributo `Queue`. También puede usar los [nombres de propiedad de metadatos de cola](#queuemetadata) como marcadores de posición. 

El siguiente ejemplo copia un blob en un nuevo blob con una extensión diferente, mediante propiedades del objeto `BlobInformation` para especificar los nombres de los objetos binarios de entrada y salida. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

En el ejemplo siguiente se muestra cómo crear un mensaje en cola POCO sin usar el SDK de WebJobs, que puede analizar el SDK. El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Cómo administrar los mensajes dudosos

Los mensajes cuyo contenido produce un error de una función se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

### Control automático de mensajes dudosos

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos. [El número máximo de reintentos es configurable](#config). 

La cola de mensajes dudosos se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación de que se requiere atención manual. 

En el siguiente ejemplo, la función `CopyBlob` fallará cuando un mensaje en cola contenga el nombre de un blob que no existe. Cuando esto ocurre, el mensaje se mueve desde la cola de copyblobqueue a la cola copyblobqueue-poison. `ProcessPoisonMessage` registra el mensaje dudoso.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

La ilustración siguiente muestra el resultado de la consola de estas funciones cuando se procesa un mensaje dudoso.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Control manual de mensajes dudosos

Puede obtener el número de veces que se ha detectado un mensaje para el procesamiento agregando un parámetro `int` denominado `dequeueCount` a la función. Puede consultar el recuento de eliminación de cola en el código de función y realizar su propio control de mensajes dudosos cuando el número supere un umbral, tal y como se muestra en el ejemplo siguiente.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Para que este código funcione según lo esperado, tendrá que aumentar el número máximo de reintentos para el control automático de mensajes dudosos o el número de eliminaciones de cola; en este ejemplo, nunca superarían los 5.

## <a id="config"></a> Cómo establecer las opciones de configuración

Puede usar el tipo `JobHostConfiguration` para establecer las opciones de configuración siguientes:

* Establezca las cadenas de conexión de SDK en el código.
* Configure `QueueTrigger` como un recuento de eliminación de cola máximo.
* Obtenga los nombres de cola de la configuración.

### <a id="setconnstr"></a>Definir cadenas de conexión de SDK en código

Configurar las cadenas de conexión de SDK en el código permite utilizar sus propios nombres de cadena de conexión en archivos de configuración o las variables de entorno, como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>Configurar los valores de la cola

Puede configurar los siguientes valores, que se aplican para el procesamiento de mensajes de la cola:

- El número máximo de mensajes en cola que se recogen simultáneamente para ejecutarse en paralelo (el valor predeterminado es 16).
- El número máximo de reintentos antes de enviar un mensaje de la cola a una cola de mensajes dudosos (el valor predeterminado es 5).
- El tiempo de espera máximo antes de volver a sondear cuando una cola está vacía (el valor predeterminado es 1 minuto).

En el ejemplo siguiente se muestra cómo configurar estas opciones:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Obtener nombres de cola de configuración

El tipo `JobHostConfiguration` permite pasar un objeto `NameResolver` que proporciona el nombre de cola al SDK para los atributos `QueueTrigger` y `Queue`.

Por ejemplo, suponga que desea usar una cola denominada logqueuetest en el entorno de prueba y un logqueueprod denominado en producción. En lugar de un nombre de cola codificado de forma rígida, desea especificar el nombre de una entrada en la colección `appSettings` que tenga el nombre de cola real. Si la clave `appSettings` es logqueue, su función podría ser similar al ejemplo siguiente.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La clase `NameResolver` podría obtener el nombre de la cola de `appSettings` como se muestra en el ejemplo siguiente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Pase la clase `NameResolver` en el objeto `JobHost` tal como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Cómo desencadenar manualmente una función

Para desencadenar una función manualmente, utilice el método `Call` o `CallAsync` en el objeto `JobHost` y el atributo `NoAutomaticTrigger` en la función, tal como se muestra en el ejemplo siguiente. 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>Cómo escribir registros

Para escribir los registros que aparecen en la página del panel de WebJobs vinculada a una invocación de función determinada, utilice un objeto `TextWriter` que obtenga de un parámetro en la firma del método.

Para escribir [registros de seguimiento de aplicación](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), utilice `Console.Out` (crea registros marcados como INFO) y `Console.Error` (crea registros marcados como ERROR). Una alternativa es usar [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Los registros de aplicación aparecen en los archivos de registro del sitio web, las tablas de Azure los o blobs de Azure, según cómo configure el sitio web de Azure. Los 100 registros de aplicaciones más recientes aparecen también en el panel si el programa se ejecuta en un WebJob de Azure. (No aparecen registros de aplicación en el panel de un programa que se ejecuta localmente o en otro entorno.)   

Puede deshabilitar el registro [definiendo la cadena de conexión del panel en Null](#config).

En el ejemplo siguiente se muestran varias maneras de escribir registros:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

En el panel de SDK de WebJobs, el resultado del objeto `TextWriter` aparece cuando vaya a la página de una invocación de función determinada y haga clic en **Alternar resultados**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

En el panel de SDK de WebJobs, aparecen las últimas 100 líneas de registros de aplicación cuando vaya a la página de WebJob (no de la invocación de función) y haga clic en **Alternar resultados**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

En un WebJob continuo, los registros de aplicación aparecen en /data/jobs/continuous/*{webjobname}*/job_log.txt en el sistema de archivos del sitio web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

En un blob de Azure, el aspecto de los registros de aplicación es similar al siguiente:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Y en una tabla de Azure, los registros `Console.Out` y `Console.Error` aparecen de esta forma:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Pasos siguientes

En este tema se han proporcionado ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con colas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=35.2-->
