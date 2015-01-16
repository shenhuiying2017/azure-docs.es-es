<properties urlDisplayName="Service Bus Topics" pageTitle="Uso de los temas del bus de servicio (Java) - Azure" metaKeywords="Introducción a temas de bus de servicio de Azure, Introducción a temas de bus de servicio de Azure, mensajería para la suscripción de publicaciones de Azure, suscripciones y temas de mensajería de Azure, tema del bus de servicio en Java" description="Aprenda a usar los temas de Bus de servicio y suscripciones de Azure. Los ejemplos de código están escritos para aplicaciones Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Uso de temas/suscripciones del bus de servicio

En esta guía se indica cómo usar los temas y las suscripciones
de bus de servicio. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java][]. Los escenarios descritos incluyen la **creación de temas
y suscripciones**, **creación de filtros de suscripción**, **envío
de mensajes a un tema**, **recepción de mensajes de una suscripción** y 
**eliminación de temas y suscripciones**.

## Tabla de contenido

-   [Qué son los temas y las suscripciones del bus de servicio][]
-   [Creación de un espacio de nombres de servicio][]
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres][]
-   [Configuración de la aplicación para usar el bus de servicio][]
-   [Direccionamiento del de un tema][]
-   [Direccionamiento del suscripciones][]
-   [Direccionamiento del mensajes a un tema][]
-   [Direccionamiento del mensajes de una suscripción][]
-   [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer][]
-   [Direccionamiento del de temas y suscripciones][]
-   [Pasos siguientes][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Configuración de la aplicación para usar el bus de servicio

Agregue las siguientes instrucciones import a la parte superior del archivo Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Agregue las bibliotecas de Azure para Java a su ruta de acceso de compilación e inclúyala en su conjunto de implementación del proyecto.

## <a name="bkmk_HowToCreateTopic"> </a>Creación de un tema

Las operaciones de administración para los temas de bus de servicio pueden realizarse mediante la clase
**ServiceBusContract**. Un objeto **ServiceBusContract**
crea con una configuración apropiada que comprende
los permisos de token para administrarlo y la clase **ServiceBusContract** es
el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar,
y eliminar temas. En el ejemplo siguiente se muestra cómo se puede usar un objeto **ServiceBusService**
para crear un tema llamado "TestTopic", con un espacio de nombres denominado "HowToSample":

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

Hay métodos en **TopicInfo** que permiten ajustar las propiedades del tema
(por ejemplo: para definir el valor "time-to-live" predeterminado
para que se aplique a los mensajes enviados al tema). El ejemplo siguiente muestra cómo
crear un tema llamado "TestTopic" con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Tenga en cuenta que puede usar el método **listTopics** en objetos
**ServiceBusContract** para comprobar si un tema con un nombre específico
ya existe dentro de un espacio de nombres de servicio.

## <a name="bkmk_HowToCreateSubscrip"> </a>Creación de suscripciones

Las suscripciones a temas también se crean con la clase **ServiceBusService**
. A las suscripciones se les asigna un nombre y pueden tener un filtro opcional que
restringe el conjunto de mensajes transmitidos a la cola virtual
de la suscripción.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro **MatchAll** es el filtro predeterminado usado si no se ha especificado
ningún filtro al crear una suscripción nueva. Cuando se usa el filtro **MatchAll**
, todos los mensajes publicados en el tema se colocan en la
cola virtual de la suscripción. En el ejemplo siguiente se crea una
suscripción llamada "AllMessages" y utiliza el filtro predeterminado **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Creación de suscripciones con filtros

También puede configurar filtros que le permiten especificar qué mensajes enviados
a un tema deben aparecer dentro de una suscripción de tema determinada.

El tipo más flexible de filtro compatible con las suscripciones es
**SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan
en las propiedades de los mensajes que se publican en el tema. Para obtener
más detalles acerca de las expresiones que pueden utilizarse con un filtro de SQL,
revise la sintaxis de SqlFilter.SqlExpression.

El ejemplo siguiente crea una suscripción llamada "HighMessages" con un filtro
**SqlFilter** que solo selecciona los mensajes que tienen una propiedad
**MessageNumber** predeterminada mayor que 3:

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

De forma similar, en el ejemplo siguiente se crea una suscripción llamada
"LowMessages" con un filtro   
 SqlFilter que solo selecciona los mensajes que tienen una propiedad MessageNumber
inferior o   
 igual a 3:

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


Cuando un mensaje se envía ahora a "TestTopic", siempre se
entrega a los destinatarios suscritos a la suscripción del tema "AllMessages"
y selectivamente se entrega a los destinatarios suscritos a las suscripciones de los temas
"HighMessages" y "LowMessages" (en función del
contenido del mensaje).

## <a name="bkmk_HowToSendMsgs"> </a>Envío de mensajes a un tema

Para enviar un mensaje a una tema de bus de servicio, la aplicación va a obtener un objeto
**ServiceBusContract**. El siguiente código muestra cómo enviar un
mensaje para el tema "TestTopic" que hemos creado anteriormente en nuestro
espacio de nombres de servicio "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Los mensajes enviados a los temas de bus de servicio son instancias de la clase
**BrokeredMessage**. Los objetos **BrokeredMessage** tienen un conjunto de
métodos estándar (como **setLabel** y **TimeToLive**), un diccionario
que se utiliza para contener propiedades específicas de aplicación personalizadas y un cuerpo
de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del
mensaje pasando todos los objetos serializables al creador de
**BrokeredMessage** y la clase **DataContractSerializer** se
utilizará para serializar el objeto. Como alternativa, se puede proporcionar un
**java.io.InputStream**.

En el siguiente ejemplo se demuestra cómo enviar cinco mensajes de prueba a
"TestTopic" **MessageSender** que se ha obtenido en el fragmento de código anterior.
Tenga en cuenta como el valor de propiedad **MessageNumber** de cada mensaje varía en
la iteración del bucle (así determinará qué suscripciones
recibe):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Los temas de bus de servicio admiten un mensaje con un tamaño máximo de 256 MB (el encabezado,
que incluye las propiedades de la aplicación estándar y personalizadas, puede tener
un tamaño máximo de 64 MB). No hay ningún límite en el número de mensajes
contenidos en un tema, pero sí hay un límite en el tamaño total de los mensajes
que un tema contiene. El tamaño de este tema se define en tiempo de creación, con un
límite máximo de 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Recepción de mensajes de una suscripción

La forma primaria de recibir mensajes de una suscripción es usar un objeto
**ServiceBusContract**. Los mensajes recibidos se comportan de dos
modos diferentes: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la recepción es una operación de un
único disparo, es decir que cuando el bus de servicio recibe una solicitud de lectura para un
mensaje, marca el mensaje como consumido y lo devuelve a la
aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor
para escenarios en los que una aplicación puede tolerar no procesar un
mensaje en caso de error. Para comprender esto, considere un
escenario en el que el consumidor emite la solicitud de recepción y, a continuación,
se bloquea antes de procesarlo. Dado que el bus de servicio habrá marcado el
mensaje como consumido, a continuación, cuando la aplicación se reinicia y comienza
a consumir mensajes de nuevo, habrá perdido el mensaje que se había
consumido antes del bloqueo.

En el modo **PeekLock**, la recepción se convierte en una operación de dos fases, que permite
admitir aplicaciones que no pueden tolerar la pérdida de
mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje
que hay que consumir, lo bloquea para impedir que otros consumidores lo reciban y
lo devuelve a la aplicación. Después de que la aplicación termine de
procesar el mensaje (o lo almacene de forma fiable para su futuro procesamiento),
completa la segunda fase del proceso de recepción haciendo una llamada a **Eliminar**
en el mensaje recibido. Cuando el bus de servicio vea la llamada **Eliminar**,
marcará el mensaje como consumido y lo eliminará del tema.

El ejemplo siguiente muestra cómo se pueden recibir y
procesar mensajes con el modo **PeekLock** (no el modo predeterminado). En el ejemplo
siguiente se ejecuta un bucle y se procesan los mensajes en la suscripción "HighMessages" y luego sale cuando no hay más mensajes (de manera alternativa, se podría establecer para que espere mensajes nuevos).

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

## <a name="bkmk_HowToHandleAppCrash"> </a>Actuación ante errores de la aplicación y mensajes que no se pueden leer

El bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de
errores de la aplicación o de las dificultades para procesar un mensaje. Si una
aplicación de recepción es incapaz de procesar el mensaje por alguna razón,
puede llamar al método **unlockMessage** en el mensaje recibido
(en lugar del método **deleteMessage**). Esto hará que el bus de servicio
desbloquee el mensaje del tema y esté disponible para ser
recibido de nuevo, por la misma aplicación que consume o por otra
aplicación de consumo.

También hay un tiempo de espera asociado con un mensaje bloqueado en el
tema, y si la aplicación no puede procesar el mensaje antes del vencimiento
del tiempo de espera (por ejemplo, si se bloquea la aplicación), el bus de servicio
desbloquea el mensaje automáticamente y lo vuelve a poner a disposición
para que se pueda volver a enviar.

En caso de que la aplicación se bloquee después de procesar el mensaje
pero antes de emitir la solicitud **deleteMessage**, el mensaje
se volverá a entregar a la aplicación cuando se reinicie. Esto suele
denominarse **Al menos un procesamiento**, es decir, que cada mensaje se procesará
al menos una vez, pero en ciertas situaciones el mismo mensaje puede
volver a entregarse. Si el escenario no puede tolerar el procesamiento duplicado,
los desarrolladores de aplicaciones deben agregar lógica adicional a su
aplicación para controlar la entrega de mensajes duplicados. Esto suele
conseguirse con el método **getMessageId** del mensaje, que permanecerá
constante en todos los intentos de entrega.

## <a name="bkmk_HowToDeleteTopics"> </a>Eliminación de temas y suscripciones

La principal manera de eliminar temas y suscripciones es usar un objeto
**ServiceBusContract**. Al eliminar un tema también se eliminan todas las suscripciones que están registradas
con el tema. También se pueden eliminar las suscripciones de forma independiente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de las colas del bus de servicio, consulte el
tema de MSDN [Colas, temas y suscripciones del bus de servicio][] para obtener más información.

  [SDK de Azure para Java]: http://www.windowsazure.com/es-es/develop/java/
  [Qué son los temas y las suscripciones del bus de servicio]: #what-are-service-bus-topics
  [Creación de un espacio de nombres de servicio]: #create-a-service-namespace
  [Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
  [Configuración de la aplicación para usar el bus de servicio]: #bkmk_ConfigYourApp
  [Direccionamiento del de un tema]: #bkmk_HowToCreateTopic
  [Direccionamiento del suscripciones]: #bkmk_HowToCreateSubscrip
  [Direccionamiento del mensajes a un tema]: #bkmk_HowToSendMsgs
  [Direccionamiento del mensajes de una suscripción]: #bkmk_HowToReceiveMsgs
  [Direccionamiento del ante errores de la aplicación y mensajes que no se pueden leer]: #bkmk_HowToHandleAppCrash
  [Direccionamiento del de temas y suscripciones]: #bkmk_HowToDeleteTopics
  [Pasos siguientes]: #bkmk_NextSteps
  [Diagrama de temas del bus de servicio]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [Captura de pantalla del nodo Service Bus]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Creación de un espacio de nombres nuevo ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Captura de pantalla de la lista de espacios de nombres]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Captura de pantalla del panel de propiedades]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Captura de pantalla de la clave predeterminada]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Colas, temas y suscripciones del bus de servicio]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
