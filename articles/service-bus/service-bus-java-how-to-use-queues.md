<properties
	pageTitle="Uso de colas del Bus de servicio con Java | Microsoft Azure"
	description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Java."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar las colas del Bus de servicio. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java][]. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

## ¿Qué son las colas del Bus de servicio?

Las colas del Bus de servicio son compatibles con el modelo de comunicación de **mensajería asíncrona**. Cuando se usan colas, los componentes de una aplicación distribuida no se comunican directamente entre sí, sino que intercambian mensajes a través de una cola, que actúa como un intermediario (agente). El productor del mensaje (remitente) manda un mensaje a la cola y, a continuación sigue con su procesamiento. De forma asíncrona, el destinatario del mensaje (receptor) extrae el mensaje de la cola y lo procesa. El productor no tiene que esperar una respuesta del destinatario para continuar el proceso y el envío de más mensajes. Las colas ofrecen una entrega de mensajes según el modelo **El primero en entrar es el primero en salir (FIFO)** a uno o más destinatarios de la competencia. Es decir, normalmente los receptores reciben y procesan los mensajes en el orden en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Las colas del Bus de servicio son una tecnología de uso general que puede utilizarse en una variedad de escenarios:

- Comunicación entre los roles de trabajo y web en una aplicación de Azure de niveles múltiples.
- Comunicación entre aplicaciones locales y aplicaciones hospedadas de Azure en una solución híbrida.
- Comunicación entre componentes de una aplicación distribuida que se ejecuta en local en distintas organizaciones o departamentos de una organización.

El uso de colas permite escalar las aplicaciones horizontalmente con mayor facilidad y dotar de resiliencia a su arquitectura.

## Creación de un espacio de nombres de servicio

Para empezar a usar colas del Bus de servicio en Azure, primero hay que crear un espacio de nombres. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación.

Para crear un espacio de nombres:

1.  Inicie sesión en el [Portal de Azure clásico][].

2.  En el panel de navegación izquierdo del Portal, haga clic en **Bus de servicio**.

3.  En el panel inferior del Portal, haga clic en **Crear**. ![](./media/service-bus-java-how-to-use-queues/sb-queues-03.png)

4.  En el cuadro de diálogo **Agregar un nuevo espacio de nombres**, escriba un nombre de espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible. ![](./media/service-bus-java-how-to-use-queues/sb-queues-04.png)

5.  Después de asegurarse de que el nombre de espacio de nombres está disponible, seleccione el país o región en el que debe hospedarse el espacio de nombres (asegúrese de que usa el mismo país o la misma región en los que está realizando la implementación de los recursos de proceso).

	IMPORTANTE: seleccione la **misma región** que vaya a seleccionar para la implementación de la aplicación. Con esto conseguirá el máximo rendimiento.

6. 	Deje los demás campos del cuadro de diálogo con los valores predeterminados (**Mensajería** y **Nivel estándar**) y, a continuación, haga clic en la marca de verificación. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

El espacio de nombres que creó tardará un momento en activarse y, después, aparecerá en el Portal de Azure. Espere hasta que el estado del espacio de nombres sea **Activo** antes de continuar.

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres. Puede obtener estas credenciales en el portal.

1.  En el panel de navegación izquierdo, haga clic en el nodo **Bus de servicio** para ver la lista de espacios de nombres disponibles: ![](./media/service-bus-java-how-to-use-queues/sb-queues-13.png)

2.  Haga clic en el espacio de nombres que acaba de crear en la lista mostrada.

3.  Haga clic en **Configurar** para ver las directivas de acceso compartido para el espacio de nombres. ![](./media/service-bus-java-how-to-use-queues/sb-queues-14.png)

4.  Anote la clave principal o cópiela en el Portapapeles.

## Configuración de la aplicación para usar el Bus de servicio

Asegúrese de que ha instalado el [SDK de Azure para Java][] antes de compilar este ejemplo. Si usa Eclipse, puede instalar el [Kit de herramientas de Azure para Eclipse][], que incluye el SDK de Azure para Java. Después puede agregar las **Bibliotecas de Microsoft Azure para Java** al proyecto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Agregue las siguientes instrucciones `import` al principio del archivo Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## Creación de una cola

Las operaciones de administración para las colas de bus de servicio se pueden realizar por medio de la clase **ServiceBusContract**. Un objeto **ServiceBusContract** se crea con una configuración adecuada que encapsula el token de SAS con permisos para administrarlo, y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar colas. El ejemplo que aparece a continuación muestra cómo se puede usar un objeto **ServiceBusService** para crear una cola llamada "TestQueue", con un espacio de nombres denominado "HowToSample":

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

En **QueueInfo** hay métodos que permiten ajustar las propiedades de la cola, por ejemplo, establecer el valor de período de vida (TTL) predeterminado que se aplicará a los mensajes enviados a la cola. El ejemplo siguiente muestra cómo crear una cola llamada `TestQueue` con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

Tenga en cuenta que puede usar el método **listQueues** en los objetos **ServiceBusContract** para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, su aplicación obtiene un objeto **ServiceBusContract**. El código siguiente muestra cómo enviar un mensaje a la cola `TestQueue` creada anteriormente en el espacio de nombres de `HowToSample`.

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

Los mensajes enviados a las colas del Bus de servicio y recibidos en ellas son instancias de la clase [BrokeredMessage][]. Los objetos [BrokeredMessage][] cuentan con un conjunto de propiedades estándar (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor de [BrokeredMessage][] y, a continuación, se usará el serializador pertinente para serializar el objeto. También puede especificar un objeto **java.IO.InputStream**.

En el ejemplo siguiente se muestra cómo enviar cinco mensajes de prueba al `TestQueue` **MessageSender** obtenido en el fragmento de código anterior.

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

El tamaño máximo de mensaje que admiten las colas del Bus de servicio es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## mensajes de una cola

La forma principal de recibir mensajes desde una cola es usando un objeto **ServiceBusContract**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** (que es el predeterminado) es el modelo más sencillo y que funciona mejor para las situaciones en las que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la llamada a **Delete** en el mensaje recibido. Cuando el bus de servicio ve la llamada **Delete**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes con el modo **PeekLock** (no es el modo predeterminado). En el ejemplo que aparece a continuación se crea un bucle infinito y se procesan mensajes a medida que llegan a "TestQueue":

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

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. Esto suele conseguirse usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que ya conoce los aspectos básicos de las colas de Bus de servicio, vea [Colas, temas y suscripciones][] para obtener más información.

Para obtener más información, consulte el [Centro de desarrolladores de Java](/develop/java/).


  [SDK de Azure para Java]: http://azure.microsoft.com/develop/java/
  [Kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0525_2016-->