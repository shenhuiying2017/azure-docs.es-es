<properties urlDisplayName="Queue Service" pageTitle="Uso del almacenamiento de colas en .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Uso del almacenamiento en cola en .NET

Esta guía muestra cómo realizar algunas tareas comunes a través del
servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en código C\#
y usan la biblioteca del cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen **la inserción**,
**inspección**, **obtención** y **eliminación** de los mensajes de la cola, así como la
**creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte
la sección [Pasos siguientes][Pasos siguientes].

> [WACOM.NOTE] Esta guía se destina a la biblioteca de cliente de almacenamiento .NET de Azure 2.x y posterior. La versión recomendada es la biblioteca de cliente de almacenamiento 4.x, que está disponible a través de [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o como parte del [SDK de Azure para .NET](/es-es/downloads/). Consulte [Acceso al almacenamiento de cola mediante programación][Acceso al almacenamiento de cola mediante programación] a continuación para obtener más información acerca de la obtención de una biblioteca de cliente de almacenamiento.

<h2>Tabla de contenido</h2>

-   [¿Qué es el almacenamiento en cola?][¿Qué es el almacenamiento en cola?]
-   [Conceptos][Conceptos]
-   [Creación de una cuenta de almacenamiento de Azure][Creación de una cuenta de almacenamiento de Azure]
-   [Configuración de una cadena de conexión de almacenamiento de Azure][Configuración de una cadena de conexión de almacenamiento de Azure]
-   [Direccionamiento del almacenamiento de cola mediante programación][Direccionamiento del almacenamiento de cola mediante programación]
-   [Direccionamiento del de una cola][Direccionamiento del de una cola]
-   [Direccionamiento del un mensaje en una cola][Direccionamiento del un mensaje en una cola]
-   [Direccionamiento del siguiente mensaje][Direccionamiento del siguiente mensaje]
-   [Direccionamiento del contenido de un mensaje en cola][Direccionamiento del contenido de un mensaje en cola]
-   [Direccionamiento del siguiente mensaje de la cola][Direccionamiento del siguiente mensaje de la cola]
-   [Direccionamiento del las opciones adicionales de los mensajes quitados de la cola][Direccionamiento del las opciones adicionales de los mensajes quitados de la cola]
-   [Direccionamiento del la longitud de la cola][Direccionamiento del la longitud de la cola]
-   [Direccionamiento del una cola][Direccionamiento del una cola]
-   [Pasos siguientes][Pasos siguientes]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a><span  class="short-header">Creación de una cuenta</span>Creación de una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span  class="short-header">Configuración de una cadena de conexión</span>Configuración de una cadena de conexión de almacenamiento de Azure</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Acceso mediante programación</span>Acceso al almacenamiento de cola mediante programación

<h3>Obtención del ensamblado</h3>
.Puede utilizar NuGet para obtener el ensamblado Microsoft.Azure.Storage.dll. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Administrar paquetes de NuGet**.  Busque "Azure.Storage" en línea y haga clic en **Instalar** para instalar el paquete y las dependencias de almacenamiento de Azure.

`Microsoft.Azure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en <a href="http://www.windowsazure.com/es-es/develop/net/#">.NET Developer Center</a>. El ensamblado se instala en el directorio `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Declaraciones de espacio de nombres</h3>
Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\#
en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.Azure.Storage.Queue;

Asegúrese de hacer referencia al ensamblado `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recuperación de la cadena de conexión</h3>
Puede utilizar el tipo **CloudStorageAccount** para representar 
la información de la cuenta de almacenamiento. Si utiliza una 
plantilla de proyecto de Azure o hace referencia a 
Microsoft.WindowsAzure.CloudConfigurationManager, 
puede utilizar el tipo **CloudConfigurationManager**
para recuperar la cadena de conexión de almacenamiento y la información de la cuenta
de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config` como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena.  Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependencias ODataLib</h3>
Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF.  A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto.  Los paquetes ODataLib son [OData], [Edm] y [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Creación de una cola</span>Creación de de una cola</h2>

Un objeto **CloudQueueClient** le permite obtener objetos de referencia para las colas.
El siguiente código crea un objeto **CloudQueueClient**. Todo el código que contiene
esta guía utiliza una cadena de conexión de almacenamiento almacenada en la configuración
de servicios de la aplicación de Azure. Además, existen otros métodos de creación de
un objeto **CloudStorageAccount**. Consulte la documentación de [CloudStorageAccount][CloudStorageAccount]
para obtener más información.

    // Recuperar cuenta de almacenamiento desde cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilice el objeto **queueClient** para obtener una referencia a la cola que desea
utilizar. En caso de que la cola no exista todavía, es posible crearla.

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Crear la cola si todavía no existe
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Inserción de un mensaje</span>Inserción de un mensaje en una cola</h2>

Para insertar un mensaje en una cola existente, primero debe crear un nuevo
**CloudQueueMessage**. A continuación, llame al método **AddMessage**. Se puede crear un
**CloudQueueMessage** desde una cadena (en formato UTF-8)
o desde una **matriz de** bytes. A continuación se muestra el código con el que se crea una cola (si
no existe) e inserta el mensaje 'Hola, mundo':

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Crear la cola si todavía no existe.
    queue.CreateIfNotExists();

    // Crear un mensaje y agregarlo a la cola.
    CloudQueueMessage message = new CloudQueueMessage("Hola, mundo");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Inspección del siguiente mensaje</span>Inspección del siguiente mensaje</h2>

Puede inspeccionar el mensaje situado en la parte delantera de una cola sin quitarlo
de la cola mediante una llamada al método **PeekMessage**.

    // Recuperar cuenta de almacenamiento desde cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Inspeccionar el siguiente mensaje
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Mostrar mensaje.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a><span  class="short-header">Cambio del contenido de un mensaje</span>Cambio del contenido de un mensaje en cola</h2>

Puede cambiar el contenido de un mensaje local en la cola. Si el
mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el
estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola
con contenido nuevo y amplia el tiempo de espera de la visibilidad en 60
segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y
se proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica
se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en cola,
sin que sea necesario volver a empezar desde el principio si se
produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente,
también mantendría un número reintentos y, si el mensaje se intentara más
de *n* veces, lo eliminaría. Esto proporciona información frente a un mensaje
que produce un error en la aplicación cada vez que se procesa.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Obtener el mensaje de la cola y actualizar el contenido del mensaje.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Lo hace visible inmediatamente.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Extracción del siguiente mensaje de la cola</span>Extracción del siguiente mensaje de la cola</h2>

El código quita un mensaje de una cola en dos pasos. Si llama a
**GetMessage**, obtiene el siguiente mensaje en una cola. Un mensaje devuelto
por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes
de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30
segundos. Para terminar de quitar el mensaje de la cola, también debe llamar
a **DeleteMessage**. Este proceso de extracción de un mensaje que consta de dos pasos
garantiza que si su código no puede procesar un mensaje a causa de un error de
hardware o software, otra instancia de su código puede obtener el mismo mensaje
e intentarlo de nuevo. Su código llama a **DeleteMessage** justo después de que se haya
procesado el mensaje.

    // Recuperar cuenta de almacenamiento desde cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Obtener el siguiente mensaje
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Procesar el mensaje en menos de 30 segundos y, a continuación, eliminar el mensaje
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">Más opciones de eliminación de la cola</span>Aprovechamiento de las opciones adicionales de los mensajes quitados de la cola</h2>

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera
de la invisibilidad más largo o más corto, para que el código disponga de más o menos
tiempo para procesar cada mensaje. El ejemplo siguiente utiliza el método
**GetMessages** para obtener 20 en una llamada. A continuación, procesa
cada mensaje con un bucle **foreach**. También se establece el tiempo de espera de
invisibilidad en cinco minutos para todos los mensajes. Tenga en cuenta que los 5 minutos empiezan a contar
para todos los mensajes al mismo tiempo, por lo que después de pasar los 5 minutos
desde la llamada a **GetMessages**, todos los mensajes que no se han eliminado
volverán a estar visibles.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Procesar todos los mensajes en menos de 5 minutos y eliminar cada mensaje después del procesamiento.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Obtención de la longitud de la cola</span>Obtención de la longitud de la cola</h2>

Puede obtener una estimación del número de mensajes existentes en una cola. El método
**FetchAttributes** solicita al servicio de cola
la recuperación de los atributos de la cola, incluido el número de mensajes. La propiedad **ApproximateMethodCount**
devuelve el último valor recuperado por el método
**FetchAttributes**, sin llamar al servicio de cola.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Obtener los atributos de la cola.
	queue.FetchAttributes();

    // Recuperar el número aproximado de mensajes en caché.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Mostrar el número de mensajes.
	Console.WriteLine("Número de mensajes en cola: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Eliminación de una cola</span>Eliminación de una cola</h2>

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método
**Delete** en el objeto de cola.

    // Recuperar cuenta de almacenamiento desde cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Eliminar la cola.
    queue.Delete();

<h2><a name="next-steps"></a>Pasos siguientes</h2>

Ahora que ha aprendido los conceptos básicos del almacenamiento de cola, siga estos vínculos
para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

<ul>
<li>Consulte la documentación de referencia del servicio de cola para obtener información detallada acerca de las API disponibles:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referencia acerca de la biblioteca de clientes de almacenamiento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/es-es/library/windowsazure/dd179355">Referencia de la API REST</a></li>
  </ul>
</li>
<li>Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en <a href="http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx">Almacenamiento de datos y acceso a los mismos en Azure</a>.</li>.
<li>Obtenga información acerca de cómo trabajar con Almacenamiento de Azure en procesos de back-end para Sitios web Azure en <a href="/es-es/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introducción al SDK de WebJobs de Azure</a>.</li>
<li>Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  <ul>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-tables/">Almacenamiento de tablas</a> para almacenar datos estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/">Almacenamiento de blobs</a> para almacenar datos no estructurados.</li>
    <li>Utilice <a href="/es-es/documentation/articles/sql-database-dotnet-how-to-use/">Base de datos SQL</a> para almacenar datos relacionales.</li>
  </ul>
</li>
</ul>



  [Pasos siguientes]: #next-steps
  [¿Qué es el almacenamiento en cola?]: #what-is
  [Conceptos]: Conceptos
  [Creación de una cuenta de almacenamiento de Azure]: #create-account
  [Configuración de una cadena de conexión de almacenamiento de Azure]: #setup-connection-string
  [CloudStorageAccount]: http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
