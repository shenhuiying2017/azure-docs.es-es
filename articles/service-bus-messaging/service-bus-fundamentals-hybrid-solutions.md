---
title: "Información general de los elementos fundamentales de Azure Service Bus | Microsoft Docs"
description: "Una introducción a la utilización de Service Bus para conectar aplicaciones de Azure a otro software."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 12654cdd-82ab-4b95-b56f-08a5a8bbc6f9
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: fab765480a2f480e8c54035d903d24843490ee38
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-service-bus"></a>Azure Service Bus

Cuando una aplicación o servicio se ejecutan en la nube o de manera local, a menudo tienen que interactuar con otras aplicaciones o servicios. Para proporcionar una manera enormemente útil de hacerlo, Microsoft Azure ofrece Service Bus. En este artículo nos centramos en esta tecnología, describiendo qué es y los beneficios que ofrece.

## <a name="service-bus-fundamentals"></a>Elementos fundamentales de Service Bus

Las distintas situaciones requieren distintos estilos de comunicación. A veces, permitir a las aplicaciones enviar y recibir mensajes mediante una simple cola es la mejor solución. En otras situaciones, una cola ordinaria no es suficiente y es mejor una cola con un mecanismo de publicación y suscripción. En algunos casos, todo lo que hace falta es una conexión entre las aplicaciones y no se requieren colas. Azure Service Bus ofrece las tres opciones, por lo que sus aplicaciones pueden interactuar de varias maneras diferentes.

Service Bus es un servicio en la nube multiinquilino, lo que significa que varios usuarios comparten el servicio. Cada usuario, como desarrollador de aplicaciones, crea un *espacio de nombres* y luego define los mecanismos de comunicación que necesita dentro de ese espacio de nombres. La figura 1 muestra esta arquitectura:

![][1]

**Figura 1: Service Bus proporciona un servicio multiinquilino para conectar aplicaciones a través de la nube.**

Dentro de un espacio de nombres puede usar uno o más ejemplos de los tres mecanismos de comunicación diferentes, que se conectan a las aplicaciones de forma distinta. Las opciones son las siguientes:

* *Colas*, que permiten una comunicación unidireccional. Cada cola funciona como un intermediario (al que se le llama a veces *agente*) que almacena los mensajes enviados hasta que se reciben. Cada mensaje lo recibe un único destinatario.
* *Temas*, que proporcionan una comunicación unidireccional mediante *suscripciones* (un solo tema puede tener varias suscripciones). Al igual que las colas, los temas funcionan como agentes, pero cada suscripción puede usar opcionalmente un filtro para recibir solo los mensajes que cumplan criterios concretos.
* *Retransmisiones*, que permiten una comunicación bidireccional. A diferencia de las colas y los temas, las retransmisiones no almacenan mensajes que se encuentran en proceso; no son agentes. Simplemente los transmiten a la aplicación de destino.

Cuando cree una cola, un tema o una retransmisión, asígneles un nombre. Cuando se combina con el nombre del espacio de nombres, se crea un identificador exclusivo para el objeto. Las aplicaciones pueden proporcionar este nombre a Service Bus y, a continuación, usar esa cola, tema o retransmisión para comunicarse entre sí. 

Para usar cualquiera de estos objetos en el escenario de retransmisión, las aplicaciones de Windows pueden usar Windows Communication Foundation (WCF). Este servicio se conoce como [WCF Relay](../service-bus-relay/relay-what-is-it.md). En el caso de las colas y los temas, las aplicaciones de Windows pueden usar las API de mensajería definidas por Service Bus. Para facilitar el uso de estos objetos desde aplicaciones que no sean de Windows, Microsoft proporciona SDK para Java, Node.js y otros lenguajes. También se puede acceder a las colas y a los temas mediante las [API de REST](/rest/api/servicebus/) sobre HTTP(s). 

Es importante comprender que aunque Service Bus se ejecute en la nube (es decir, en los centros de datos de Microsoft Azure), las aplicaciones que usa pueden ejecutarse desde cualquier lugar. Puede usar Service Bus para conectarse a aplicaciones que se ejecutan, por ejemplo, en Azure o aplicaciones que se ejecutan dentro de su propio centro de datos. También puede usarlo para conectarse a una aplicación que se ejecuta en Azure o a otra plataforma en la nube con una aplicación local o con tabletas y teléfonos. Es posible incluso conectar aparatos domésticos, sensores y otros dispositivos a una aplicación central, o conectar estos dispositivos entre sí. Service Bus es un mecanismo de comunicación en la nube al que se puede acceder desde prácticamente cualquier lugar. La forma en la que lo use depende de las tareas que tengan que realizar sus aplicaciones.

## <a name="queues"></a>Colas

Supongamos que decide conectar dos aplicaciones con una cola de Service Bus. La figura 2 muestra esta situación:

![][2]

**Figura 2: las colas de Service Bus proporcionan una cola asincrónica unidireccional.**

