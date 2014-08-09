<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Java) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="waltpo" solutions="" manager="" editor="mollybos" />

Utilización de las colas del bus de servicio
============================================

Esta guía le mostrará cómo usar las colas del bus de servicio. Los ejemplos están escritos en Java y utilizan el [SDK de Azure para Java](http://msdn.microsoft.com/es-es/library/windowsazure/hh690953(v=vs.103).aspx). Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

Tabla de contenido
------------------

-   [¿Qué son las colas del bus de servicio?](#what-are-service-bus-queues)
-   [Creación de un espacio de nombres de servicio](#create-a-service-namespace)
-   [Obtención de credenciales de administración predeterminadas para el espacio de nombres](#obtain-default-credentials)
-   [Configuración de la aplicación para usar el bus de servicio](#bkmk_ConfigApp)
-   [Creación de un proveedor de tokens de seguridad](#bkmk_HowToCreateQueue)
-   [Creación de una cola](#bkmk_HowToCreateQueue)
-   [Envío de mensajes a una cola](#bkmk_HowToSendMsgs)
-   [Recepción de mensajes de una cola](#bkmk_HowToReceiveMsgs)
-   [Actuación ante errores de la aplicación y mensajes que no se pueden leer](#bkmk_HowToHandleAppCrashes)
-   [Pasos siguientes](#bkmk_NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Configuración de la aplicación para usar el bus de servicio
-----------------------------------------------------------

Agregue las siguientes instrucciones importantes en la parte superior del archivo Java:

    // Incluir las siguientes importaciones para utilizar las API del bus de servicio
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*; 
    import com.microsoft.windowsazure.services.core.*; 
    import javax.xml.datatype.*;

Creación de una cola
--------------------

Las operaciones de administración para las colas de bus de servicio se pueden realizar por medio de la clase **ServiceBusContract**. Un objeto **ServiceBusContract** se construye con una configuración adecuada que encapsula los permisos del token para administrarlo, y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar colas. El ejemplo que aparece a continuación muestra cómo se puede usar un objeto **ServiceBusService** para crear una cola llamada "TestQueue", con un espacio de nombres denominado "HowToSample":

    Configuration config = 
        ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

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

En QueueInfo hay métodos que permiten ajustar las propiedades de la cola (por ejemplo: para configurar el valor de "período de vida" predeterminado que se aplicará a los mensajes enviados a la cola). El ejemplo siguiente muestra cómo crear una cola llamada "TestQueue" con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Tenga en cuenta que puede usar el método **listQueues** en los objetos **ServiceBusContract** para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

Envío de mensajes a una cola
----------------------------

Para enviar un mensaje a una cola de bus de servicio, su aplicación obtendrá un objeto **ServiceBusContract**. El código que aparece a continuación muestra cómo enviar un mensaje a la cola "TestQueue" que hemos creado arriba dentro de nuestro espacio de nombres de servicio "HowToSample":

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

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase **BrokeredMessage**. Los objetos **BrokeredMessage** cuentan con un conjunto de métodos estándar (como **getLabel**, **getTimeToLive**, **setLabel** y **setTimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor de **BrokeredMessage** y, a continuación, se usará el serializador pertinente para serializar el objeto. También se puede proporcionar un **java.IO.InputStream**.

En el ejemplo que aparece a continuación se indica cómo enviar cinco mensajes de prueba al elemento **MessageSender** de "TestQueue" que se ha obtenido en el fragmento de código anterior:

    for (int i=0; i<5; i++)
    {
         // Crear mensajes, pasando un mensaje de cadena al cuerpo.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Configurar una propiedad adicional específica de la aplicación.
         message.setProperty("MyProperty", i); 
         // Enviar mensaje a la cola
         service.sendQueueMessage("TestQueue", message);
    }

Las colas del bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

Recepción de mensajes de una cola
---------------------------------

La forma principal de recibir mensajes desde una cola es usando un objeto **ServiceBusContract**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** (predeterminado) es el modelo más sencillo y funciona mejor para las situaciones en las que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la llamada a **Delete** en el mensaje recibido. Cuando el bus de servicio ve la llamada **Delete**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo que aparece a continuación, se indica cómo se pueden procesar y recibir los mensajes usando el modo **PeekLock** (no el predeterminado). En el ejemplo que aparece a continuación se crea un bucle infinito y se procesan mensajes a medida que llegan a "TestQueue":

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
                // Mostrar el mensaje de la cola.
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
                // Eliminar el mensaje de la cola.
                System.out.println("Deleting this message.");
                //service.deleteMessage(message);
            }  
            else  
            {        
                System.out.println("Finishing up - no more messages.");        
                break; 
                // Agregado para no admitir más mensajes.
                // Puede esperar a que se agreguen más mensajes.
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

Actuación ante errores de la aplicación y mensajes que no se pueden leer
------------------------------------------------------------------------

El bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **At Least Once Processing**; es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

Pasos siguientes
----------------

Ahora que ya conoce los aspectos básicos de las colas del bus de servicio, consulte el tema [Colas, temas y suscripciones](http://msdn.microsoft.com/es-es/library/windowsazure/hh367516.aspx) de MSDN para obtener más información.

