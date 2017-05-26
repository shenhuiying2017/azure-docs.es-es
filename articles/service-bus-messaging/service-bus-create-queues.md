---
title: Crear aplicaciones que usen colas de Azure Service Bus | Microsoft Docs
description: "Escritura de una aplicación sencilla basada en cola que usa Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 754d91b3-1426-405e-84b4-fd36d65b114a
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: d987aa22379ede44da1b791f034d713a49ad486a
ms.openlocfilehash: 40414edebcd76fc93136cbc14d7a6436fc7f6da5
ms.contentlocale: es-es
ms.lasthandoff: 02/16/2017


---
# <a name="create-applications-that-use-service-bus-queues"></a>Creación de aplicaciones que usan colas del Bus de servicio
Este tema describe las colas de Service Bus y muestra cómo escribir una aplicación sencilla basada en cola que use Service Bus.

Considere un escenario del sector de la venta al por menor en el que los datos de ventas de los terminales de los puntos de venta (PDV) individuales deben enrutarse a un sistema de gestión de inventarios que usa esos datos para determinar cuándo hay que reponer las existencias. Esta solución usa la mensajería de Bus de servicio para la comunicación entre los terminales y el sistema de gestión de inventarios, tal como se muestra en la ilustración siguiente:

![Imagen de colas de Service Bus 1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal del PDV informa de sus datos de venta mediante el envío de mensajes a **DataCollectionQueue**, donde permanecen hasta que el sistema de gestión de inventarios los recupera. Este patrón se denomina a menudo *mensajería asincrónica*, ya que el terminal del PDV no tiene que esperar una respuesta del sistema de gestión de inventarios para continuar el procesamiento.

## <a name="why-queuing"></a>Motivos para usar la cola
Antes de examinar el código necesario para configurar esta aplicación, considere las ventajas de usar una cola en este escenario, en lugar de que los terminales de los PDV se comuniquen directamente (de forma sincrónica) con el sistema de gestión de inventarios.

### <a name="temporal-decoupling"></a>Desacoplamiento temporal
Con el patrón de mensajería asincrónica, no es preciso que los productores y consumidores estén en línea al mismo tiempo. La infraestructura de mensajería almacena de forma confiable los mensajes hasta que la parte consumidora esté preparada para recibirlos. Esto significa que los componentes de la aplicación distribuida pueden desconectarse, ya sea voluntariamente (por ejemplo, para realizar tareas de mantenimiento) o debido al bloqueo de algún componente, sin que afecte a todo el sistema. Además, es posible que la aplicación consumidora solo tenga que estar en línea durante determinados momentos del día. Por ejemplo, en este escenario minorista, el sistema de gestión de inventarios solo debe estar en línea después del final del día laborable.

### <a name="load-leveling"></a>Redistribución de la carga
En muchas aplicaciones, la carga del sistema varía con el paso del tiempo, mientras que el tiempo de procesamiento requerido para cada unidad de trabajo suele ser constante. La intermediación entre consumidores y productores de mensajes con una cola significa que la aplicación consumidora (el trabajador) solo tiene que aprovisionarse para servir una carga media, en lugar de una carga máxima. La profundidad de la cola aumentará y se contraerá a medida que varíe la carga entrante, lo que permite ahorrar dinero directamente en función de la cantidad de infraestructura requerida para dar servicio a la carga de la aplicación.

![Imagen de colas de Service Bus 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Equilibrio de carga
A medida que aumenta la carga, se pueden agregar más procesos de trabajo para que puedan leerse desde la cola de trabajo. Cada mensaje se procesa únicamente por uno de los procesos de trabajo. Además, este equilibrio de carga basado en la extracción permite un uso óptimo de los equipos de trabajo, aunque estos tengan distinta capacidad de procesamiento, ya que extraerán mensajes a su propia velocidad máxima. Este patrón con frecuencia se denomina patrón de consumo de competidor.

![Imagen de colas de Service Bus 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Acoplamiento débil
El uso de colas de mensajes para intermediar entre los consumidores y productores de mensajes proporciona un acoplamiento intrínseco débil entre los componentes. Dado que los productores y consumidores no están relacionados entre sí, un consumidor puede actualizarse sin tener ningún efecto en el productor. Además, la topología de los mensajes puede evolucionar sin que ello afecte a los extremos existentes. Esto se tratará con mayor detalle cuando se expliquen la publicación y la suscripción.

## <a name="show-me-the-code"></a>Visualización del código
En la sección siguiente se muestra cómo usar el Bus de servicio para compilar esta aplicación.

### <a name="sign-up-for-an-azure-account"></a>Registro para obtener una cuenta de Azure
Para empezar a trabajar con el Bus de servicio, se necesita una cuenta de Azure. Si no tiene una, puede registrarse para obtener una cuenta gratuita [aquí](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Creación de un espacio de nombres
Una vez que tenga una suscripción, puede [crear un espacio de nombres de servicio](service-bus-create-namespace-portal.md). Cada espacio de nombres actúa como contenedor con un ámbito para un conjunto de entidades de Bus de servicio. Asigne un nombre único al nuevo espacio de nombres en todas las cuentas de Bus de servicio. 

### <a name="install-the-nuget-package"></a>Instalación del paquete NuGet.
Para usar el espacio de nombres de Bus de servicio, una aplicación debe hacer referencia al ensamblado de Bus de servicio, en concreto Microsoft.ServiceBus.dll. Este ensamblado forma parte del SDK de Microsoft Azure y la descarga está disponible en la [página de descarga de Azure SDK](https://azure.microsoft.com/downloads/). Sin embargo, el [paquete NuGet de Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la forma más sencilla de obtener la API de Service Bus y configurar su aplicación con todas las dependencias de Service Bus.

### <a name="create-the-queue"></a>Creación de la cola
Las operaciones de administración de las entidades de mensajería de Service Bus (colas y temas de publicación o suscripción) se realizan a través de la clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager). Service Bus usa un modelo de seguridad basado en la [firma de acceso compartido (SAS)](service-bus-sas.md). La clase [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) representa un proveedor de tokens de seguridad con métodos de generador integrados que devuelven algunos proveedores de tokens conocidos. Vamos a usar un método [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) para retener las credenciales de SAS. A continuación, se construye la instancia de [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) con la dirección base del espacio de nombres de Service Bus y el proveedor de tokens.

La clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) proporciona métodos para crear, enumerar y eliminar entidades de mensajes. El siguiente código muestra cómo se crea la instancia de [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) y se usa para crear la cola **DataCollectionQueue**.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Tenga en cuenta que hay sobrecargas del método [CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) que permiten ajustar las propiedades de la cola. Por ejemplo, puede configurar el valor predeterminado del período de vida (TTL) para los mensajes enviados a la cola.

### <a name="send-messages-to-the-queue"></a>Envío de mensajes a la cola
En el caso de operaciones en tiempo de ejecución en entidades de Service Bus (por ejemplo, enviar y recibir mensajes), una aplicación debe crear primero un objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). De forma parecida a la clase [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager), la instancia de [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) se crea a partir de la dirección base del espacio de nombres del servicio y del proveedor de tokens.

```csharp
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Los mensajes enviados a las colas de Service Bus y recibidos en ellas son instancias de la clase [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . Esta clase consta de un conjunto de propiedades estándar (como [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) y [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), un diccionario que se usa para conservar las propiedades de la aplicación y un cuerpo de datos de aplicación arbitrarios. Una aplicación puede establecer el cuerpo pasando cualquier objeto serializable (el siguiente ejemplo pasa un objeto **SalesData** que los datos de ventas del terminal del PDV), que usará [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) para serializar el objeto. También se puede proporcionar un objeto [Stream](https://msdn.microsoft.com/library/system.io.stream.aspx).

La manera más fácil de enviar mensajes a una cola determinada, en nuestro caso **DataCollectionQueue**, consiste en usar [CreateMessageSender](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) para crear un objeto [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) directamente desde la instancia de [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Recepción de mensajes desde la cola
La forma más sencilla de recibir mensajes desde la cola es usar un objeto [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que se puede crear directamente en [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) con [CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). Los receptores de mensajes pueden funcionar en dos modos distintos: **ReceiveAndDelete** y **PeekLock**. [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) se establece cuando se crea el receptor del mensaje como un parámetro de la llamada a [CreateMessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagingfactory?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_).

Al usar el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando Service Bus recibe la solicitud, marca el mensaje como consumido y lo devuelve a la aplicación. El modo **ReceiveAndDelete** es el modelo más sencillo y funciona mejor en aquellos escenarios en que la aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Dado que el Bus de servicio marcó el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock**, la recepción se convierte en una operación de dos fases que hace posible admitir aplicaciones que no pueden tolerar mensajes perdidos. Cuando el Bus de servicio recibe la solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para evitar que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) en el mensaje recibido. Cuando Service Bus ve la llamada [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), marca el mensaje como consumido.

Hay otros dos resultados posibles. En primer lugar, si por cualquier motivo la aplicación no puede procesar el mensaje, puede llamar a [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) en el mensaje recibido (en lugar de [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Esto hará que el Bus de servicio desbloquee el mensaje y esté disponible para recibirse de nuevo, ya sea por el mismo consumidor o por otro consumidor que lo esté completando. En segundo lugar, hay un tiempo de espera asociado con el bloqueo y, si la aplicación no puede procesar el mensaje antes de que expire el tiempo de espera de bloqueo (por ejemplo, si la aplicación se bloquea), Service Bus desbloqueará el mensaje y hará que esté disponible para recibirse de nuevo (básicamente, realizando una operación [Abandonar](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) de manera predeterminada).

Tenga en cuenta si la aplicación se bloquea tras procesar el mensaje, pero antes la emisión de la solicitud de [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esto se suele denominar procesamiento *Al menos una vez*. Esto significa que cada mensaje se procesará al menos una vez, aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no tolera el procesamiento duplicado, hará falta lógica adicional en la aplicación para detectar duplicados, lo que puede con la propiedad [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) del mensaje. El valor de esta propiedad permanece constante en todos los intentos de entrega. Esto se conoce como procesamiento *Una sola vez*.

El código que se muestra aquí recibe y procesa un mensaje con el modo **PeekLock**, que es el valor predeterminado si no se proporciona explícitamente ningún valor [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode).

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>Uso del cliente de la cola
En los ejemplos anteriores de esta sección se crearon objetos [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender) y [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) directamente desde [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) para enviar mensajes a la cola y recibirlos desde ella, respectivamente. Un enfoque alternativo es usar el objeto [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), que admite tanto operaciones de envío como de recepción, además de características más avanzadas como sesiones.

```csharp
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);

BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos de las colas, consulte [Creación de aplicaciones que usan temas y suscripciones de Service Bus](service-bus-create-topics-subscriptions.md) para continuar este tema sobre el uso de las funcionalidades de publicación y suscripción de los temas y las suscripciones de Service Bus.