Un usuario envía un mensaje a la cola de Service Bus y un receptor lo recoge más tarde. Una cola puede tener solo un único receptor, como muestra la Figura 2, o bien varias aplicaciones pueden leer de la misma cola. En el segundo caso, cada mensaje lo lee un solo receptor. Para un servicio de multidifusión, es preciso utilizar un tema.

Cada mensaje tiene dos partes: un conjunto de propiedades, cada una de ellas, un par clave/valor, y una carga de mensaje. La carga de mensaje puede ser binaria, texto o incluso XML. El modo en que se usan depende de lo que una aplicación esté tratando de hacer. Por ejemplo, una aplicación que envía un mensaje sobre una venta reciente podría incluir las propiedades **Seller="Ava"** y **Amount=10000**. El cuerpo del mensaje puede contener una imagen escaneada del contrato firmado de la venta o, si no la hubiera, puede aparecer vacío.

El receptor puede leer los mensajes de la cola de Service Bus de dos formas distintas. La primera opción, denominada *[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)*, recibe un mensaje de la cola y lo elimina de inmediato. Esta opción es sencilla, pero si se produce un error por parte del receptor antes de que finalice el procesamiento del mensaje, este se perderá. Puesto que se quita de la cola, ningún otro receptor podrá obtener acceso a él. 

La segunda opción, *[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)*, se ha creado para solucionar este problema. Como ocurre con **ReceiveAndDelete**, una lectura de **PeekLock** quita un mensaje de la cola. Sin embargo, no elimina el mensaje. En este caso, bloquea el mensaje, por lo que se oculta a los demás receptores. A continuación, espera a que se produzca uno de los tres eventos:

* Si el receptor procesa el mensaje correctamente, invoca [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) y la cola elimina el mensaje. 
* Si el receptor decide que no puede procesar el mensaje correctamente, invoca [Abandon()](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync). La cola quita el bloqueo del mensaje y se pone a disposición de otros receptores.
* Si el receptor no invoca ninguno de estos métodos en un período configurable (60 segundos de forma predeterminada), la cola presupone que se ha producido un error con el receptor. En ese caso, se comporta como si el receptor hubiera llamado a **Abandon**, lo que hace que el mensaje esté disponible para otros receptores.

