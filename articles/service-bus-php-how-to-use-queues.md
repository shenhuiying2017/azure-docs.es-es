<properties urlDisplayName="Service Bus Queues" pageTitle="Uso de las colas del bus de servicio (PHP) - Azure" metaKeywords="colas del bus de servicio de Azure, colas de Azure, mensajería de Azure, colas de Azure en PHP" description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />

# Utilización de las colas del bus de servicio

En esta guía se explica cómo usar las colas del bus de servicio con PHP. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP][download-sdk]. Entre los escenarios proporcionados se incluyen **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

## Tabla de contenido

-   [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
- 	[Creación de una aplicación PHP](#CreateApplication)
-	[Obtención de las bibliotecas de clientes de Azure](#GetClientLibrary)
-   [Configuración de la aplicación para usar el bus de servicio](#ConfigureApp)
-   [Creación de una cola](#CreateQueue)
-   [Envío de mensajes a una cola](#SendMessages)
-   [Recepción de mensajes de una cola](#ReceiveMessages)
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#HandleCrashes)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>Creación de una aplicación PHP

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del [SDK de Azure para PHP][download-sdk] dentro del código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

> [WACOM.NOTE]
> La instalación de PHP debe tener también la <a href="http://php.net/openssl">extensión OpenSSL</a> instalada y habilitada.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

##<a id="GetClientLibrary"></a>Obtención de las bibliotecas de clientes de Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configuración de la aplicación para usar el bus de servicio

Para usar las API de la cola del bus de servicio de Azure, necesita:

1. Hacer referencia al archivo autocargador mediante la instrucción [require_once][require_once] y
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE] 
>  En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


En los ejemplos que aparecen a continuación, la instrucción "require_once" aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

##<a id="ConnectionString"></a>Configuración de una conexión del bus de servicio de Azure

Para crear una instancia de un cliente del bus de servicio de Azure, primero debe disponer de una cadena de conexión válida con el siguiente formato:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Donde el extremo tiene generalmente el formato "https://[yourNamespace].servicebus.windows.net".

Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**, puede:

* pasarle directamente la cadena de conexión, o bien
* 	utilizar el **CloudConfigurationManager (CCM)** (Administrador de configuración de nube) para buscar la cadena de conexión en varios orígenes externos:
	* De manera predeterminada, admite un origen externo: variables de entorno.
	* Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>Creación de una cola

Las operaciones de administración para las colas de bus de servicio se pueden realizar por medio de la clase **ServiceBusRestProxy**. Un objeto **ServiceBusRestProxy** se construye a través del método Factory Method **ServicesBuilder::createServiceBusService** con una cadena de conexión adecuada que encapsula los permisos de token para administrarlo.

El siguiente ejemplo muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy->createQueue** para crear una cola llamada "myqueue" dentro de un espacio de nombres de servicio "MySBNamespace":

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/es-es/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> Puede usar el método <b>listQueues</b> en el objeto <b>ServiceBusRestProxy</b> para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

##<a id="SendMessages"></a>Envío de mensajes a una cola

Para enviar un mensaje a una cola de bus de servicio, la aplicación va a llamar al método **ServiceBusRestProxy->sendQueueMessage**. El siguiente código muestra cómo enviar un mensaje a la cola "myqueue" que hemos creado anteriormente en el espacio de nombres de servicio
"MySBNamespace".

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/es-es/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de métodos estándar (como **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**) y propiedades que se usan para contener propiedades personalizadas específicas de la aplicación y un cuerpo de datos arbitrarios de aplicaciones.

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB.

##<a id="ReceiveMessages"></a>Recepción de mensajes de una cola

La principal manera de recibir mensajes desde una cola es usar un método **ServiceBusRestProxy->receiveQueueMessage**. Los mensajes pueden recibirse en dos modos diferentes: **ReceiveAndDelete** (predeterminado) y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios con una
aplicación que puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy->deleteMessage**. Cuando el bus de servicio ve la llamada **deleteMessage**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se pueden recibir y procesar un mensaje usando el modo **PeekLock** (no el modo predeterminado).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/es-es/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces se recomienda la incorporación de lógica adicional a su aplicación para administrar la entrega de mensajes duplicados. A menudo, esto se consigue usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

##<a id="NextSteps"></a>Pasos siguientes

Ahora que ya conoce los aspectos básicos de las colas del bus de servicio, consulte de MSDN [Colas, temas y suscripciones][] para obtener más información.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Diagrama de cola de Bus de servicio]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Portal de administración de Azure]: http://manage.windowsazure.com/
[Captura de pantalla del nodo Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[Captura de pantalla de la creación de un nuevo espacio de nombres]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[Captura de pantalla de los espacios de nombres disponibles]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[Captura de pantalla de la lista de espacios de nombres]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[Captura de pantalla del panel de propiedades]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[Captura de pantalla de la clave predeterminada]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[Colas, temas y suscripciones del Service Bus]: http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once

