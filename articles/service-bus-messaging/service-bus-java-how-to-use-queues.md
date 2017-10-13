---
title: Uso de colas de Service Bus de Azure con Java | Microsoft Docs
description: "Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Java."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 170f431525ffdc93a01fc085e48e69c3a774968e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Uso de colas de Service Bus con Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar las colas del Bus de servicio. Los ejemplos están escritos en Java y utilizan el [Azure SDK para Java][Azure SDK for Java]. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configuración de la aplicación para usar el Bus de servicio
Asegúrese de que ha instalado [Azure SDK para Java][Azure SDK for Java] antes de compilar este ejemplo. Si usa Eclipse, puede instalar el [Kit de herramientas de Azure para Eclipse][Azure Toolkit for Eclipse], que incluye Azure SDK para Java. Después puede agregar las **Bibliotecas de Microsoft Azure para Java** al proyecto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Agregue las siguientes instrucciones `import` al principio del archivo Java:

```java
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Creación de una cola
Las operaciones de administración para las colas de Service Bus se pueden realizar por medio de la clase **ServiceBusContract**. Un objeto **ServiceBusContract** se crea con una configuración adecuada que encapsula el token de SAS con permisos para administrarlo, y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar colas. El ejemplo que aparece a continuación muestra cómo se puede usar un objeto **ServiceBusService** para crear una cola llamada `TestQueue`, con un espacio de nombres denominado `HowToSample`:

```java
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
```

En `QueueInfo` hay métodos que permiten ajustar las propiedades de la cola, por ejemplo, establecer el valor de período de vida (TTL) predeterminado que se aplicará a los mensajes enviados a la cola. El ejemplo siguiente muestra cómo crear una cola llamada `TestQueue` con un tamaño máximo de 5 GB:

````java
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Tenga en cuenta que puede usar el método `listQueues` en los objetos **ServiceBusContract** para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## <a name="send-messages-to-a-queue"></a>mensajes a una cola
Para enviar un mensaje a una cola de Service Bus, su aplicación obtiene un objeto **ServiceBusContract**. El código siguiente muestra cómo enviar un mensaje a la cola `TestQueue` creada anteriormente en el espacio de nombres de `HowToSample`.

```java
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
```

Los mensajes enviados a las colas de Service Bus y recibidos en ellas son instancias de la clase [BrokeredMessage][BrokeredMessage]. Los objetos [BrokeredMessage][BrokeredMessage] cuentan con un conjunto de propiedades estándar (como [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) y [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor de [BrokeredMessage][BrokeredMessage] y, a continuación, se usará el serializador pertinente para serializar el objeto. También puede especificar un objeto **java.IO.InputStream**.

En el ejemplo siguiente se muestra cómo enviar cinco mensajes de prueba a `TestQueue` **MessageSender** obtenido en el fragmento de código anterior:

```java
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

El tamaño máximo de mensaje que admiten las colas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## <a name="receive-messages-from-a-queue"></a>mensajes de una cola
La forma principal de recibir mensajes desde una cola es usando un objeto **ServiceBusContract**. Los mensajes recibidos pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el bus de servicio recibe una solicitud de lectura para un mensaje de la cola, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** (que es el modelo predeterminado) es el modelo más sencillo y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla.
Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción mediante la llamada a **Delete** en el mensaje recibido. Cuando Service Bus ve la llamada **Delete**, marca el mensaje como consumido y lo elimina de la cola.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes con el modo **PeekLock** (no es el modo predeterminado). En el ejemplo que aparece a continuación se crea un bucle infinito y se procesan mensajes a medida que llegan a `TestQueue`:

```java
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
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer
El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realiza la llamada al método **unlockMessage** en el mensaje recibido (en lugar del método **deleteMessage**). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación se bloquee después de procesar el mensaje y antes de emitir la solicitud **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina *Al menos un procesamiento*, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. Esto suele conseguirse usando el método **getMessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya conoce los aspectos básicos de las colas de Service Bus, consulte [Colas, temas y suscripciones][Queues, topics, and subscriptions], donde encontrará más información.

Para obtener más información, consulte el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