Observe lo que puede ocurrir aquí: el mismo mensaje puede enviarse dos veces, quizás a dos receptores distintos. Las aplicaciones que usan colas de Service Bus deben estar preparadas para este evento. Para facilitar la detección de duplicados, cada mensaje dispone de una propiedad [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid#Microsoft_Azure_ServiceBus_Message_MessageId) única que no se modifica de forma predeterminada, independientemente del número de veces que se lea un mensaje en una cola. 

Las colas son útiles en determinadas situaciones. Permiten a las aplicaciones comunicarse incluso si ambos no se están ejecutando a la vez. Esto es especialmente útil con aplicaciones móviles y por lotes. Una cola con varios receptores también proporciona un equilibrio de carga automático, ya que los mensajes enviados se difunden a través de esos receptores.

## <a name="topics"></a>Temas

Aunque son útiles, las colas no siempre son la solución adecuada. En ocasiones, los temas son mejores. La figura 3 ilustra esta idea:

![][3]

**Figura 3: En función del filtro que especifique la aplicación de suscripción, puede recibir algunos de los mensajes enviados a un tema de Service Bus, o todos ellos.**

Un *tema* se parece en muchos aspectos a una cola. Los remitentes envían mensajes a un tema de la misma forma en la que envían mensajes a una cola, y esos mensajes tienen la misma apariencia que las colas. La diferencia es que los temas permiten a cada aplicación receptora crear su propia *suscripción* mediante la definición opcional de un *filtro*. Un suscriptor ve, por lo tanto, solo los mensajes que coincidan con ese filtro. Por ejemplo, la Figura 3 muestra un remitente y un tema con tres suscriptores, cada uno con su propio filtro:

* El suscriptor 1 recibe solo mensajes que contienen la propiedad *Seller="Ava"*.
* El suscriptor 2 recibe mensajes que contienen la propiedad *Seller="Ruby"* o la propiedad *Amount* cuyo valor es superior a 100.000. Quizás Ruby es la directora de ventas, y por eso quiere ver sus propias ventas y todas las ventas importantes con independencia de quién las haga.
* El suscriptor 3 ha establecido su filtro en *True*, lo que significa que recibe todos los mensajes. Por ejemplo, esta aplicación puede ser responsable de mantener una traza de auditoría y, por tanto, necesita ver todos los mensajes.

Como ocurre con las colas, los suscriptores a un tema pueden leer los mensajes utilizando [ReceiveAndDelete o PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode). Sin embargo, a diferencia de las colas, varias suscripciones pueden recibir un mensaje individual enviado a un tema. Este enfoque, que se denomina normalmente *publicar y suscribir* (o *pub/sub*), es útil cuando hay varias aplicaciones interesadas en los mismos mensajes. Si se define el filtro adecuado, cada suscriptor puede pulsar solo la parte de la secuencia de mensaje que necesita ver.

## <a name="relays"></a>Retransmisiones

Tanto las colas como los temas proporcionan una comunicación asincrónica unidireccional a través de un agente. El tráfico fluye en una única dirección y no existe una conexión directa entre los remitentes y los receptores. Sin embargo, ¿qué ocurre si no quiere esa conexión? Supongamos que sus aplicaciones necesitan enviar y recibir mensajes, o que quizás quiere un vínculo directo entre ellas y necesita un agente para almacenar los mensajes. Para abordar escenarios como este, Service Bus proporciona *retransmisiones*, como muestra la figura 4:

![][4]

**Figura 4: Service Bus Relay proporciona comunicación sincrónica bidireccional entre aplicaciones.**

La pregunta obvia que se plantea acerca de las retransmisiones es la siguiente: ¿por qué habría de usar una? Incluso si no necesito colas, ¿por qué hacer que las aplicaciones se comuniquen a través de un servicio en la nube en lugar de interactuar directamente? La respuesta es que hablar directamente puede ser más complicado de lo que se piensa.

Supongamos que desea conectar dos aplicaciones locales que se ejecutan dentro de centros de datos corporativos. Cada una de esas aplicaciones se encuentran protegidas por un firewall y cada centro de datos usa probablemente la traducción de direcciones de red (NAT). El firewall bloquea los datos de entrada en casi todos los puertos y NAT implica que el equipo en el que se ejecuta cada aplicación no dispone de una dirección IP fija a la que se pueda llegar directamente desde fuera del centro de datos. Si no se cuenta con ayuda adicional, conectar esas aplicaciones mediante una conexión pública a Internet es complicado.

Una retransmisión de Service Bus puede ser de gran ayuda. Para comunicarse de forma bidireccional a través de una retransmisión, cada aplicación establece una conexión TCP saliente con Service Bus y, a continuación, la mantiene abierta. Todas las comunicaciones entre las dos aplicaciones se llevarán a cabo a través de esas conexiones. Puesto que cada conexión se estableció desde dentro del centro de datos, el firewall permite el tráfico entrante a cada aplicación sin abrir puertos nuevos. Este enfoque también evita el problema de NAT, puesto que cada aplicación cuenta con un extremo coherente en la nube a través de la comunicación. Las aplicaciones pueden evitar los problemas que podrían complicar la comunicación mediante el intercambio de datos a través del relé. 

Para utilizar retransmisiones de Service Bus, las aplicaciones se basan en Windows Communication Foundation (WCF). Service Bus proporciona enlaces a WCF que facilitan la interactuación de las aplicaciones de Windows a través de las retransmisiones. Las aplicaciones que ya usan WCF pueden normalmente especificar solo uno de esos enlaces y después comunicarse con los demás a través de una retransmisión. Sin embargo, a diferencia de las colas y los temas, el uso, cuando sea posible, de retransmisiones desde aplicaciones que no sean de Windows requiere cierto esfuerzo de programación; no se proporcionan bibliotecas estándar.

A diferencia de lo que ocurre con las colas y los temas, las aplicaciones no crean retransmisiones explícitamente. En este caso, cuando una aplicación que desea recibir mensajes establece una conexión TCP con Service Bus, se crea una retransmisión automáticamente. Cuando se abandona la conexión, el relé se elimina. Para que una aplicación encuentre una retransmisión creada por un agente de escucha específico, Service Bus proporciona un registro que permite a las aplicaciones localizar una retransmisión específica por nombre.

Las retransmisiones son la solución adecuada cuando se necesita una comunicación directa entre las aplicaciones. Por ejemplo, piense en un sistema de reserva de una aerolínea que se ejecute en un centro de datos local y al que deba poder accederse desde mostradores de facturación, dispositivos móviles y otros equipos. Las aplicaciones que se ejecuten en todos esos sistemas podrían confiar en las retransmisiones de Service Bus en la nube para comunicarse, con independencia del lugar en el que lo hagan.

## <a name="summary"></a>Resumen

La conexión de aplicaciones forma parte desde siempre de la creación de soluciones completas. El tipo de escenarios que requieren las aplicaciones y los servicios para comunicarse entre sí se configura de forma que aumente en función de las aplicaciones y los dispositivos que se conectan a Internet. Al proporcionar tecnologías basadas en la nube para lograrlo a través de colas, temas y retransmisiones, el objetivo de Service Bus es facilitar la implementación de esta función fundamental y ofrecer una disponibilidad más amplia.

## <a name="next-steps"></a>pasos siguientes

Ahora que conoce los fundamentos de Azure Service Bus, siga estos vínculos para obtener más información.

* [Utilización de las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* Uso de [temas de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* Uso de [Service Bus Relay](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
* [Ejemplos de Service Bus](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
