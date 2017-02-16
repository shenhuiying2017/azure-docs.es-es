---
title: "Preguntas más frecuentes (FAQ) sobre Azure Service Bus | Microsoft Docs"
description: Respuestas a algunas preguntas frecuentes acerca del Bus de servicio de Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;juconway
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: ef8fc057ca6b15fe25bcf549ad92ceb6cf5722e4


---
# <a name="service-bus-faq"></a>Preguntas más frecuentes sobre el Bus de servicio
En este artículo se responden algunas preguntas frecuentes sobre el Bus de servicio de Microsoft Azure. También puede visitar [Preguntas más frecuentes de soporte técnico de Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para información general sobre los precios y el soporte técnico de Azure. Se tratan los siguientes temas:

* [Preguntas generales acerca de la mensajería de Azure Service Bus](#general-questions-about-azure-service-bus-messaging)
* [Procedimientos recomendados de Service Bus](#service-bus-best-practices)
* [Precios de Service Bus](#service-bus-pricing)
* [Cuotas de Service Bus](#service-bus-quotas)
* [Administración de suscripción y espacio de nombres](#subscription-and-namespace-management)
* [Solución de problemas](#service-bus-troubleshooting)

## <a name="general-questions-about-azure-service-bus-messaging"></a>Preguntas generales acerca de la mensajería de Azure Service Bus
### <a name="what-is-azure-service-bus-messaging"></a>¿Qué es la mensajería de Azure Service Bus?
[Mensajería de Azure Service Bus](service-bus-messaging-overview.md) es una plataforma de nube de mensajería asíncrona que le permite enviar datos entre sistemas desacoplados. Microsoft ofrece esta función como un servicio, lo que significa que el usuario no tendrá que hospedar su propio hardware para poder utilizarlo.

### <a name="what-is-a-service-bus-namespace"></a>¿Qué es un espacio de nombres del Bus de servicio?
Un [espacio de nombres](service-bus-create-namespace-portal.md) proporciona un contenedor con un ámbito para el desvío de recursos de Service Bus en la aplicación. Es necesario crear uno para usar el Bus de servicio y es uno de los primeros pasos a realizar para empezar.

### <a name="what-is-an-azure-service-bus-queue"></a>¿Qué es una cola del Bus de servicio de Azure?
Una [cola de Service Bus](service-bus-queues-topics-subscriptions.md) es una entidad en la que se almacenan los mensajes. Las colas son especialmente útiles cuando tiene varias aplicaciones o varias partes de una aplicación distribuida que necesitan comunicarse entre sí. La cola es similar a un centro de distribución en el sentido de que se recibe múltiples productos (mensajes) que luego se envían desde esa ubicación.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>¿Qué son los temas y las suscripciones de Azure Service Bus?
Un tema se puede visualizar como una cola y cuando utiliza varias suscripciones, se convierte en un modelo de mensajería más enriquecido; básicamente en una herramienta de comunicación de uno a varios. Este modelo de publicación o suscripción (o *pub/sub*) permite que cuando una aplicación envía un mensaje a un tema con varias suscripciones, el mensaje lo reciban varias aplicaciones.

### <a name="what-is-a-partitioned-entity"></a>¿Qué es una entidad con particiones?
Un único agente de mensajes controla una cola o tema convencional, que se almacena en un almacén de mensajería. Las [colas o temas con particiones](service-bus-partitioning.md) las administran varios agentes de mensajes y se almacenan en varios almacenes de mensajería. Esto significa que el rendimiento general de una cola o tema particionado ya no está limitado por el rendimiento de un solo agente o almacén de mensajería. Además, una interrupción temporal de un almacén de mensajería no hace que una cola o tema con particiones deje de estar disponible.

Tenga en cuenta que la ordenación no está garantizada al utilizar particiones de entidades. En caso de que una partición no esté disponible, puede enviar y recibir mensajes de las otras particiones.

## <a name="service-bus-best-practices"></a>Procedimientos recomendados del Bus de servicio
### <a name="what-are-some-azure-service-bus-best-practices"></a>¿Cuáles son algunos de los procedimientos recomendados del Bus de servicio de Azure?
* [Procedimientos recomendados para mejorar el rendimiento mediante la mensajería asincrónica de Service Bus][Best practices for performance improvements using Service Bus brokered messaging]: en este artículo se describe cómo optimizar el rendimiento al intercambiar mensajes asincrónicos.

### <a name="what-should-i-know-before-creating-messaging-entities"></a>¿Qué debo saber antes de crear entidades de mensajería?
Las siguientes propiedades de una cola y un tema son inmutables. Tenga esto en cuenta al aprovisionar las entidades ya que esto no se puede modificar, sin crear una nueva entidad de reemplazo.

* Tamaño
* Creación de particiones
* Sesiones
* Detección de duplicados
* Entidad exprés

## <a name="service-bus-pricing"></a>Precios del Bus de servicio
En esta sección responde a algunas preguntas frecuentes acerca de la estructura de precios del Bus de servicio. También puede visitar [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para obtener información general sobre los precios de Microsoft Azure. Para obtener más información sobre los precios del Bus de servicio, consulte [Precios del Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>¿Cómo se cobra el Bus de servicio?
Para más información sobre los precios del Bus de servicio, consulte los [detalles de precios de Service Bus][Pricing overview]. Además de los precios indicados, se le cobrará por las transferencias de datos asociadas para salidas del centro de datos en el que se aprovisiona la aplicación.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>¿Qué uso del Bus de servicio está sujeto a la transferencia de datos? ¿Cuál no lo está?
Cualquier transferencia de datos dentro de una determinada región de Azure se proporciona sin cargo alguno. Cualquier transferencia fuera de una región de datos está sujeta a cargos de salida a la tarifa de&0;,15 USD por GB desde las regiones de Norteamérica y Europa y&0;,20 USD por GB desde la región de Asia Pacífico. Cualquier transferencia de datos de entrada se proporciona sin cargo alguno.

### <a name="does-service-bus-charge-for-storage"></a>¿El Bus de servicio cobra por almacenamiento?
No, el Bus de servicio no cobra por almacenamiento. Sin embargo, hay una cuota que limita la cantidad máxima de datos que pueden persistir por cola/tema. Consulte la siguiente pregunta.

## <a name="service-bus-quotas"></a>Cuotas del Bus de servicio
Para obtener una lista de las cuotas y los límites de Service Bus, consulte [Información general sobre cuotas][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>¿El Bus de servicio tiene alguna cuota de uso?
De forma predeterminada, para cualquier servicio en la nube, Microsoft establece una cuota de uso mensual agregada que se calcula en todas las suscripciones del cliente. Dado que entendemos que puede necesitar más de estos límites, póngase en contacto con el servicio de atención al cliente en cualquier momento para que podamos conocer sus necesidades y ajustar estos límites adecuadamente. Para el Bus de servicio, las cuotas de uso agregado son las siguientes:

* 5 millardos de mensajes

Aunque nos reservamos el derecho de deshabilitar una cuenta de cliente que supere sus cuotas de uso en un mes determinado, se proporcionará una notificación por correo electrónico y se realizarán varios intentos para ponerse en contacto con un cliente antes de llevar a cabo cualquier acción. Los clientes que superen estas cuotas todavía será responsables de los cargos que superen las cuotas.

Al igual que con otros servicios de Azure, el Bus de servicio aplica un conjunto de cuotas específicas para garantizar que hay un uso justo de los recursos. Las siguientes son las cuotas de uso que aplica el servicio:

#### <a name="queuetopic-size"></a>Tamaño de cola o tema
Especifique el tamaño máximo de la cola o del tema al crearlos. Esta cuota puede tener un valor de 1, 2, 3, 4 o 5 GB. Si se alcanza el tamaño máximo de la cola establecido, se rechazarán los mensajes entrantes adicionales y el código de llamada recibirá una excepción.

#### <a name="naming-restrictions"></a>Restricciones de nomenclatura
Un espacio de nombres de Service Bus solo puede tener entre 6 y 50 caracteres. El límite de recuento de caracteres para cada cola, tema o suscripción está entre 1 y 50 caracteres.

#### <a name="number-of-concurrent-connections"></a>Número de conexiones simultáneas
Cola, tema o suscripción: el número de conexiones TCP simultáneas de una cola, tema o suscripción está limitado a 100. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de conexiones adicionales y el código de llamada recibirá una excepción. Para cada generador de mensajes, el Bus de servicio mantiene una conexión TCP si cualquiera de los clientes creados por esa factoría de mensajes tiene una operación activa pendiente o ha completado una operación hace menos de 60 segundos. Las operaciones REST no se cuentan en las conexiones de TCP simultáneas.

#### <a name="number-of-topicsqueues-per-service-namespace"></a>Número de temas o colas por espacio de nombres del servicio
El número máximo de temas o colas (entidades con copia en almacenamiento duraderas) en un espacio de nombres del servicio es 10 000. Si se alcanza esta cuota, se rechazarán las posteriores solicitudes de creación colas o temas nuevos en el espacio de nombres del servicio. En este caso, el Portal de Azure clásico mostrará un mensaje de error o el código del cliente que llama recibirá una excepción, en función de si el intento de creación se realiza a través del portal o en el código del cliente.

### <a name="message-size-quotas"></a>Cuotas de tamaño de los mensajes
#### <a name="queuetopicsubscription"></a>Cola, tema o suscripción
**Tamaño de los mensajes** : cada mensaje se limita a un tamaño total de 256 KB, incluidos los encabezados del mensaje.

**Tamaño de encabezado de mensaje** : cada encabezado de mensaje se limita a 64 KB.

Se rechazarán los mensajes que superen estas cuotas de tamaño y el código de llamada recibirá una excepción.

**Número de suscripciones por tema** : el número máximo de suscripciones por tema se limita a 2000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de suscripciones adicionales al tema. En este caso, el Portal de Azure clásico mostrará un mensaje de error o el código del cliente que llama recibirá una excepción, en función de si el intento de creación se realiza a través del portal o en el código del cliente.

**Número de filtros SQL por tema** : el número máximo de filtros SQL por tema está limitado a 2000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de filtros adicionales en el tema y el código de llamada recibirá una excepción.

**Número de filtros de correlación por tema** : el número máximo de filtros de correlación por tema se limita a 100 000. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de creación de filtros adicionales en el tema y el código de llamada recibirá una excepción.

## <a name="subscription-and-namespace-management"></a>Administración de suscripción y espacio de nombres
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>¿Cómo se migra un espacio de nombres a otra suscripción de Azure?
Puede usar comandos de PowerShell (los encontrará [aquí][here]) para mover un espacio de nombres de una suscripción de Azure a otra. Para ejecutar la operación, el espacio de nombres tiene que estar ya activo. Además, el usuario que ejecuta los comandos tiene que ser administrador en las suscripciones de origen y de destino.

## <a name="service-bus-troubleshooting"></a>Solución de problemas del Bus de servicio de Azure
[Información general sobre excepciones][Exceptions overview]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions"></a>¿Cuáles son algunas de las excepciones generadas por las API de mensajería del Bus de servicio de Azure y sus acciones sugeridas?
Las excepciones que pueden generar las API de mensajería se dividen en las siguientes categorías:

* Error de codificación de usuario
* Error de instalación/configuración
* Excepciones transitorias
* Otras excepciones

El artículo [Excepciones de mensajería de Service Bus][Exceptions overview] describe algunas excepciones con las acciones que se sugieren.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>¿Qué es una firma de acceso compartido y qué lenguajes admiten la generación de una firma?
Las firmas de acceso compartido son un mecanismo de autenticación basado en URI y valores hash seguros SHA-256. Para más información sobre cómo generar sus propias firmas en Node, PHP, Java y C, consulte el artículo \#[Las firmas de acceso compartido][Shared Access Signatures].

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la mensajería de Bus de servicio, consulte los siguientes temas:

* [Introducción a la mensajería Premium del Bus de servicio de Azure (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducción a la mensajería Premium del Bus de servicio de Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
* [Información general sobre la arquitectura de Bus de servicio de Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus brokered messaging]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[here]: service-bus-powershell-how-to-provision.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas-overview.md



<!--HONumber=Jan17_HO4-->


