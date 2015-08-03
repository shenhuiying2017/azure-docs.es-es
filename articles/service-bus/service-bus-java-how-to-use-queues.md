<properties
	pageTitle="Utilización de las colas del Bus de servicio (Java) - Azure"
	description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Java."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

Esta guía describe cómo utilizar las colas del Bus de servicio. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java][]. Entre los escenarios que abarca se incluyen la **creación de colas**, el **envío y la recepción de mensajes** y la **eliminación de colas**.

[AZURE.INCLUDE [service-bus-java-how-to-create-queue](../../includes/service-bus-java-how-to-create-queue.md)]

## Configuración de la aplicación para usar el Bus de servicio
Asegúrese de que ha instalado el [SDK de Azure para Java][] antes de compilar este ejemplo. Si está usando Eclipse, puede instalar el [Kit de herramientas de Azure para Eclipse][], que incluye el SDK de Azure para Java. A continuación, puede agregar las **Bibliotecas de Microsoft Azure para Java** al proyecto: ![](media/service-bus-java-how-to-use-queues/eclipselibs.png)

Agregue las siguientes instrucciones import a la parte superior del archivo Java:

	// Include the following imports to use Service Bus APIs
	import com.microsoft.windowsazure.services.servicebus.*;
	import com.microsoft.windowsazure.services.servicebus.models.*;
	import com.microsoft.windowsazure.core.*;
	import javax.xml.datatype.*;

## Creación de una cola

Las operaciones de administración para las colas del Bus de servicio se pueden realizar por medio de la clase **ServiceBusContract**. Un objeto **ServiceBusContract** se construye con una configuración adecuada que encapsula el token de SAS con permisos para administrarlo, y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar colas. El ejemplo que aparece a continuación muestra cómo se puede usar un objeto **ServiceBusService** para crear una cola llamada "TestQueue" con un espacio de nombres denominado "HowToSample":

		Configuration config =
			ServiceBusConfiguration.configureWithSASAuthentication(
					"HowToSample",
					"RootManageSharedAccessKey",
					"SAS_key_value",
					".servicebus.windows.net"
					);

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

En QueueInfo hay métodos que permiten ajustar las propiedades de la cola (por ejemplo, establecer el valor de "período de vida" predeterminado que se aplicará a los mensajes enviados a la cola). El ejemplo siguiente muestra cómo crear una cola llamada "TestQueue" con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

Tenga en cuenta que puede usar el método **listQueues** en los objetos **ServiceBusContract** para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## Envío de mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, su aplicación obtendrá un objeto **ServiceBusContract**. El código que aparece a continuación muestra cómo enviar un mensaje a la cola "TestQueue" que hemos creado arriba dentro de nuestro espacio de nombres de servicio "HowToSample":

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Los mensajes enviados a las colas del Bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de métodos estándar (como **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor de **BrokeredMessage** y, a continuación, se usará el serializador pertinente para serializar el objeto. Como alternativa, también se puede proporcionar un elemento **java.IO.InputStream**.

En el ejemplo que aparece a continuación se indica cómo enviar cinco mensajes de prueba al elemento **MessageSender** de "TestQueue" que se ha obtenido en el fragmento de código anterior:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## Recepción de mensajes de una cola

La forma principal de recibir mensajes desde una cola es usando un objeto **ServiceBusContract**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe una solicitud de lectura de un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** (que es el predeterminado) es el modelo más sencillo y que funciona mejor para las situaciones en las que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la llamada a **Delete** en el mensaje recibido. Cuando el Bus de servicio ve la llamada a **Delete**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación se indica cómo se pueden recibir y procesar mensajes con el modo **PeekLock** (no el modo predeterminado). En el ejemplo que aparece a continuación se crea un bucle infinito y se procesan mensajes a medida que llegan a "TestQueue":

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

		while(true)  {
	         ReceiveQueueMessageResult resultQM =
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());
			    // Display the queue message.
			    System.out.print("From queue: ");
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
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
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

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar de al método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. Esto suele conseguirse usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que ya conoce los aspectos básicos de las colas del Bus de servicio, consulte el tema [Colas, temas y suscripciones][] de MSDN para obtener más información.

  [SDK de Azure para Java]: http://azure.microsoft.com/develop/java/
  [Kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/es-es/library/azure/hh694271.aspx
  [What are Service Bus Queues?]: #what-are-service-bus-queues
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Configure Your Application to Use Service Bus]: #bkmk_ConfigApp
  [How to: Create a Security Token Provider]: #bkmk_HowToCreateQueue
  [How to: Send Messages to a Queue]: #bkmk_HowToSendMsgs
  [How to: Receive Messages from a Queue]: #bkmk_HowToReceiveMsgs
  [How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrashes
  [Next Steps]: #bkmk_NextSteps
  [Azure Management Portal]: http://manage.windowsazure.com/
  [Colas, temas y suscripciones]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
 

<!---HONumber=July15_HO4-->