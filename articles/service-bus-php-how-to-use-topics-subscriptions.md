<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

Uso de temas/suscripciones del bus de servicio
==============================================

En esta guía se indica cómo usar los temas y las suscripciones de bus de servicio. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Entre los escenarios tratados se incluyen **la creación de temas y suscripciones**, **la creación de filtros de suscripción**, **el envío de mensajes a un tema**, **la recepción de mensajes de una suscripción** y **la eliminación de temas y suscripciones**.

Tabla de contenido
------------------

-   [Qué son los temas y las suscripciones del bus de servicio](#what-are-service-bus-topics)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
-   [Creación de una aplicación PHP](#CreateApplication)
-   [Obtención de las bibliotecas de clientes de Azure](#GetClientLibrary)
-   [Configuración de la aplicación para usar el bus de servicio](#ConfigureApp)
-   [Recepción de un tema](#CreateTopic)
-   [Creación de una suscripción](#CreateSubscription)
-   [Creación de mensajes a un tema](#SendMessage)
-   [Creación de mensajes de una suscripción](#ReceiveMessages)
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#HandleCrashes)
-   [Eliminación de temas y suscripciones](#DeleteTopicsAndSubscriptions)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

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

Para usar las API del tema del bus de servicio de Azure, necesita:

1.  Hacer referencia al archivo autocargador mediante la instrucción [require\_once](http://php.net/require_once).
2.  Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServiceBusService**.

    > [WACOM.NOTE]
    > En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador <code>WindowsAzure.php</code>.

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

Creación de un tema
-------------------

Las operaciones de administración para los temas de bus de servicio pueden realizarse mediante la clase **ServiceBusRestProxy**. Un objeto **ServiceBusRestProxy** se construye a través del método Factory Method **ServicesBuilder::createServiceBusService** con una cadena de conexión adecuada que encapsula los permisos de token para administrarlo.

El siguiente ejemplo muestra cómo crear una instancia de un **ServiceBusRestProxy** y llamar a **ServiceBusRestProxy-\>createTopic** para crear un tema llamado `mytopic` dentro de un espacio de nombres de servicio `MySBNamespace`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\TopicInfo;

    // Cree el proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Cree el tema.
        $topicInfo = new TopicInfo("mytopic");
        $serviceBusRestProxy->createTopic($topicInfo);
    }
    catch(ServiceException $e){
        // Administre la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    > [WACOM.NOTE]
    > Puede usar el método <b>listTopics</b> en objetos <b>ServiceBusRestProxy</b> para comprobar si ya existe un tema con un nombre especificado dentro de un espacio de nombres de servicio.

Creación de una suscripción
---------------------------

Las suscripciones a temas también se crean con el método **ServiceBusRestProxy-\>createSubscription**. A las suscripciones se les puede asignar un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a la cola virtual de la suscripción.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "mysubscription" que usa el filtro predeterminado **MatchAll**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

    // Cree el proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        // Cree una suscripción.
        $subscriptionInfo = new SubscriptionInfo("mysubscription");
        $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
    }
    catch(ServiceException $e){
        // Administre la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

### Creación de suscripciones con filtros

También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de tema determinada. El tipo de filtro más flexible compatible con las suscripciones es **SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre los SqlFilter, consulte [SqlExpression Propiedad](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

    > [WACOM.NOTE]
    > Cada regla en una suscripción procesa los mensajes entrantes de forma independiente, lo que agrega los mensajes de resultados a la suscripción. Además, cada nueva suscripción dispone de una <b>regla</b> predeterminada con un filtro que agrega todos los mensajes del tema a la suscripción. Para recibir solo mensajes que coincidan con su filtro, debe quitar la regla predeterminada. Puede quitar la regla predeterminada con el método <b>ServiceBusRestProxy->deleteRule</b>.

En el ejemplo que aparece a continuación se crea una suscripción llamada "HighMessages" con un **SqlFilter** que solo selecciona los mensajes con una propiedad **MessageNumber** personalizada superior a 3 (consulte [Envío de mensajes a un tema](#SendMessage) para obtener información sobre la incorporación de propiedades personalizadas a mensajes):

    $subscriptionInfo = new SubscriptionInfo("HighMessages");

\$serviceBusRestProxy-\>createSubscription("mytopic", \$subscriptionInfo);

    $serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

\$ruleInfo = new RuleInfo("HighMessagesRule"); \$ruleInfo-\>withSqlFilter("MessageNumber \> 3"); \$ruleResult = \$serviceBusRestProxy-\>createRule("mytopic", "HighMessages", \$ruleInfo);

Tenga en cuenta que el código anterior requiere el uso de un espacio de nombres adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Del mismo modo, en el ejemplo que aparece a continuación, se crea una suscripción llamada "LowMessages" con un SqlFilter que solo selecciona los mensajes con una propiedad MessageNumber menor o igual a 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Ahora, cuando se envíe un mensaje a `mytopic`, siempre se entregará a los destinatarios suscritos a `mysubscription` y, selectivamente, a los destinatarios suscritos a “HighMessages” y “LowMessages” (dependiendo del contenido del mensaje).

Envío de mensajes a un tema
---------------------------

Para enviar un mensaje a un tema de bus de servicio, la aplicación va a llamar al método **ServiceBusRestProxy-\>sendTopicMessage**. El siguiente código muestra cómo enviar un mensaje al tema `mytopic` que hemos creado anteriormente en el espacio de nombres de servicio `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\BrokeredMessage;

    // Cree el proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Cree el mensaje.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Envíe el mensaje.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }
    catch(ServiceException $e){
        // Administre la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Los mensajes enviados a los temas de bus de servicio son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de métodos y propiedades estándar (como **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**), así como propiedades que pueden usarse para mantener propiedades personalizadas específicas de la aplicación. El siguiente ejemplo muestra cómo enviar cinco mensajes de prueba al tema `mytopic` creado anteriormente. El método **setProperty** se usa para agregar una propiedad personalizada (`MessageNumber`) a cada mensaje. Tenga en cuenta la forma en la que el valor de la propiedad `MessageNumber` varía en cada mensaje (esto se puede usar para determinar qué suscripciones recibe, como se muestra en la sección [Creación de una suscripción](#CreateSubscription) anterior):

    for($i = 0; $i < 5; $i++){
        // Cree el mensaje.
        $message = new BrokeredMessage();
        $message->setBody("my message ".$i);
            
        // Establezca la propiedad personalizada.
        $message->setProperty("MessageNumber", $i);
            
        // Envíe el mensaje.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. Este límite superior para el tamaño de la cola es de 5 GB.

Recepción de mensajes de una suscripción
----------------------------------------

La principal manera de recibir mensajes desde una suscripción es usar un método **ServiceBusRestProxy-\>receiveSubscriptionMessage**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** (el modo predeterminado) y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje en una suscripción, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción de un mensaje se convierte en una operación de dos etapas, lo que hace posible admitir aplicaciones que no pueden tolerar la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción pasando el mensaje recibido a **ServiceBusRestProxy-\>deleteMessage**. Cuando el bus de servicio ve la llamada **deleteMessage**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se puede procesar y recibir un mensaje usando el modo **PeekLock** (no es el modo predeterminado).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Cree el proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Establezca el modo de recepción en PeekLock (el modo predeterminado es ReceiveAndDelete).
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();

        // Obtenga el mensaje.
        $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
                                                                    "mysubscription", 
                                                                    $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Procesar mensaje aquí.
        ----------------------------*/
        
        // Elimine el mensaje. No es necesario si peek lock no está configurado.
        echo "Deleting message...<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Administre la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí:
        // http://msdn.microsoft.com/en-us/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Recepción errores de la aplicación y mensajes que no se pueden leer
-------------------------------------------------------------------

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

Eliminación de temas y suscripciones
------------------------------------

Para eliminar un tema o una suscripción, use los métodos **ServiceBusRestProxy-\>deleteTopic** o **ServiceBusRestProxy-\>deleteSubscripton** respectivamente. Tenga en cuenta que al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él.

En el siguiente ejemplo se muestra cómo eliminar un tema (`mytopic`) y las suscripciones registradas.

    require_once 'vendor\autoload.php';

    use WindowsAzure\ServiceBus\ServiceBusService;
    use WindowsAzure\ServiceBus\ServiceBusSettings;
    use WindowsAzure\Common\ServiceException;

    // Cree el proxy REST de bus de servicio.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Elimine el tema.
        $serviceBusRestProxy->deleteTopic("mytopic");
    }
    catch(ServiceException $e){
        // Administre la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si usa el método **deleteSubscription**, puede eliminar una suscripción de manera independiente:

    $serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

Pasos siguientes
----------------

Ahora que ya conoce los aspectos básicos de las colas del bus de servicio, consulte el tema [Colas, temas y suscripciones](http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx) de MSDN para obtener más información.

