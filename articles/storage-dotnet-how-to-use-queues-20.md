<properties  linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

# Uso del almacenamiento en cola en .NET

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en código C# y utilizan el cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos, se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de los mensajes en cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

<h2>Tabla de contenido</h2>


* [&iquest;Qué es el almacenamiento en cola?](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#create-account)
* [Configuración de una cadena de conexión de almacenamiento de
  Azure](#setup-connection-string)
* [Acceso a las colas mediante programación con .NET](#access)
* [Creación de una cola](#create-queue)
* [Inserción de un mensaje en una cola](#insert-message)
* [Inspección del siguiente mensaje](#peek-message)
* [Cambio de contenido de un mensaje en cola](#change-contents)
* [Extracción del siguiente mensaje de la cola](#get-message)
* [Aprovechamiento de las opciones adicionales de los mensajes quitados
  de la cola](#advanced-get)
* [Obtención de la longitud de la cola](#get-queue-length)
* [Eliminación de una cola](#delete-queue)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Creación de una cuenta</span>Creación de una cuenta de almacenamiento de Azure</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configuración de una cadena de conexión</span>Configuración de una cadena de conexión de almacenamiento de Azure</h2>


La biblioteca del cliente de almacenamiento de Azure para .NET admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. Puede poner la cadena de conexión de almacenamiento en un archivo de configuración en vez de codificarla de forma rígida:

* Si utiliza los servicios en la nube de Azure, es recomendable que
  almacene la cadena de conexión mediante el sistema de configuración de
  servicios de Azure (archivos `*.csdef` y `*.cscfg`).
* Si utiliza Sitios web Azure, Máquinas virtuales de Azure o crea
  aplicaciones .NET diseñadas para ejecutarse al margen de Azure, es
  recomendable que almacene la cadena de conexión mediante el sistema de
  configuración .NET (por ejemplo, un archivo `web.config` o
  `app.config`).

En ambos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se muestra más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta **Roles** del proyecto de implementación de Azure, haga clic con el botón secundario en su rol web o de trabajo y, a continuación, haga clic en **Propiedades**.
    
    ![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Haga clic en la pestaña **Configuración** y, a continuación, en el botón **Agregar configuración**.
    
    ![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)
    
    Entonces aparecerá una nueva entrada, **Setting1**, en la cuadrícula de configuración.

3.  En el menú desplegable **Tipo** de la nueva entrada **Setting1**, elija **Cadena de conexión**.
    
    ![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Haga clic en el botón **...** situado en el extremo derecho de la entrada **Setting1**. Se abrirá el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

5.  Elija si desea utilizar el emulador de almacenamiento (almacenamiento de Azure simulado en su equipo local) o una cuenta de almacenamiento real en la nube. El código de esta guía funciona con cualquiera de estas opciones. Escriba el valor **Clave de acceso primaria** copiado del paso anterior de este tutorial si desea almacenar los datos de las colas en la cuenta de almacenamiento que se creó anteriormente en Azure.
    
    ![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Cambie el contenido del campo **Nombre** de **Setting1** a algo más intuitivo, como **CadenaConexiónAlmacenamiento**. Más adelante en esta guía hará referencia a esta cadena de conexión.
    
    ![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### Configuración de la cadena de conexión mediante la configuración .NET

Si está desarrollando una aplicación ajena a los servicios en la nube de Azure (consulte la sección anterior), es recomendable que utilice el sistema de configuración .NET (por ejemplo., `web.config` o `app.config`). Esto afecta a las aplicaciones que se ejecutan en los Sitios web Azure y en las Máquinas virtuales de Azure, así como a aquellas diseñadas para ejecutarse al margen de Azure. La cadena de conexión se almacena utilizando el elemento `<appSettings>`, tal y como sigue:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 Consulte [Configurar las cadenas de conexión de Azure][1] para obtener más información acerca de las cadenas de conexión de almacenamiento.

 Ahora está preparado para realizar los procedimientos de esta guía.

 <h2><a name="access"></a><span  class="short-header">Acceso mediante programación</span>Acceso a las colas mediante programación con .NET</h2>


 <h3>Obtención del ensamblado</h3>


 Puede utilizar NuGet para obtener el ensamblado `Microsoft.Azure.Storage.dll`. Haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones**, y elija **Manage NuGet Packages**. Busque "Azure.Storage" en línea y haga clic en **Install** para instalar el paquete y las dependencias de almacenamiento de Azure.

 `Microsoft.Azure.Storage.dll` también se incluye en el SDK de Azure para .NET, que se puede descargar en [.NET Developer Center][2]. El ensamblado se instala en el directorio `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk>\ref\`.

 <h3>Declaraciones de espacio de nombres</h3>


 Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.Azure.Storage;
    using Microsoft.Azure.Storage.Auth;
    using Microsoft.Azure.Storage.Queue;

 Asegúrese de hacer referencia al ensamblado
`Microsoft.Azure.Storage.dll`.

 <h3>Recuperación de la cadena de conexión</h3>


 Puede utilizar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está utilizando una plantilla de proyecto de Azure o hace referencia a Microsoft.Azure.CloudConfigurationManager, puede utilizar el tipo **CloudConfigurationManager** para recuperar la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración de servicios de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Si está creando una aplicación sin ninguna referencia a Microsoft.Azure.CloudConfigurationManager y la cadena de conexión está situada en el archivo `web.config` o `app.config`, como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena. Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>Dependencias ODataLib</h3>


 Las dependencias ODataLib de la biblioteca de clientes de almacenamiento para .NET se resuelven mediante los paquetes ODataLib (versión 5.0.2) disponibles a través de NuGet y no a través de los servicios de datos de WCF. A través de NuGet, es posible descargar directamente las bibliotecas ODataLib o bien hacer referencia a ellas con el código del proyecto. Los paquetes ODataLib específicos son [OData][3], [Edm][4] y
[Spatial][5].

 <h2><a name="create-queue"></a><span  class="short-header">Creación de una cola</span>Creación de una cola</h2>


 Los objetos **CloudQueueClient** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. Todo el código que contiene esta guía utiliza una cadena de conexión de almacenamiento almacenada en la configuración de servicios de la aplicación de Azure. Además, existen otros métodos de creación de un objeto **CloudStorageAccount**. Consulte la documentación de [CloudStorageAccount][6] para obtener más información.

    // Recuperar cuenta de almacenamiento de cadena de conexión
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

 Utilice el objeto **queueClient** para obtener una referencia a la cola que desea utilizar. En caso de que la cola no exista todavía, es posible crearla:

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Crear la cola si todavía no existe
    queue.CreateIfNotExists();

 <h2><a name="insert-message"> </a><span  class="short-header">Inserción de un mensaje</span>Inserción de un mensaje en una cola</h2>


 Para insertar un mensaje en una cola existente, cree en primer lugar un nuevo **CloudQueueMessage**. A continuación, llame al método **AddMessage**. Un **CloudQueueMessage** se puede crear a partir de una cadena (en formato UTF-8) o de una matriz de **bytes**. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo":

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


 Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **PeekMessage**.

    // Recuperar cuenta de almacenamiento de cadena de conexión
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


 Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

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


 El código quita un mensaje de una cola en dos pasos. Si llama a **GetMessage**, obtiene el siguiente mensaje en una cola. Un mensaje devuelto por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **DeleteMessage**. Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **DeleteMessage** justo después de que se haya procesado el mensaje.

    // Recuperar cuenta de almacenamiento de cadena de conexión
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


 Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **GetMessages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **foreach**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que los 5 minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los 5 minutos desde la llamada a **GetMessages**, todos los mensajes que no se han eliminado volverán a estar visibles.

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


 Puede obtener una estimación del número de mensajes existentes en una cola. El método **FetchAttributes** solicita al servicio de cola la recuperación de los atributos de la cola, incluido el número de mensajes. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **FetchAttributes**, sin llamar al servicio de cola.

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


 Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **Delete** en el objeto de cola.

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


 Ahora que está familiarizado con los aspectos básicos del almacenamiento en cola, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

 *  Consulte la documentación de referencia del servicio de cola para obtener información detallada acerca de las API disponibles:
  *  [Referencia acerca de la biblioteca de clientes de almacenamiento para .NET][7]
  *  [Referencia acerca de la API de REST][8]
  

*  Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en [Almacenamiento][9].
*  Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
  *  Utilice [Almacenamiento de tablas](/en-us/develop/net/how-to-guides/table-services/) para almacenar datos estructurados.
  *  Utilice [Almacenamiento de
    blobs](/en-us/develop/net/how-to-guides/blob-storage/) para almacenar datos no estructurados.
  *  Utilice [Base de datos
    SQL](/en-us/develop/net/how-to-guides/sql-database/) para almacenar datos relacionales.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx