<properties title="How to use the queue service (PHP) - Azure feature guide" pageTitle="Uso del servicio de cola (PHP) | Microsoft Azure" metaKeywords="Azure Queue Service messaging PHP" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in PHP." documentationCenter="PHP" services="storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />

# Uso del servicio Cola de PHP

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio Cola de Azure. Los ejemplos se escriben con las clases del SDK de Windows para PHP. Entre los escenarios descritos se incluyen la **inserción**, **inspección**, **obtención** y **eliminación** de mensajes de la cola, así como la **creación y eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#NextSteps) .

##Tabla de contenido

* [¿Qué es el almacenamiento en cola?](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#create-account)
* [Creación de una aplicación PHP](#create-app)
* [Configuración de la aplicación para el servicio Cola](#configure-app)
* [Configuración de una conexión de almacenamiento de Azure](#connection-string)
* [Eliminación de una cola](#create-queue)
* [Eliminación de un mensaje a una cola](#add-message)
* [Eliminación siguiente mensaje](#peek-message)
* [Quitar el siguiente mensaje de la cola](#dequeue-message)
* [Eliminación contenido de un mensaje en cola](#change-message)
* [Opciones adicionales para quitar mensajes de la cola](#additional-options)
* [Obtención de la longitud de la cola](#get-queue-length)
* [Obtención de una cola](#delete-queue)
* [Pasos siguientes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="create-account"></a>Crear una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="create-app"></a>Creación de una aplicación PHP</h2>

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio Cola de Azure es que el código haga referencia a clases del SDK de Azure para PHP desde el código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará funciones del servicio Cola a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

<h2><a id="GetClientLibrary"></a>Obtención de las bibliotecas de clientes de Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="configure-app"></a>Configuración de la aplicación para obtener acceso al servicio Cola</h2>

Para utilizar las API del servicio Cola de Azure, necesita:

1. Hacer referencia al archivo autocargador mediante la instrucción [require_once][require_once] y
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE]
> En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador "WindowsAzure.php".

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


En los ejemplos que aparecen a continuación, la instrucción "require_once" aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

<h2><a id="connection-string"></a>Configuración de una conexión de almacenamiento de Azure</h2>

Para crear una instancia de un cliente del servicio Cola de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio Cola es:

Para obtener acceso a un servicio en directo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al emulador de almacenamiento:

	UseDevelopmentStorage=true


Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**. puede:

* pasarle directamente la cadena de conexión, o bien
* utilizar el **Administrador de configuración de nube (CCM)** (CloudConfigurationManager) para buscar la cadena de conexión en varios orígenes externos:
	* De manera predeterminada, admite un origen externo: variables de entorno.
	* Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);


<h2><a id="create-queue"></a>Reproducción de una cola</h2>

Un objeto **QueueRestProxy** le permite crear una cola con el método **createQueue**. Al crear una cola, puede establecer opciones en ella, aunque no es obligatorio. El ejemplo siguiente muestra cómo configurar metadatos en una cola.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateQueueOptions;
	
	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set queue metadata.
	$createQueueOptions = new CreateQueueOptions();
	$createQueueOptions->addMetaData("key1", "value1");
	$createQueueOptions->addMetaData("key2", "value2");
	
	try	{
		// Create queue.
		$queueRestProxy->createQueue("myqueue", $createQueueOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> No debe confiar en la distinción entre minúsculas y mayúsculas para las claves de metadatos. Todas las claves se leen en el servicio en minúsculas.


<h2><a id="add-message"></a>Reproducción de un mensaje a una cola</h2>

Para agregar un mensaje a una cola, use **QueueRestProxy->createMessage**. El método toma el nombre de la cola, el texto del mensaje y las opciones de mensaje (que son opcionales).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateMessageOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Create message.
		$builder = new ServicesBuilder();
		$queueRestProxy->createMessage("myqueue", "Hello World!");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="peek-message"></a>Reproducción de siguiente mensaje</h2>

Puede inspeccionar uno o varios mensajes en la parte delantera de una cola, sin quitarlos de la cola, mediante una llamada a **QueueRestProxy->peekMessages**. De forma predeterminada, el método **peekMessage** devuelve un único mensaje, pero puede cambiar el valor con el método **PeekMessagesOptions->setNumberOfMessages** method.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\PeekMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set peek message options.
	$message_options = new PeekMessagesOptions();
	$message_options->setNumberOfMessages(1); // Default value is 1.
	
	try	{
		$peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$messages = $peekMessagesResult->getQueueMessages();

	// View messages.
	$messageCount = count($messages);
	if($messageCount <= 0){
		echo "There are no messages.<br />";
	}
	else{
		foreach($messages as $message)	{
			echo "Peeked message:<br />";
			echo "Message Id: ".$message->getMessageId()."<br />";
			echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
			echo "Message text: ".$message->getMessageText()."<br /><br />";
		}
	}

<h2><a id="dequeue-message"></a>Reproducción de siguiente mensaje de la cola</h2>

El código borra un mensaje de una cola en dos pasos. Primero llama a **QueueRestProxy->listMessages**, que hace que el mensaje sea invisible a cualquier otra lectura de código desde la cola. De forma predeterminada, este mensaje permanecerá invisible durante 30 segundos (si el mensaje no se elimina en este período, volverá a estar visible de nuevo en la cola). Para terminar de quitar el mensaje de la cola, debe llamar a **QueueRestProxy->deleteMessage**. Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. Su código llama a **deleteMessage** justo después de que se haya procesado el mensaje.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	/* ---------------------
		Process message.
	   --------------------- */
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Delete message.
		$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="change-message"></a>Reproducción de contenido de un mensaje en cola</h2>

Puede cambiar el contenido de un mensaje local en la cola llamando a **QueueRestProxy->updateMessage**. Si el mensaje representa una tarea de trabajo, puede utilizar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de la cola con contenido nuevo y amplía el tiempo de espera de la visibilidad en 60 segundos más. De este modo, se guarda el estado de trabajo asociado al mensaje y se le proporciona al cliente un minuto más para que siga elaborando el mensaje. Esta técnica se puede utilizar para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes en cola, sin que sea necesario volver a empezar desde el principio si se produce un error en un paso del proceso a causa de un error de hardware o software. Normalmente, también mantendría un número de reintentos y, si el mensaje se intentara más de n veces, lo eliminaría. Esto proporciona protección frente a un mensaje que produce un error en la aplicación cada vez que se procesa.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;	

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	// Define new message properties.
	$new_message_text = "New message text.";
	$new_visibility_timeout = 5; // Measured in seconds. 
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Update message.
		$queueRestProxy->updateMessage("myqueue", 
									$messageId, 
									$popReceipt, 
									$new_message_text, 
									$new_visibility_timeout);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="additional-options"></a>Opciones adicionales de los mensajes quitados de la cola</h2>

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la visibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método **getMessages** para obtener 16 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle **for**. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\ListMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Set list message options. 
	$message_options = new ListMessagesOptions();
	$message_options->setVisibilityTimeoutInSeconds(300); 
	$message_options->setNumberOfMessages(16);
	
	// Get messages.
	try{
		$listMessagesResult = $queueRestProxy->listMessages("myqueue", 
														 $message_options); 
		$messages = $listMessagesResult->getQueueMessages(); 

		foreach($messages as $message){
			
			/* ---------------------
				Process message.
			--------------------- */
		
			// Get message Id and pop receipt.
			$messageId = $message->getMessageId();
			$popReceipt = $message->getPopReceipt();
			
			// Delete message.
			$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="get-queue-length"></a>Reproducción de longitud de la cola</h2>

Puede obtener una estimación del número de mensajes existentes en una cola. El método **QueueRestProxy->getQueueMetadata** solicita al servicio de Cola devolver los metadatos sobre la cola. Si llama al método **getApproximateMessageCount** en el objeto devuelto, se ofrece un recuento de la cantidad de mensajes que hay en una cola. El recuento solo es aproximado, ya que se pueden agregar o borrar mensajes después de que el servicio de cola haya respondido su solicitud.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Get queue metadata.
		$queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
		$approx_msg_count = $queue_metadata->getApproximateMessageCount();
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	echo $approx_msg_count;

<h2><a id="delete-queue"></a>Reproducción de una cola</h2>

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **QueueRestProxy->deleteQueue** method.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Delete queue.
		$queueRestProxy->deleteQueue("myqueue");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="next-steps"></a>Pasos siguientes</h2>

Ahora que está familiarizado con los aspectos básicos del servicio Cola de Azure, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure] []
- Obtenga acceso al blog del equipo de almacenamiento de Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

[descargar]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Portal de administración de Azure]: http://manage.windowsazure.com/
[Almacenamiento de datos y acceso a los mismos en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx

<!--HONumber=35.1-->
