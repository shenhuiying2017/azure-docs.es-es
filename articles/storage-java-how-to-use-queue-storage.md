<properties urlDisplayName="Queue Service" pageTitle="Uso del servicio de colas (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Uso del almacenamiento de colas en Java

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento en cola de Azure. Los ejemplos están escritos en Java y utilizan el [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java]. Entre los escenarios descritos se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de los mensajes en cola, así como la **creación** y **eliminación** de colas. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#NextSteps).

Nota: hay un SDK disponible para los desarrolladores que usen el almacenamiento de Azure en dispositivos Android. Para obtener más información, consulte el [SDK de almacenamiento de Azure para Android][SDK de almacenamiento de Azure para Android]. 

## <a name="Contents"> </a>Tabla de contenido

* [¿Qué es el almacenamiento en cola?](#what-is)
* [Conceptos](#Concepts)
* [Crear una cuenta de almacenamiento de Azure](#CreateAccount)
* [Creación de una aplicación Java](#CreateApplication)
* [Configuración de la aplicación para obtener acceso al almacenamiento en cola](#ConfigureStorage)
* [Configuración de una cadena de conexión de almacenamiento de Azure](#ConnectionString)
* [Direccionamiento del de una cola](#create-queue)
* [Direccionamiento del un mensaje a una cola](#add-message)
* [Direccionamiento del siguiente mensaje](#peek-message)
* [Direccionamiento del contenido de un mensaje en cola](#change-message)
* [Direccionamiento del la longitud de la cola](#get-queue-length)
* [Direccionamiento del siguiente mensaje de la cola](#dequeue-message)
* [Opciones adicionales para quitar mensajes de la cola](#additional-options)
* [Direccionamiento del las colas](#list-queues)
* [Direccionamiento del una cola](#delete-queue)
* [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="CreateAccount"></a>Crear una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Creación de una aplicación Java

En esta guía utilizará funciones del almacenamiento que puede ejecutar en una aplicación Java localmente o bien mediante código a través de un rol web o un rol de trabajo de Azure.

Para ello, deberá instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en su suscripción de Azure. A continuación, deberá verificar que su sistema de desarrollo satisface los requisitos mínimos y las dependencias que se indican en el repositorio del [SDK de almacenamiento de Azure para Java][SDK de almacenamiento de Azure para Java] en GitHub. Si su sistema cumple esos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema desde ese repositorio. Cuando haya completado esas tareas, podrá crear una aplicación Java que use los ejemplos de este artículo.

## <a name="ConfigureStorage"> </a>Configuración de la aplicación para obtener acceso al almacenamiento en cola

Agregue las siguientes instrucciones de importación en la parte superior del archivo Java en el que desea utilizar las API de almacenamiento de Azure para obtener acceso a las colas:

    // Incluir las siguientes instrucciones de importación para utilizar las API de las colas.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="ConnectionString"> </a>Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, utilizando el nombre de su cuenta de almacenamiento y la clave de acceso principal de la cuenta de almacenamiento que se muestra en el Portal de administración para los valores *AccountName* y *AccountKey*. En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

    // Definir la cadena de conexión con sus valores.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

En una aplicación que se esté ejecutando en un rol de Microsoft Azure, esta cadena se puede almacenar en el archivo de configuración del servicio, *ServiceConfiguration.cscfg*, y se puede obtener acceso a él con una llamada al método **RoleEnvironment.getConfigurationSettings**. A continuación se muestra un ejemplo de cómo obtener la cadena de conexión desde un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración del servicio:

    // Recuperar la cuenta de almacenamiento de la cadena de conexión.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

En los ejemplos siguientes se supone que ha usado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="create-queue"> </a>Direccionamiento del de una cola

Un objeto **CloudQueueClient** le permite obtener objetos de referencia para las colas. El siguiente código crea un objeto **CloudQueueClient**. (Nota: existen otras maneras de crear objetos **CloudStorageAccount**; para obtener más información, consulte **CloudStorageAccount** en la [Referencia del SDK del cliente de almacenamiento de Azure]).

Utilice el objeto **CloudQueueClient** para obtener una referencia a la cola que desea utilizar. En caso de que la cola no exista todavía, es posible crearla.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

	   // Crear el cliente de cola.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Recuperar una referencia a una cola.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Crear la cola si todavía no existe.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="add-message"> </a>Direccionamiento del un mensaje a una cola

Para insertar un mensaje en una cola existente, cree en primer lugar un nuevo **CloudQueueMessage**. A continuación, llame al método **addMessage**. Se puede crear un objeto **CloudQueueMessage** a partir de una cadena (en formato UTF-8) o de una matriz de bytes. A continuación se muestra el código con el que se crea una cola (si no existe) y se inserta el mensaje "Hola, mundo".

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Crear la cola si todavía no existe.
    	queue.createIfNotExists();

    	// Crear un mensaje y agregarlo a la cola.
    	CloudQueueMessage message = new CloudQueueMessage("Hola, mundo");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="peek-message"> </a>Direccionamiento del siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peekMessage**.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");
			
    	// Inspeccionar el siguiente mensaje.
    	CloudQueueMessage peekedMessage = queue.peekMessage();
			
    	// Consultar el valor del mensaje
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="change-message"> </a>Direccionamiento del contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de *n* veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

El siguiente código de ejemplo busca en la cola de mensajes, encuentra el primer mensaje cuyo contenido coincide con "Hola, mundo" y luego modifica el contenido del mensaje "Hola mundo" y se cierra. 

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // El número máximo de mensajes que se pueden recuperar es 32. 
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Aplicar un bucle a través de los mensajes en la cola.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Comprobar una cadena específica.
            if (message.getMessageContentAsString().equals("Hola, mundo"))
            {
                // Modifique el contenido del primer mensaje que coincida.
                message.setMessageContent("Updated contents.");
                // Establecer que sea visible en 30 segundos.
                EnumSet<MessageUpdateFields> updateFields = 
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Actualizar el mensaje.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

Como alternativa, el siguiente código de ejemplo actualiza únicamente el primer mensaje visible de la cola.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Recuperar el primer mensaje visible en la cola.
    	CloudQueueMessage message = queue.retrieveMessage();
			
    	if (message != null)
    	{
            // Modificar el contenido del mensaje.
            message.setMessageContent("Updated contents.");
            // Establecer que sea visible en 60 segundos.
            EnumSet<MessageUpdateFields> updateFields = 
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Actualizar el mensaje.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="get-queue-length"> </a>Direccionamiento del la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método **downloadAttributes** pide al servicio de cola varios valores actuales, incluido un conteo de cuántos mensajes están en la cola. El recuento solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud. El método **getApproximateMessageCount** devuelve el último valor recuperado por la llamada a **downloadAttributes**, sin llamar al servicio de cola.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Descargar el recuento de mensajes aproximado del servidor.
    	queue.downloadAttributes();

    	// Recuperar el número aproximado de mensajes recién colocados en caché.
    	long cachedMessageCount = queue.getApproximateMessageCount();
			
    	// Mostrar la longitud de la cola.
    	System.out.println(String.format("Longitud de la cola: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="dequeue-message"> </a>Direccionamiento del siguiente mensaje de la cola

El código extrae un mensaje de una cola en dos pasos. Si llama a **retrieveMessage**, obtiene el siguiente mensaje en una cola. Un mensaje devuelto por **retrieveMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar a **deleteMessage**. Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **deleteMessage** justo después de que se haya procesado el mensaje.

    try
    {
    	// Recuperar la cuenta de almacenamiento de la cadena de conexión.
    	CloudStorageAccount storageAccount = 
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Crear el cliente de cola.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar una referencia a una cola.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Recuperar el primer mensaje visible en la cola.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();
			
    	if (retrievedMessage != null)
    	{
    		// Procesar el mensaje en menos de 30 segundos y, a continuación, eliminar el mensaje.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }


## <a name="additional-options"> </a>Opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje.

El siguiente ejemplo de código utiliza el método **retrieveMessages** para obtener 20 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **for**. También establece el tiempo de espera de la invisibilidad en cinco minutos (300 segundos) para cada mensaje. Tenga en cuenta que los cinco minutos empiezan a contar para todos los mensajes al mismo tiempo, por lo que después de pasar los cinco minutos desde la llamada a **retrieveMessages**, todos los mensajes que no se han eliminado volverán a estar visibles.

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de cola.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Recuperar una referencia a una cola.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Recuperar 20 mensajes de la cola con un tiempo de espera de visibilidad de 300 segundos.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Realizar el procesamiento de todos los mensajes en menos de 5 minutos, 
            // y eliminar cada mensaje después del procesamiento.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="list-queues"> </a>Direccionamiento del las colas

Para obtener una lista de las colas actuales, llame al método **CloudQueueClient.listQueues()**, el cual devolverá una colección de objetos **CloudQueue**. 

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de cola.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Aplique un bucle a través de la colección de colas.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Consultar el nombre de cada cola.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="delete-queue"> </a>Direccionamiento del una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **deleteIfExists** en el objeto de cola **CloudQueue**.

    try
    {
        // Recuperar la cuenta de almacenamiento de la cadena de conexión.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Crear el cliente de cola.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Recuperar una referencia a una cola.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Eliminar la cola si existe.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Consultar el seguimiento de la pila.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento en cola, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- [SDK de almacenamiento de Azure para Java]
- [Referencia del SDK de cliente de almacenamiento de Azure]
- [API REST de almacenamiento de Azure]
- [Blog del equipo de almacenamiento de Azure]

[SDK de Azure para Java]: http://www.windowsazure.com/es-es/develop/java/
[SDK de almacenamiento de Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de almacenamiento de Azure para Android]: https://github.com/azure/azure-storage-android
[Referencia del SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de almacenamiento de Azure]: http://msdn.microsoft.com/es-es/library/azure/gg433040.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
