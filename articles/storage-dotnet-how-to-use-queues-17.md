<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Uso del servicio de almacenamiento en cola
==========================================

[versión 1.7](/en-us/develop/net/how-to-guides/queue-service-v17/ "versión 1.7") [versión 2.0](/en-us/develop/net/how-to-guides/queue-service/ "versión 2.0")

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos se escriben en código C\# y usan la API .NET. Entre los escenarios descritos se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de los mensajes en cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [¿Qué es el almacenamiento en cola?](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Configuración de una cadena de conexión de almacenamiento de Azure](#setup-connection-string)
-   [Acceso a las colas mediante programación con .NET](#access)
-   [Creación de una cola](#create-queue)
-   [Inserción de un mensaje en una cola](#insert-message)
-   [Inspección del siguiente mensaje](#peek-message)
-   [Cambio de contenido de un mensaje en cola](#change-contents)
-   [Extracción del siguiente mensaje de la cola](#get-message)
-   [Aprovechamiento de las opciones adicionales de los mensajes quitados de la cola](#advanced-get)
-   [Obtención de la longitud de la cola](#get-queue-length)
-   [Eliminación de una cola](#delete-queue)
-   [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Creación de una cuentaCreación de una cuenta de almacenamiento de Azure
-----------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configuración de una cadena de conexiónConfiguración de una cadena de conexión de almacenamiento de Azure
---------------------------------------------------------------------------------------------------------

La API de almacenamiento para .NET de Azure admite el uso de una cadena de conexión de almacenamiento para configurar extremos y credenciales a fin de obtener acceso a los servicios de almacenamiento. Puede poner la cadena de conexión de almacenamiento en un archivo de configuración en vez de codificarla de forma rígida:

-   Si utiliza los servicios en la nube de Azure, es recomendable que almacene la cadena de conexión mediante el sistema de configuración de servicios de Azure (archivos `*.csdef` y `*.cscfg`).
-   Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión usando el sistema de configuración .NET (por ejemplo, el archivo `web.config`).

En cualquiera de los dos casos, puede recuperar la cadena de conexión utilizando el método `CloudConfigurationManager.GetSetting`, como se indica más adelante en esta guía.

### Configuración de la cadena de conexión si utiliza los servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el Portal de administración de Azure sin volver a implementar la aplicación.

Siga estos pasos para configurar la cadena de conexión mediante la configuración de servicios de Azure:

1.  En el Explorador de soluciones de Visual Studio, en la carpeta **Roles** del proyecto de implementación de Azure, haga clic con el botón secundario en su rol web o de trabajo y, a continuación, haga clic en **Propiedades**.

     ![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  Haga clic en la pestaña **Configuración** y, a continuación, en el botón **Agregar configuración**.

     ![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    Entonces aparecerá una nueva entrada, **Setting1**, en la cuadrícula de configuración.

3.  En el menú desplegable **Tipo** de la nueva entrada **Setting1**, elija **Cadena de conexión**.

     ![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  Haga clic en el botón **...** situado en el extremo derecho de la entrada **Setting1**. Se abrirá el cuadro de diálogo **Cadena de conexión de cuenta de almacenamiento**.

5.  Elija si desea utilizar el emulador de almacenamiento (almacenamiento de Azure simulado en su equipo local) o una cuenta de almacenamiento real en la nube. El código de esta guía funciona con cualquiera de estas opciones. Escriba el valor **Clave de acceso primaria** copiado del paso anterior de este tutorial si desea almacenar los datos de blobs en la cuenta de almacenamiento que se creó anteriormente en Azure.

    ![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  Cambie el contenido del campo **Nombre** de **Setting1** a algo más intuitivo, como **CadenaConexiónAlmacenamiento**. Más adelante en esta guía hará referencia a esta cadena de conexión.

    ![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales

Al usar Sitios web o Máquinas virtuales, es recomendable que utilice el sistema de configuración .NET (por ejemplo, `web.config`). La cadena de conexión se almacena utilizando el elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Consulte [Configurar las cadenas de conexión de Azure](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx) para obtener más información acerca de las cadenas de conexión de almacenamiento.

Ahora está preparado para realizar los procedimientos de esta guía.

Acceso mediante programaciónAcceso a las colas mediante programación con .NET
-----------------------------------------------------------------------------

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\# en el que desee obtener acceso al almacenamiento de Azure mediante programación:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Puede usar el tipo **CloudStorageAccount** y el tipo **CloudConfigurationManager** para recuperar la información de la cuenta de almacenamiento y la cadena de conexión de almacenamiento de la configuración del servicio de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Creación de una colaCreación de una cola
----------------------------------------

Los objetos **CloudQueueClient** le permiten obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. Todo el código que contiene esta guía utiliza una cadena de conexión de almacenamiento almacenada en la configuración de servicios de la aplicación de Azure. Además, existen otros métodos de creación de un objeto **CloudStorageAccount**. Consulte la documentación de [CloudStorageAccount](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx) para obtener más información.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilice el objeto **queueClient** para obtener una referencia a la cola que desea utilizar. En caso de que la cola no exista todavía, es posible crearla:

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Crear la cola si todavía no existe
    queue.CreateIfNotExist();

Inserción de un mensajeInserción de un mensaje en una cola
----------------------------------------------------------

Para insertar un mensaje en una cola existente, cree en primer lugar un nuevo **CloudQueueMessage**. A continuación, llame al método **AddMessage**. Un **CloudQueueMessage** se puede crear a partir de una cadena (en formato UTF-8) o de una matriz de **bytes**. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo".

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Crear la cola si todavía no existe
    queue.CreateIfNotExist();

    // Crear un mensaje y agregarlo a la cola
    CloudQueueMessage message = new CloudQueueMessage("Hola, mundo");
    queue.AddMessage(message);

Inspección del siguiente mensajeInspección del siguiente mensaje
----------------------------------------------------------------

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **PeekMessage**.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Inspeccionar el siguiente mensaje
    CloudQueueMessage peekedMessage = queue.PeekMessage();

Cambio del contenido de un mensajeCambio del contenido de un mensaje en cola
----------------------------------------------------------------------------

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible inmediatamente
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Extracción del siguiente mensaje de la colaExtracción del siguiente mensaje de la cola
--------------------------------------------------------------------------------------

El código extrae un mensaje de una cola en dos pasos. Si llama a **GetMessage**, obtiene el siguiente mensaje en una cola. Un mensaje devuelto por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **DeleteMessage**. Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **DeleteMessage** justo después de que se haya procesado el mensaje.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
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

Más opciones de eliminación de la colaAprovechamiento de las opciones adicionales de los mensajes quitados de la cola
---------------------------------------------------------------------------------------------------------------------

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **GetMessages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **foreach**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que los 5 minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los 5 minutos desde la llamada a **GetMessages**, todos los mensajes que no se han eliminado volverán a estar visibles.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Procesar todos los mensajes en menos de 5 minutos y eliminar cada mensaje después del procesamiento.
        queue.DeleteMessage(message);
    }

Obtención de la longitud de la colaObtención de la longitud de la cola
----------------------------------------------------------------------

Puede obtener una estimación del número de mensajes existentes en una cola. El método **RetrieveApproximateMessageCount** pide al servicio de cola que cuente los mensajes que hay en una cola. El recuento solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **RetrieveApproximateMessageCount**, sin llamar al servicio de cola.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Recuperar el número aproximado de mensajes
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Recuperar el número aproximado de mensajes en caché
    int? cachedMessageCount = queue.ApproximateMessageCount;

Eliminación de una colaEliminación de una cola
----------------------------------------------

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **Delete** en el objeto de cola.

    // Recupere la cuenta de almacenamiento de cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Crear el cliente de cola
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar una referencia a una cola
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Eliminar la cola
    queue.Delete();

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento en cola, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la documentación de referencia del servicio de cola para obtener información detallada acerca de las API disponibles:
    -   [Referencia a la biblioteca de clases de .NET](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Referencia de la API REST](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Obtenga información acerca de las tareas más avanzadas que se pueden realizar con el almacenamiento de Azure en [Almacenamiento](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Consulte más guías de características para obtener información acerca de otras opciones del almacenamiento de datos en Azure.
    -   Utilice [Almacenamiento de tablas](/en-us/develop/net/how-to-guides/table-services/) para almacenar datos estructurados.
    -   Utilice [Almacenamiento de blobs](/en-us/develop/net/how-to-guides/blob-storage/) para almacenar datos no estructurados.
    -   Utilice [Base de datos SQL](/en-us/develop/net/how-to-guides/sql-database/) para almacenar datos relacionales.

