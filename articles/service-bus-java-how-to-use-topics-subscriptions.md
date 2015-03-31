<properties 
	pageTitle="Uso de los temas del Bus de servicio (Java) - Azure" 
	description="Aprenda a usar los temas y las suscripciones del Bus de servicio de Azure. Los ejemplos de código están escritos para aplicaciones Java." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Uso de temas/suscripciones del Bus de servicio

En esta guía se describe cómo usar los temas y las suscripciones del Bus de servicio. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java][]. Entre los escenarios tratados se incluye **la creación de temas y suscripciones**, la **creación de filtros de suscripción**, el **envío de mensajes a un tema**, la **recepción de mensajes de una suscripción** y la **eliminación de temas y suscripciones**.

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Configuración de la aplicación para usar el Bus de servicio

Agregue las siguientes instrucciones import a la parte superior del archivo Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Agregue las bibliotecas de Azure para Java a su ruta de acceso de compilación e inclúyala en su conjunto de implementación del proyecto.

## Creación de un tema

Las operaciones de administración para los temas del Bus de servicio pueden realizarse mediante la clase **ServiceBusContract**. Un objeto **ServiceBusContract** se construye con una configuración adecuada que encapsula los permisos de token para administrarlo y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar temas. En el ejemplo siguiente se muestra cómo se puede usar un objeto **ServiceBusService** para crear un tema llamado "TestTopic" con un espacio de nombres denominado "HowToSample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

En **TopicInfo** hay métodos que permiten ajustar las propiedades del tema (por ejemplo, establecer el valor de "período de vida" predeterminado que se aplicará a los mensajes enviados al tema). El siguiente ejemplo muestra cómo crear un tema llamado "TestTopic", con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Tenga en cuenta que puede usar el método **listTopics** en los objetos **ServiceBusContract** para comprobar si un tema con un nombre específico ya existe en un espacio de nombres de servicio.

## Creación de suscripciones

Las suscripciones a los temas también se crean con la clase **ServiceBusService**. A las suscripciones se les puede asignar un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a la cola virtual de la suscripción.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "AllMessages" que usa el filtro predeterminado **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Creación de suscripciones con filtros

También puede configurar filtros que le permitan especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de tema determinada.

El tipo de filtro más flexible compatible con las suscripciones es **SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información acerca de las expresiones que se pueden usar con un filtro de SQL, revise la sintaxis de SqlFilter.SqlExpression.

En el ejemplo que aparece a continuación se crea una suscripción llamada "HighMessages" con un **SqlFilter** que solo selecciona los mensajes con una propiedad **MessageNumber** personalizada superior a 3:

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

Del mismo modo, en el ejemplo que aparece a continuación, se crea una suscripción llamada "LowMessages" con un SqlFilter que solo selecciona los mensajes con una propiedad MessageNumber menor o igual a 3:

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


Cuando se envía ahora un mensaje a "TestTopic", este se entregará siempre a los destinatarios suscritos al tema "AllMessages" y se entregará de forma selectiva a los destinatarios suscritos a los temas "HighMessages" y "LowMessages" (según el contenido del mensaje).

## Envío de mensajes a un tema

Para enviar un mensaje a una tema de Bus de servicio, la aplicación va a obtener un **ServiceBusContract**. El código que aparece a continuación muestra cómo enviar un mensaje a la cola "TestTopic" que hemos creado arriba dentro de nuestro espacio de nombres de servicio "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Los mensajes enviados a los temas de Bus de servicio son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** tienen un conjunto de métodos estándar (como **setLabel** y **TimeToLive**), un diccionario que contiene las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor de **BrokeredMessage** y, a continuación, se usará el **DataContractSerializer** adecuado para serializar el objeto. Como alternativa, se puede proporcionar un elemento
**java.io.InputStream**.

En el ejemplo que aparece a continuación se indica cómo enviar cinco mensajes de prueba al "TestTopic" **MessageSender** que se ha obtenido en el fragmento de código anterior.
Observe cómo el valor de la propiedad **MessageNumber** de cada mensaje varía en función de la iteración del bucle (así se determinará qué suscripción lo recibe):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Los temas del Bus de servicio admiten mensajes con un tamaño máximo de 256 MB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 MB). No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB).

## Recepción de mensajes de una suscripción

La forma primaria de recibir mensajes de una suscripción es usar un objeto **ServiceBusContract**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe una solicitud de lectura para un mensaje, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la llamada a **Delete** en el mensaje recibido. Cuando el Bus de servicio ve la llamada a **Delete**, marca el mensaje como consumido y lo quita del tema.

En el ejemplo que aparece a continuación se indica cómo se pueden recibir y procesar mensajes con el modo **PeekLock** (no el modo predeterminado). El ejemplo siguiente realiza un bucle y procesa los mensajes en la suscripción "HighMessages" y luego sale cuando no hay más mensajes (de manera alternativa, se podría establecer para que espere mensajes nuevos).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " + 
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
		    }  
		    else  
		    {        
		        System.out.println("Finishing up - no more messages.");        
		        break; 
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	} 

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje del tema y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado a un mensaje bloqueado en el tema y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Eliminación de temas y suscripciones

La principal manera de eliminar temas y suscripciones es usar un objeto **ServiceBusContract**. Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Pasos siguientes

Ahora que ha aprendido los conceptos básicos de las colas de bus de servicio, consulte el tema de MSDN [Colas, temas y suscripciones del Service Bus][Colas, temas y suscripciones del Bus de servicio] para obtener más información.

  [SDK de Azure para Java]: http://azure.microsoft.com/develop/java/
  [Qué son los temas y las suscripciones del Bus de servicio]: #what-are-service-bus-topics
  [Creación de un espacio de nombres de servicio]: #create-a-service-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
  [Configuración de la aplicación para usar el Bus de servicio]: #bkmk_ConfigYourApp
  [Codificación de un tema]: #bkmk_HowToCreateTopic
  [Codificación suscripciones]: #bkmk_HowToCreateSubscrip
  [Codificación mensajes a un tema]: #bkmk_HowToSendMsgs
  [Codificación mensajes de una suscripción]: #bkmk_HowToReceiveMsgs
  [Codificación ante errores de la aplicación y mensajes que no se pueden leer]: #bkmk_HowToHandleAppCrash
  [Codificación temas y suscripciones]: #bkmk_HowToDeleteTopics
  [Pasos siguientes]: #bkmk_NextSteps
  [Diagrama de temas del bus de servicio]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [Captura de pantalla del nodo Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Creación de un espacio de nombres nuevo ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Captura de pantalla de la lista de espacios de nombres]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Captura de pantalla del panel de propiedades]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Captura de pantalla de la clave predeterminada]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Colas, temas y suscripciones del Bus de servicio]: http://msdn.microsoft.com/library/hh367516.aspx

<!--HONumber=47-->
