<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (PHP) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Utilización de las colas del bus de servicio
============================================

En esta guía se explica cómo usar las colas del bus de servicio con PHP. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

Tabla de contenido
------------------

-   [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
-   [Creación de una aplicación PHP](#CreateApplication)
-   [Obtención de las bibliotecas de clientes de Azure](#GetClientLibrary)
-   [Configuración de la aplicación para usar el bus de servicio](#ConfigureApp)
-   [Creación de una cola](#CreateQueue)
-   [Envío de mensajes a una cola](#SendMessages)
-   [Recepción de mensajes de una cola](#ReceiveMessages)
-   [Administración de errores de la aplicación y mensajes que no se pueden leer](#HandleCrashes)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Creación de una aplicación PHP
------------------------------

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del [SDK de Azure para PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

> [WACOM.NOTE] La instalación de PHP debe tener también la [extensión OpenSSL](http://php.net/openssl) instalada y habilitada.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

Obtención de las bibliotecas de clientes de Azure
-------------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configuración de la aplicación para usar el bus de servicio
-----------------------------------------------------------

Para usar las API de la cola del bus de servicio de Azure, necesita:

1.  Hacer referencia al archivo autocargador mediante la instrucción [require\_once](http://php.net/require_once).
2.  Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE] En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

Configuración de una conexión del bus de servicio de Azure
----------------------------------------------------------

Para crear una instancia de un cliente del bus de servicio de Azure, primero debe disponer de una cadena de conexión válida con el siguiente formato:

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Donde el extremo tiene generalmente el formato `https://[yourNamespace].servicebus.windows.net`.

Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**, puede:

-   pasarle directamente la cadena de conexión, o bien
-   utilizar el **Administrador de configuración de nube (CCM)** (CloudConfigurationManager ) para buscar la cadena de conexión en varios orígenes externos:
    -   De manera predeterminada, admite un origen externo: variables de entorno.
    -   Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

Creación de una cola
--------------------

Las operaciones de administración para las colas de bus de servicio pueden realizarse mediante la clase **ServiceBusRestProxy**. Un objeto **ServiceBusRestProxy** se construye a través del método Factory Method **ServicesBuilder::createServiceBusService** con una cadena de conexión adecuada que encapsula los permisos de token para administrarlo.

El siguiente ejemplo muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy-\>createQueue** para crear una cola llamada `myQueue` dentro de un espacio de nombres de servicio `MySBNamespace`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\QueueInfo;

    // Crear proxy REST de bus de servicio.
        $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        $queueInfo = new QueueInfo("myqueue");
        
        // Crear la cola.
        $serviceBusRestProxy->createQueue($queueInfo);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE] Se puede usar el método **listQueues** en los objetos de **ServiceBusRestProxy** para comprobar si una cola con un nombre especificado ya existe dentro de un espacio de nombres de servicio.

Envío de mensajes a una cola
----------------------------

Para enviar un mensaje a una cola de bus de servicio, la aplicación va a llamar al método **ServiceBusRestProxy-\>sendQueueMessage**. El siguiente código muestra cómo enviar un mensaje a la cola `myQueue` que hemos creado anteriormente en el espacio de nombres de servicio `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\BrokeredMessage;

    // Crear proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Crear el mensaje.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Enviar mensaje.
        $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/es-es/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. **Los objetos BrokeredMessage** tienen un conjunto de métodos estándar (por ejemplo, **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**) y las propiedades que se usan para mantener las propiedades específicas de la aplicación personalizada y un cuerpo de datos de aplicaciones arbitrarias.

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB.

Recepción de mensajes de una cola
---------------------------------

La principal manera de recibir mensajes desde una cola es usar un método **ServiceBusRestProxy-\>receiveQueueMessage**. Los mensajes pueden recibirse en dos modos diferentes: **ReceiveAndDelete** (el modo predeterminado) y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. **El modo ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy-\>deleteMessage**. Cuando el bus de servicio ve la llamada **deleteMessage**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se puede procesar y recibir un mensaje usando el modo **PeekLock** (no es el modo predeterminado).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

    // Crear proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Establecer el modo de recepción en PeekLock (el modo predeterminado es ReceiveAndDelete).
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();
        
        // Recibir mensaje.
        $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Procesar mensaje aquí.
        ----------------------------*/
        
        // Eliminar mensaje. No es necesario si peek lock no está configurado.
        echo "Message deleted.<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí:
        // http://msdn.microsoft.com/es-es/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Control de errores de la aplicación y mensajes que no se pueden leer
--------------------------------------------------------------------

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces se recomienda la incorporación de lógica adicional a su aplicación para administrar la entrega de mensajes duplicados. A menudo, esto se consigue usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

Pasos siguientes
----------------

Ahora que ya conoce los aspectos básicos de las colas del bus de servicio, consulte el tema [Colas, temas y suscripciones](http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx) de MSDN para obtener más información.

