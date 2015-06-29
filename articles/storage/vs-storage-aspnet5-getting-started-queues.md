<properties 
	pageTitle="Introducción a Almacenamiento de Azure" 
	description="Cómo empezar a usar el almacenamiento en cola de Azure en un proyecto de ASP.NET en Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Introducción a Almacenamiento de Azure (proyectos ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

El almacenamiento de cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Para obtener más información, consulte [Uso del almacenamiento en cola en .NET](storage-dotnet-how-to-use-queues.md/ "Uso del almacenamiento en cola en .NET").

Para obtener acceso a las tablas en proyectos de ASP.NET 5, deberá agregar los elementos siguientes, si ya no están presentes.

1. Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Utilice el comando siguiente para obtener los valores de configuración.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obtención de la cadena de conexión de almacenamiento
Antes de poder hacer algo con una cola, deberá obtener la cadena de conexión para la cuenta de almacenamiento en la que residen las colas. Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está usando un proyecto ASP.NET 5, puede llamar al método get del objeto Configuration para obtener la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure, como se muestra en el código siguiente.

**NOTA:** las API que realizan llamadas a Almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####de una cola
Los objetos **CloudQueueClient** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. Todo el código que contiene este tema utiliza una cadena de conexión de almacenamiento almacenada en la configuración de servicios de la aplicación de Azure. También hay otras formas de crear un objeto **CloudStorageAccount**. Para obtener más información, consulte la documentación de [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

**NOTA:** las API que realizan llamadas a Almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información. El código siguiente supone que se están utilizando métodos de programación asincrónica.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use el objeto **queueClient** para obtener una referencia a la cola que desea usar. El código intenta hacer referencia a una cola denominada “myqueue”. Si no encuentra ninguna queue con ese nombre, la crea.

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**NOTA:** use todo este código delante del código que aparece en las secciones siguientes.

#####Inserción de un mensaje en una cola
Para insertar un mensaje en una cola existente, cree primero un nuevo objeto **CloudQueueMessage**. A continuación, llame al método AddMessageAsync(). Se puede crear un objeto **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo".

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####Inspección del siguiente mensaje
Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método PeekMessageAsync().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####Eliminación del siguiente mensaje
Su código puede quitar un mensaje de una cola en dos pasos.


1. Llame a GetMessageAsync() para obtener el siguiente mensaje en una cola. Un mensaje devuelto por GetMessageAsync() se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. 
2.	Para terminar de quitar el mensaje de la cola, llame a DeleteMessageAsync(). 

Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código siguiente llama a DeleteMessageAsync() justo después de procesar el mensaje.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Más información acerca de Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/) Consulte también [Explorar recursos de almacenamiento en el Explorador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx) y [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=58_postMigration-->