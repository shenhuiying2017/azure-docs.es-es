<properties 
	pageTitle="Cómo trabajar con almacenamiento de cola de Azure mediante el SDK de WebJobs" 
	description="Aprenda como usar el almacenamiento de cola de Azure con el SDK de WebJobs. Cree y elimine colas; inserte, inspeccione, obtenga y elimine mensajes de la cola y mucho más." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Cómo trabajar con almacenamiento de cola de Azure mediante el SDK de WebJobs

Esta guía proporciona ejemplos de código en C# que muestran cómo usar la versión 1.x del SDK de WebJobs de Azure con el servicio de almacenamiento de cola de Azure.

En la guía se supone que conoce [cómo crear un proyecto de WebJob en Visual Studio con cadenas de conexión que señalen a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md).

La mayoría de fragmentos de código muestran solo las funciones, no el código que crea el objeto `JobHost`, como en este ejemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Tabla de contenido

-   [Cómo activar una función cuando se recibe un mensaje en cola](#trigger)
	- Mensajes en cola de cadena
	- Mensajes de la cola de POCO
	- Funciones de Async
	- Tipos con los que funciona el atributo QueueTrigger
	- Algoritmo de sondeo
	- Varias instancias
	- Ejecución en paralelo
	- Obtener cola o metadatos del mensaje en cola
	- Apagado correcto
-   [Cómo crear un mensaje en cola mientras se procesa un mensaje en cola](#createqueue)
	- Mensajes en cola de cadena
	- Mensajes de la cola de POCO
	- Creación de varios mensajes o funciones asincrónicas
	- Tipos con los que funciona el atributo Queue
	- Uso de atributos del SDK de WebJobs en el cuerpo de una función
-   [Cómo leer y escribir datos BLOB al procesar un mensaje en cola](#blobs)
	- Mensajes en cola de cadena
	- Mensajes de la cola de POCO
	- Tipos con los que funciona el atributo Blob
-   [Cómo administrar los mensajes dudosos](#poison)
	- Control automático de mensajes dudosos
	- Control manual de mensajes dudosos
-   [Cómo establecer las opciones de configuración](#config)
	- Definir cadenas de conexión de SDK en código
	- Configurar QueueTrigger
	- Establecimiento de los valores de los parámetros del constructor del SDK de WebJobs en el código
-   [Cómo desencadenar manualmente una función](#manual)
-   [Cómo escribir registros](#logs)
-   [Pasos siguientes](#nextsteps)

## <a id="trigger"></a>Cómo activar una función cuando se recibe un mensaje en cola

Para escribir una función a la que el SDK de WebJobs llama cuando se recibe un mensaje de la cola, use el atributo `QueueTrigger`. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la cola para sondear. También puede [establecer dinámicamente el nombre de cola](#config).

### Mensajes en cola de cadena

En el ejemplo siguiente, la cola contiene un mensaje de cadena, por lo que se aplica `QueueTrigger` a un parámetro de cadena denominado `logMessage`, que contiene el contenido del mensaje en cola. La función [escribe un mensaje de registro en el panel](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Además de `string`, el parámetro puede ser una matriz de bytes, un objeto `CloudQueueMessage` o un POCO que se defina.

### Mensajes de la cola de POCO [(objeto CRL estándar sin formato](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

En el ejemplo siguiente, el mensaje en cola contiene JSON para un objeto `BlobInformation` que incluye una propiedad `BlobName`. El SDK automáticamente deserializa el objeto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes. Si crea la cola de mensajes en un programa que no use el SDK de WebJobs, puede escribir código similar al del ejemplo siguiente para crear un mensaje de la cola de POCO que el SDK pueda analizar. 

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funciones de Async

La siguiente función async [escribe un registro en el panel](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Las funciones Async pueden tomar un [token de cancelación](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), tal como se muestra en el ejemplo siguiente, que copia un blob. (Para obtener una explicación del marcador de posición `queueTrigger`, consulte la sección [Blobs](#blobs) ).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Tipos con los que funciona el atributo QueueTrigger

Puede usar `QueueTrigger` con los siguientes tipos:

* `string`
* Un tipo POCO serializado como JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Algoritmo de sondeo

El SDK implementa un algoritmo de retroceso aleatorio exponencial para reducir el efecto de sondeo de cola inactiva de los costes de transacción de almacenamiento.  Cuando se encuentra un mensaje, el SDK espera dos segundos y, a continuación, comprueba si hay otro mensaje; cuando no se encuentra ningún mensaje, espera unos cuatro segundos antes de intentarlo de nuevo. Después de varios intentos fallidos para obtener un mensaje de la cola, el tiempo de espera sigue aumentando hasta que alcanza el tiempo de espera máximo, predeterminado en un minuto. [El tiempo de espera máximo es configurable](#config).

### <a id="instances"></a> Varias instancias

Si su sitio web se ejecuta en varias instancias, un WebJob continuo se ejecuta en cada equipo y cada equipo esperará a los desencadenadores e intentará ejecutar funciones. En algunos escenarios puede ocurrir que algunas funciones procesen los mismos datos dos veces, por lo que las funciones deben ser idempotentes (escritas de tal forma que al llamarlas repetidamente con los mismos datos de entrada no se generen resultados duplicados).  

### <a id="parallel"></a> Ejecución en paralelo

Si tiene varias funciones escuchando en diferentes colas, el SDK los llamará en paralelo cuando se reciben mensajes simultáneamente. 

Esto también se da cuando se reciben varios mensajes de una sola cola. De forma predeterminada, el SDK obtiene un lote de 16 mensajes de la cola a la vez y ejecuta la función que se procesa en paralelo. [El tamaño del lote es configurable](#config). Cuando el número que se está procesando llegue a la mitad del tamaño del lote, el SDK obtiene otro lote y empieza a procesar los mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es uno y medio por el tamaño del lote. Este límite se aplica por separado a cada función que tiene un atributo `QueueTrigger`. Si no desea la ejecución en paralelo de los mensajes en una cola, establezca el tamaño del lote en 1.

### <a id="queuemetadata"></a>Obtención de una cola o los metadatos del mensaje de una cola

Puede obtener las siguientes propiedades de mensaje agregando parámetros a la firma del método:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contiene texto del mensaje)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Si desea trabajar directamente con la API de almacenamiento de Azure, también puede agregar un parámetro  `CloudStorageAccount`.

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

Una función que se ejecuta en un WebJob continuo puede aceptar un parámetro  `CancellationToken`, que permite al sistema operativo notificar a la función cuándo WebJob está a punto de terminar. Puede utilizar esta notificación para asegurarse de que la función no se termina inesperadamente en una forma que deje los datos en un estado incoherente.

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

## <a id="createqueue"></a>Cómo crear un mensaje en la cola mientras se procesa un mensaje en la cola

Para escribir una función que crea un nuevo mensaje en cola, use el atributo `Queue`. Al igual que `QueueTrigger`, pase el nombre de la cola como una cadena, o también puede [establecer dinámicamente el nombre de cola](#config).

### Mensajes en cola de cadena

En el ejemplo de código no asincrónico siguiente se crea un nuevo mensaje de cola en la cola llamada "outputqueue", con el mismo contenido que el mensaje de cola recibido en la cola llamada "inputqueue". (Para las funciones asincrónicas, use `IAsyncCollector<T>` tal y como se muestra más adelante en esta sección).


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### Mensajes de la cola de POCO [(objeto CRL estándar sin formato](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para crear un mensaje de cola que contenga un objeto POCO en lugar de una cadena, pase el tipo POCO como un parámetro de salida al constructor de atributo  `Queue`.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

El SDK serializa automáticamente el objeto en JSON. Siempre se crea un mensaje en cola, incluso si el objeto es null.

### Creación de varios mensajes o funciones asincrónicas

Para crear varios mensajes, convierta el tipo de parámetro para la cola de salida `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo siguiente.

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

### Tipos con los que funciona el atributo Queue

Puede usar el atributo `Queue` en los siguientes tipos de parámetros:

* `out string` (crea mensajes de cola si el valor del parámetro es distinto de null cuando finaliza la función)
* `out byte[]` (funciona como `string`) 
* `out CloudQueueMessage` (funciona como `string`) 
* `out POCO` (un tipo serializable, crea un mensaje con un objeto null si el parámetro es null cuando finaliza la función)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (para crear mensajes manualmente directamente con la API de almacenamiento de Azure)

### <a id="ibinder"></a>Uso de atributos del SDK de WebJobs en el cuerpo de una función

Si necesita realizar algún trabajo en la función antes de usar un atributo del SDK de WebJobs como `Queue`, `Blob` o `Table`, puede utilizar la interfaz `IBinder`.

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

La interfaz `IBinder` también puede usarse con los atributos `Table` y `Blob`.

## <a id="blobs"></a>Cómo leer y escribir los blobs y tablas al procesar un mensaje en cola

Los atributos `Blob` y `Table` le permiten leer y escribir los blobs y tablas. Los ejemplos en esta sección se aplican a los blobs. Para los ejemplos de código que muestran cómo desencadenar procesos cuando se crean o actualizan los blobs, consulte [Cómo trabajar con almacenamiento de blobs de Azure mediante el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), y para obtener ejemplos de código que leen y escriben las tablas, consulte[Cómo trabajar con almacenamiento de tablas de Azure mediante el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Mensajes de la cola de cadena que desencadenan operaciones de blobs

Para un mensaje en cola que contenga una cadena, `queueTrigger` es un marcador de posición que se puede utilizar en el `Blob` del parámetro `blobPath` del atributo que incluye el contenido del mensaje. 

En el siguiente ejemplo se usan objetos `Stream` para leer y escribir blobs. El mensaje de cola es el nombre de un blob que se encuentra en el contenedor textblobs. Se creará una copia del blob con "-new" anexado al nombre en el mismo contenedor. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

El constructor del atributo `Blob` toma un parámetro `blobPath` que especifica el nombre del blob y el contenedor. Para obtener más información acerca de este marcador de posición, consulte [Cómo trabajar con almacenamiento de blobs de Azure mediante el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), 

Cuando el atributo decora un objeto `Stream`, otro parámetro de constructor especifica el modo de `FileAccess` como lectura, escritura o lectura/escritura. 

En el ejemplo siguiente se usa un objeto `CloudBlockBlob` para borrar un blob. El mensaje de cola es el nombre del blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> Mensajes de la cola de POCO [(objeto CRL estándar sin formato](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Para un POCO almacenado como JSON en el mensaje de cola, puede usar marcadores de posición que asigne nombres a las propiedades del objeto en el parámetro `blobPath` del atributo `Queue`. También puede usar los [nombres de propiedad de metadatos de cola](#queuemetadata) como marcadores de posición. 

En el siguiente ejemplo se copia un blob en un nuevo blob con una extensión diferente. El mensaje de la cola es un objeto `BlobInformation` que incluye las propiedades `BlobName` y `BlobNameWithoutExtension`. Los nombres de propiedad se usan como marcadores de posición en la ruta de acceso de blob para los atributos `Blob`. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

El SDK usa el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar los mensajes. Si crea la cola de mensajes en un programa que no use el SDK de WebJobs, puede escribir código similar al del ejemplo siguiente para crear un mensaje de la cola de POCO que el SDK pueda analizar.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Si necesita realizar algún trabajo en la función antes de enlazar un blob a un objeto, puede usar el atributo en el cuerpo de la función, [como se ha mostrado anteriormente para el atributo de cola](#ibinder).

### <a id="blobattributetypes"></a> Tipos que puede usar con el atributo Blob
 
El atributo `Blob` puede usarse con los siguientes tipos:

* `Stream` (lectura o escritura, especificado mediante el parámetro del constructor FileAccess)
* `TextReader`
* `TextWriter`
* `string` (read)
* `out string` (escritura; crea un blob solo si el parámetro de cadena no es null cuando la función vuelve)
* POCO (lectura)
* out POCO (escritura; siempre crea un blob, crea un objeto null si el parámetro POCO es null cuando la función vuelve)
* `CloudBlobStream` (write)
* `ICloudBlob` (lectura o escritura)
* `CloudBlockBlob` (lectura o escritura) 
* `CloudPageBlob` (lectura o escritura) 

## <a id="poison"></a> Cómo administrar los mensajes dudosos

Los mensajes cuyo contenido produce un error de una función se denominan  *poison messages*. Cuando se produce un error en la función, el mensaje de la cola no se elimina y se recoge de nuevo, provocando que el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones, o puede hacerlo usted manualmente.

### Control automático de mensajes dudosos

El SDK llamará a una función hasta 5 veces para procesar un mensaje de la cola. Si se produce un error en el quinta intento, el mensaje se mueve a una cola de mensajes dudosos. [El número máximo de reintentos es configurable](#config). 

La cola dudosa se denomina *{originalqueuename}*-poison. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación de que se requiere atención manual. 

En el siguiente ejemplo, la función `CopyBlob` generará un error cuando un mensaje en cola contenga el nombre de un blob que no existe. Cuando esto ocurre, el mensaje se mueve desde la cola de copyblobqueue a la cola copyblobqueue-poison. A continuación, el `ProcessPoisonMessage` registra el mensaje dudoso.

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

Puede obtener el número de veces que se ha seleccionado un mensaje para su procesamiento si agrega un parámetro `int` denominado `dequeueCount` a su función. Puede consultar el recuento de eliminación de cola en el código de función y realizar su propio control de mensajes dudosos cuando el número supere un umbral, tal y como se muestra en el ejemplo siguiente.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a> Cómo establecer las opciones de configuración

Puede usar el tipo `JobHostConfiguration` para establecer las opciones de configuración siguientes:

* Establecimiento de las cadenas de conexión de SDK en el código.
* Configuración de los ajustes de `QueueTrigger`, como el contador de eliminación de cola máximo.
* Obtención de los nombres de cola de la configuración.

### <a id="setconnstr"></a>Definición de las cadenas de conexión de SDK en código

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

### <a id="configqueue"></a>Configuración de los ajustes de QueueTrigger

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
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a>Definición de los valores de los parámetros del constructor del SDK de WebJobs en el código

En ocasiones, querrá especificar un nombre de cola, un nombre de blob o contenedor, o un nombre de tabla en lugar de codificarlo de forma rígida. Por ejemplo, quizá desee especificar el nombre de la cola de `QueueTrigger` en un archivo de configuración o una variable de entorno. 

Puede hacerlo pasando un objeto `NameResolver` al tipo `JobHostConfiguration`. Incluirá marcadores de posición especiales rodeados de signos de porcentaje (%) en parámetros del constructor de atributos del SDK de WebJobs y su código `NameResolver` especificará los valores reales en lugar de los marcadores de posición.

Por ejemplo, suponga que desea usar una cola denominada logqueuetest en el entorno de prueba y un logqueueprod denominado en producción. En lugar de un nombre de cola codificado de forma rígida, desea especificar el nombre de una entrada en la colección `appSettings` que tenga el nombre de cola real. Si la clave `appSettings` es logqueue, su función podría ser similar al ejemplo siguiente.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La clase `NameResolver` puede entonces obtener el nombre de cola de `appSettings`, tal y como se muestra en el ejemplo siguiente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Pasará la clase `NameResolver` al objeto `JobHost`, tal y como se muestra en el ejemplo siguiente.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Nota:** los nombres de blobs, tablas y colas se resuelven cada vez que se llama a una función, pero los nombres de contenedor de blob se resuelven solamente cuando se inicia la aplicación. No puede cambiar el nombre del contenedor de blobs mientras se ejecuta el trabajo.

## <a id="manual"></a>Cómo desencadenar manualmente una función

Para desencadenar manualmente una función, use los métodos `Call` o `CallAsync` en el objeto `JobHost` y el atributo `NoAutomaticTrigger` en la función, tal y como se muestra en el ejemplo siguiente. 

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

El Panel muestra los registros en dos lugares: la página del WebJob y la página de una invocación particular de WebJob. 

![Logs in WebJob page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

El resultado de los métodos de consola a los que llama en una función o en el método `Main()` aparece en la página Panel del WebJob, pero no en la página de una invocación de método concreto. El resultado del objeto TextWriter que obtiene de un parámetro en la firma de su método aparece en la página Panel de una invocación de método.

El resultado de la consola no se puede vincular a una invocación de método particular, porque la Consola tiene un único subproceso, aunque muchas de las funciones de trabajo pueden estar en ejecución al mismo tiempo. Es por eso que el SDK proporciona cada invocación de función con su propio objeto de escritura de registro único.

Para escribir [registros de seguimiento de la aplicación](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), use `Console.Out` (crea registros marcados como INFO) y `Console.Error` (crea registros marcados como ERROR). Una alternativa es usar [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que proporcionan los niveles Verbose, Warning y Critical, además de Info y Error. Los registros de seguimiento de la aplicación aparecen en los archivos de registro, las tablas de Azure o los blobs de Azure del sitio web, según la configuración del sitio web de Azure. Como es el caso de los resultados de la consola, los 100 registros de aplicaciones más recientes también aparecen en la página Panel del WebJob, no la página de una invocación de función. 

La salida de la consola aparece en el Panel solo si el programa se ejecuta en un WebJob de Azure, no si el programa se ejecuta de forma local o en otro entorno.

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

En el Panel del SDK de WebJobs, el resultado del objeto  `TextWriter` aparece cuando va a la página de la invocación de una función determinada y hace clic en **Alternar resultados**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

En el Panel de SDK de WebJobs, aparecen las últimas 100 líneas de resultados de la Consola cuando va a la página del WebJob (no de la invocación de función) y hace clic en **Alternar resultados**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

En un WebJob continuo, los registros de aplicación aparecen en /data/jobs/continuous/*{webjobname}*/job_log.txt, en el sistema de archivos del sitio web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

En un blob de Azure, el aspecto de los registros de aplicación es similar al siguiente:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Y en una tabla de Azure los registros `Console.Out` y `Console.Error` tienen este aspecto:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextstepsPasos siguientes"></a>

En esta guía se proporcionan ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con colas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
