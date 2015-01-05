<properties urlDisplayName="Queue Service" pageTitle="Uso del almacenamiento de colas en .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Uso del almacenamiento en cola en .NET
 Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en código C# y utilizan el cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de mensajes de la cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es biblioteca de cliente de almacenamiento .NET de Azure 4.x, que se encuentra disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/en-us/downloads/). Consulte [Acceso al almacenamiento de cola mediante programación][] a continuación para obtener más información acerca de la obtención de una biblioteca de cliente de almacenamiento.

<h2>Tabla de contenido</h2>

-   [¿Qué es el almacenamiento en cola?][]
-   [Conceptos][]
-   [Creación de una cuenta de almacenamiento de Azure][]
-   [Configuración de una cadena de conexión de almacenamiento de Azure][]
-   [Acceso al almacenamiento de cola mediante programación][]
-   [Creación de una cola][]
-   [Inserción de un mensaje en una cola][]
-   [Inspección del siguiente mensaje][]
-   [Cambio del contenido de un mensaje en cola][]
-   [Quitar el siguiente mensaje de la cola][]
-   [Usar opciones adicionales para quitar mensajes de la cola][]
-   [Obtención de la longitud de la cola][]
-   [Eliminación de una cola][]
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a>Crear una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Reproducción de almacenamiento de cola mediante programación

<h3>Obtención del ensamblado</h3>
Puede utilizar NuGet para obtener el ensamblado "Microsoft.WindowsAzure.Storage.dll". Haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Administrar paquetes de NuGet**.  Busque "WindowsAzure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de Almacenamiento de Azure.

"Microsoft.WindowsAzure.Storage.dll" también se incluye en el SDK de Azure para .NET, que se puede descargar desde el <a href="http://www.windowsazure.com/en-us/develop/net/#">Centro de desarrolladores de .NET</a>. El ensamblado se instala en el directorio "%Archivos de programa%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\".

<h3>Declaraciones de espacio de nombres</h3>
Agregue las siguientes declaraciones de espacio de nombres en la parte superior de cualquier archivo de C\#
en el que desea tener acceso al Almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Asegúrese de hacer referencia al ensamblado "Microsoft.WindowsAzure.Storage.dll".

<h3>Recuperación de la cadena de conexión</h3>
Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está utilizando una plantilla de proyecto de Azure o hace referencia a Microsoft.Azure.CloudConfigurationManager, puede utilizar el tipo **CloudConfigurationManager** para recuperar la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.Azure.CloudConfigurationManager y la cadena de conexión está situada en el archivo "web.config" o  "app.config", como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena.  Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependencias ODataLib</h3>
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF.  A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto.  Los paquetes ODataLib específicos son [OData], [Edm] y [Spatial].

<h2><a name="create-queue"></a>Reproducción de una cola</h2>

Los objetos **CloudQueueClient** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. Todo el código que contiene esta guía utiliza una cadena de conexión de almacenamiento almacenada en la
configuración de servicios de la aplicación de Azure. Además, existen otros métodos de creación de objetos
**CloudStorageAccount**. Consulte la documentación de [CloudStorageAccount][]
para obtener información detallada.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use the **queueClient** object to get a reference to the queue you want
to use. You can create the queue if it doesn't exist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>Reproducción de un mensaje en una cola</h2>

Para insertar un mensaje en una cola existente, primero debe crear un nuevo objeto
**CloudQueueMessage**. A continuación, llame al método **AddMessage**. Un objeto
**CloudQueueMessage** se puede crear desde una cadena (en formato UTF-8
) o desde una matriz de **bytes**. Aquí se presenta el código que crea una cola (si
no existe) e inserta el mensaje "Hola, mundo":

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a>Inspección del siguiente mensaje</h2>

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo
de la cola, mediante una llamada al método **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a>Reproducción de contenido de un mensaje en cola</h2>

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a>Reproducción de siguiente mensaje de la cola</h2>

El código quita un mensaje de una cola en dos pasos. Si llama a **GetMessage**, obtiene el siguiente mensaje en una cola. Un mensaje devuelto por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes
de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **DeleteMessage**. Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **DeleteMessage** justo después de que se haya procesado el mensaje.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a>Reproducción de las opciones adicionales de los mensajes quitados de la cola</h2>

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **GetMessages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **foreach**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que los 5 minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los 5 minutos desde la llamada a **GetMessages**, todos los mensajes que no se han eliminado volverán a estar visibles.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a>Reproducción de la longitud de la cola</h2>

Puede obtener una estimación del número de mensajes existentes en una cola. El método **FetchAttributes** solicita al servicio de cola la recuperación de los atributos de la cola, incluido el número de mensajes. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **FetchAttributes**, sin llamar al servicio de cola.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a>Reproducción de una cola</h2>

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **Delete** en el objeto de cola.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Pasos siguientes</h2>

Ahora que ha aprendido los conceptos básicos de Almacenamiento de colas, siga estos vínculos
para obtener información sobre cómo hacer tareas de almacenamiento más complejas.

<ul>
<li>Consulte la documentación de referencia del servicio de cola para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/en-us/library/windowsazure/dd179355">Referencia de la API REST</a></li>
  </ul>
</li>
<li>Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en <a href="http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>
<li>Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web Azure en <a href="/en-us/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introducción al SDK de WebJobs de Azure</a>.</li>
<li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Use <a href="/en-us/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li>
    <li>Use <a href="/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/">Almacenamiento de blobs</a> para almacenar datos no estructurados.</li>
    <li>Use <a href="/en-us/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>



  [Pasos siguientes]: #next-steps
  [¿Qué es el almacenamiento en cola?]: #what-is
  [Conceptos]: #concepts
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento de Azure]: #setup-connection-string
  [Acceso al almacenamiento de cola mediante programación]: #configure-access
  [Creación de una cola]: #create-queue
  [Inserción de un mensaje en una cola]: #insert-message
  [Inspección del siguiente mensaje]: #peek-message
  [Cambio de contenido de un mensaje en cola]: #change-contents
  [Quitar el siguiente mensaje de la cola]: #get-message
  [Uso de opciones adicionales para quitar mensajes de la cola]: #advanced-get
  [Obtención de la longitud de la cola]: #get-queue-length
  [Eliminación de una cola]: #delete-queue
  [Descargar e instalar el SDK de Azure para .NET]: /en-us/develop/net/
  [Referencia a la biblioteca de clientes de .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creación de un proyecto de Azure en Visual Studio]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurar cadenas de conexión]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
