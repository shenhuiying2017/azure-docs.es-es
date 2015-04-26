<properties 
	pageTitle="Cómo trabajar con servicio de bus de Azure mediante el SDK de WebJobs" 
	description="Aprenda a usar los temas y las colas del bus de servicio de Azure con el SDK de WebJobs." 
	services="web-sites, service-bus" 
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

# Cómo trabajar con servicio de bus de Azure mediante el SDK de WebJobs

En esta guía se proporcionan ejemplos de código de C# que muestran cómo desencadenar un proceso cuando se crea o se actualiza un blob de Azure. Los ejemplos de código usan la [SDK de WebJobs](websites-dotnet-webjobs-sdk.md)versión 1.x.

En la guía se supone que conoce [cómo crear un proyecto de WebJob en Visual Studio con cadenas de conexión que señalen a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md).

Los fragmentos de código muestran solo las funciones, no el código que crea el objeto `JobHost`, como en este ejemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Tabla de contenido

-   [Requisitos previos](#prerequisites)
-   [Cómo activar una función cuando se recibe un mensaje en cola](#trigger)
-   [Cómo crear la cola de mensajes](#create)
-   [Cómo trabajar con temas de bus de servicio](#topics)
-   [Temas relacionados que se tratan en el artículo de colas de almacenamiento](#queues)
-   [Pasos siguientes](#nextsteps)

## <a id="prerequisites"></a>Requisitos previos

Para trabajar con el bus de servicio debe instalar el paquete de NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) además de los otros paquetes del SDK de WebJobs. 

También debe establecer la cadena de conexión AzureWebJobsServiceBus, junto con las cadenas de conexión de almacenamiento.

## <a id="trigger"></a> Cómo activar una función cuando se recibe un mensaje de la cola de bus de servicio

Para escribir una función a la que el SDK de WebJobs llama cuando se recibe un mensaje de la cola, use el atributo `ServiceBusTrigger`. El constructor de atributo toma un parámetro que especifica el nombre de la cola que se sondeará.

### Cómo funciona ServicebusTrigger

El SDK recibe un mensaje en modo `PeekLock` y llama a `Complete` en el mensaje si la función finaliza correctamente, o llama a `Abandon` si se produce un error en la función. Si la función se ejecuta durante más tiempo que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

El bus de servicio realiza su propio control de la cola de mensajes dudosos, para que no se controle ni se pueda configurar por parte del SDK de WebJobs. 

### Mensaje en cola de cadena

El ejemplo de código siguiente lee un mensaje de la cola que contiene una cadena y escribe la cadena en el panel de SDK de WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Nota:** si va a crear la cola de mensajes en una aplicación que no use el SDK de WebJobs, asegúrese de establecer [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) a "text/plain".

### Mensaje de la cola de POCO

El SDK deserializará automáticamente un mensaje de la cola que contenga JSON para un tipo POCO [(objeto CRL estándar sin formato](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). El ejemplo de código siguiente lee un mensaje de la cola que contenga un objeto `BlobInformation` que tenga una propiedad `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Para los ejemplos de código que muestran cómo utilizar las propiedades del POCO para trabajar con tablas y blobs en la misma función, consulte la [versión de colas de almacenamiento de este artículo](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Tipos con los que trabaja ServiceBusTrigger

Además de `string` y los tipos POCO, puede usar el atributo `ServiceBusTrigger` con una matriz de bytes o un objeto `BrokeredMessage`.

## <a id="create"></a>Cómo crear mensajes de cola de bus de servicio

Para escribir una función que cree un nuevo mensaje de cola, use el atributo `ServiceBus` y pase el nombre de cola al constructor del atributo. 


### Creación de un único mensaje en cola en una función no asincrónica

El ejemplo de código siguiente utiliza un parámetro de salida para crear un nuevo mensaje en la cola con el nombre "outputqueue" y el mismo contenido que el mensaje recibido en la cola llamada "inputqueue".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

El parámetro de salida para la creación de un único mensaje en cola puede ser de cualquiera de los siguientes tipos:

* `string`
* `byte[]`
* `BrokeredMessage`
* Un tipo POCO serializable que defina. Se serializa automáticamente como JSON.

Para los parámetros de tipo POCO, siempre se crea un mensaje de cola cuando finaliza la función; si el parámetro es null, el SDK crea un mensaje en cola que devolverá null cuando se recibe el mensaje y se deserializa. Para otros tipos, si el parámetro es null, no se crea ningún mensaje de cola.

### Creación de varios mensajes de cola o en funciones asincrónicas

Para crear varios mensajes, use el atributo `ServiceBus` con `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo de código siguiente:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensaje de la cola se crea inmediatamente cuando se llama al método `Add`.

## <a id="topics"></a>Cómo trabajar con temas de bus de servicio

Para escribir una función a la que el SDK llame cuando se reciba un mensaje en un tema de bus de servicio, use el atributo `ServiceBusTrigger` con el constructor que toma el nombre del tema y de la suscripción, como se muestra en el ejemplo de código siguiente:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Para crear un mensaje en un tema, use el atributo `ServiceBus` con el nombre de un tema del mismo modo que se usa con un nombre de cola.

## <a id="queues"></a>Temas relacionados tratados en el artículo de procedimientos de las colas de almacenamiento

Para obtener información acerca de los escenarios del SDK de WebJobs que no son específicos del bus de servicio, consulte [Cómo usar el almacenamiento de la cola de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Los temas que se tratan en este artículo incluyen lo siguiente:

* Funciones de Async
* Varias instancias
* Apagado correcto
* Uso de atributos del SDK de WebJobs en el cuerpo de una función
* Establecimiento de las cadenas de conexión de SDK en el código
* Establecimiento de los valores de los parámetros del constructor del SDK de WebJobs en el código
* Activación manual de una función
* Escritura de registros

## <a id="nextstepsPasos siguientes"></a>

En esta guía se proporcionan ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con el bus de servicio de Azure. Para obtener más información acerca de cómo usar WebJobs de Azure y el SDK de WebJobs, consulte [Recursos recomendados de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).





<!--HONumber=42-->
