---
title: "Guía del protocolo AMQP 1.0 en Azure Service Bus y Event Hubs | Microsoft Docs"
description: "Guía del protocolo para expresiones y la descripción de AMQP 1.0 en Azure Service Bus y Event Hubs"
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: 
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: clemensv;hillaryc;sethm
ms.openlocfilehash: 4e1fa9db3b4801103069163c55a9b342a27d00ac
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Guía del protocolo AMQP 1.0 Azure Service Bus y Event Hubs

Advanced Message Queueing Protocol 1.0 es un protocolo de tramas y transferencia estandarizado para transferir mensajes de forma asincrónica, segura y confiable entre dos partes. Es el principal protocolo de Azure Service Bus Messaging y Azure Event Hubs. Ambos servicios también admiten HTTPS. El protocolo SBMP propietario, que también se admite, está desapareciendo en favor de AMQP.

AMQP 1.0 es el resultado de una amplia colaboración del sector que reunió a proveedores de middleware, como Microsoft y Red Hat, con muchos usuarios de middleware de mensajería, como JP Morgan Chase, que representa al sector de los servicios financieros. El foro de normalización técnica para las especificaciones del protocolo AMQP y la extensión es OASIS, y ha logrado la aprobación formal como estándar internacional como ISO/IEC 19494.

## <a name="goals"></a>Objetivos

Este artículo resume brevemente los conceptos básicos de la especificación de mensajería AMQP 1.0, junto con un pequeño conjunto de borradores de especificaciones de la extensión que actualmente se está finalizando en el comité técnico de OASIS para AMQP; también explica cómo Azure Service Bus implementa y crea basándose en estas especificaciones.

El objetivo es que cualquier desarrollador que usa una pila de cliente de AMQP 1.0 existente en cualquier plataforma pueda interactuar con Azure Service Bus mediante AMQP 1.0.

Las pilas de propósito general de AMQP 1.0 comunes, como Apache Proton o AMQP.NET Lite, ya implementan todos los protocolos importantes de AMQP 1.0. Los gestos fundacionales a veces se encapsulan con una API de mayor nivel; Apache Proton incluso ofrece dos: la API de Messenger imperativa y la API de Reactor reactiva.

En la siguiente sección, se supone que la administración de conexiones, sesiones y vínculos de AMQP, y la administración de las transferencias de tramas y el control de flujo se tratan mediante la pila correspondiente (por ejemplo, Apache Proton-C) y no requieren demasiada atención específica de los desarrolladores de aplicaciones, o ninguna atención. Suponemos de forma abstracta la existencia de unas primitivas de API, como la capacidad de conectarse y de crear algún tipo de objetos de abstracción *remitente* y *receptor*, que luego tienen alguna forma de operaciones `send()` y `receive()`, respectivamente.

Cuando se habla de las funcionalidades avanzadas de Azure Service Bus, como la consulta de mensajes o la administración de sesiones, estas características se explican en relación a AMQP, pero también como una pseudoimplementación superpuesta sobre esta abstracción de API supuesta.

## <a name="what-is-amqp"></a>¿Qué es AMQP?

AMQP es un protocolo de tramas y transferencia. Las tramas significa que proporciona una estructura para los flujos de datos binarios que fluyan en ambas direcciones de una conexión de red. La estructura proporciona la delineación de bloques de datos distintivos *tramas* que se intercambiarán entre las partes conectadas. Las funcionalidades de transferencia se aseguran de que ambas partes de la comunicación pueden establecer un conocimiento compartido acerca de cuándo se transferirán las tramas y cuándo se considerarán completadas las transferencias.

A diferencia de los anteriores borradores expirados producidos por el grupo de trabajo de AMQP que todavía usan algunos agentes de mensajes, el protocolo AMQP 1.0 estandarizado y final del grupo de trabajo no prescribe la presencia de un agente de mensajes o de ninguna topología concreta para las entidades dentro de un agente de mensajes.

Puede usar el protocolo para la comunicación punto a punto simétrica, para la interacción con los agentes de mensajes que admiten las colas y las entidades de publicación/suscripción, como hace Azure Service Bus. También se puede utilizar para la interacción con la infraestructura de mensajería cuando los patrones de interacción son distintos de las colas regulares, como sucede con Azure Event Hubs. Un Centro de eventos actúa como una cola cuando se le envían eventos, pero actúa más como un servicio de almacenamiento en serie cuando se leen los eventos desde él; en cierto modo, se parece a una unidad de cinta. El cliente elige un desplazamiento en el flujo de datos disponible y, después, atiende todos los eventos desde ese desplazamiento hasta el más reciente disponible.

El protocolo AMQP 1.0 está diseñado para ser extensible, lo que permite que las especificaciones mejoren sus funcionalidades. Las tres especificaciones de la extensión que tratamos en este documento lo muestran. Para la comunicación a través de la infraestructura existente de HTTPS/WebSockets donde la configuración de los puertos TCP de AMQP nativos puede ser difícil, una especificación de enlace define cómo superponer AMQP a WebSockets. Para interactuar con la infraestructura de mensajería en forma de solicitud/respuesta para fines de administración o para proporcionar una funcionalidad avanzada, la especificación de Administración de AMQP define las primitivas de interacción básica necesarias. Para la integración del modelo de autorización federada, la especificación de seguridad basada en notificaciones de AMQP define cómo asociar y renovar los tokens de autorización asociados a los vínculos.

## <a name="basic-amqp-scenarios"></a>Escenarios básicos de AMQP

En esta sección se explica el uso básico de AMQP 1.0 con Azure Service Bus, lo que incluye la creación de conexiones, sesiones y vínculos, así como la transferencia de mensajes tanto a las entidades de Services Bus (colas, temas y suscripciones) como desde ellas.

La fuente con mayor autoridad para aprender cómo funciona AMQP es la especificación AMQP 1.0, pero se escribió para guiar de forma precisa la implementación, no para enseñar el protocolo. Esta sección se centra en la presentación de tanta terminología como sea necesario para describir el modo en que Service Bus usa AMQP 1.0. Para una introducción más completa a AMQP, así como una explicación más amplia de AMQP 1.0, puede consultar [este curso en vídeo][this video course].

### <a name="connections-and-sessions"></a>Conexiones y sesiones

AMQP llama a los programas de comunicación *contenedores*, que contienen *nodos*, unas entidades que se comunican dentro de estos contenedores. Una cola puede ser uno de esos nodos. AMQP permite la multiplexación, por lo que se puede usar una sola conexión para muchas rutas de comunicación entre los nodos; por ejemplo, un cliente de aplicación puede recibir al mismo tiempo desde una cola y enviar a otra cola a través de la misma conexión de red.

![][1]

Por lo tanto, la conexión de red está anclada en el contenedor. El contenedor la inicia en el rol de cliente, realizando una conexión de socket TCP saliente a un contenedor en el rol de receptor, que escucha y acepta las conexiones TCP entrantes. El protocolo de enlace de conexión incluye negociar la versión del protocolo, declarar o negociar el uso de la seguridad de nivel de transporte (TLS/SSL) y un protocolo de enlace de autenticación y autorización en el ámbito de la conexión que se basa en SASL.

Azure Service Bus requiere el uso de TLS en todo momento. Admite conexiones a través del puerto TCP 5671, donde la conexión TCP primero se superpone con TLS antes de entrar en el protocolo de enlace del protocolo AMQP y también admite conexiones a través del puerto TCP 5672, donde el servidor ofrece inmediatamente una actualización obligatoria de la conexión a TLS con el modelo prescrito por AMQP. El enlace de WebSockets de AMQP crea un túnel a través del puerto TCP 443 que es equivalente a las conexiones 5671 de AMQP.

Después de configurar la conexión y TLS, Service Bus ofrece dos opciones de mecanismo SASL:

* Normalmente, SASL PLAIN se utiliza para pasar las credenciales de usuario y la contraseña a un servidor. Service Bus no tiene cuentas, sino [reglas de seguridad de acceso compartido](service-bus-sas.md) con nombre, que confieren derechos y están asociadas a una clave. El nombre de una regla se usa como nombre de usuario y la clave (como texto codificado con base64) se utiliza como contraseña. Los derechos asociados a la regla elegida rigen las operaciones permitidas en la conexión.
* SASL ANONYMOUS se utiliza para omitir la autorización de SASL cuando el cliente desea usar el modelo de seguridad basada en notificaciones (CBS), que se describe más adelante. Con esta opción, se puede establecer una conexión de cliente de forma anónima por un breve período, durante el cual el cliente solo puede interactuar con el punto de conexión CBS y se debe completar el protocolo de enlace CBS.

Una vez establecida la conexión de transporte, cada contenedor declara el tamaño máximo de trama que desea controlar y después de qué tiempo de espera inactivo se desconectará unilateralmente si no hay ninguna actividad en la conexión.

También declaran cuántos canales simultáneos se admiten. Un canal es una ruta de transferencia unidireccional, saliente y virtual sobre la conexión. Una sesión toma un canal de cada uno de los contenedores interconectados para formar una ruta de comunicación bidireccional.

Las sesiones tienen un modelo de control de flujo basado en ventanas; cuando se crea una sesión, cada parte declara el número de tramas que está dispuesto a aceptar en su ventana de recepción. A medida que las partes intercambian tramas, las tramas transferidas llenan esa ventana y se detienen cuando la ventana está llena, y hasta que la ventana se restablece o se expande mediante el *performativo del flujo* (*performativo* es el término de AMQP para los gestos en el nivel de protocolo intercambiados entre las dos partes).

Este modelo basado en ventanas es parecido al concepto de control de flujo basado en ventanas de TCP, pero en el nivel de sesión dentro del socket. El concepto del protocolo de permitir que haya varias sesiones simultáneas existe para que el tráfico de alta prioridad pueda adelantar al tráfico normal limitado, como en un carril rápido de una autopista.

En la actualidad, Azure Service Bus utiliza exactamente una sesión para cada conexión. El tamaño de trama máximo de Service Bus es 262 144 bytes (256 Kbytes) para Service Bus estándar y Event Hubs. Para Service Bus Premium es 1 048 576 (1 MB). Service Bus no impone ninguna ventana específica de limitación en el nivel de sesión, pero restablece la ventana periódicamente como parte del control de flujo en el nivel de vínculo (consulte [la siguiente sección](#links)).

Las conexiones, los canales y las sesiones son efímeros. Si la conexión subyacente se contrae, es necesario restablecer las conexiones, el túnel TLS, el contexto de autorización SASL y las sesiones.

### <a name="links"></a>Vínculos

AMQP transfiere los mensajes a través de vínculos. Un vínculo es una ruta de comunicación creada en una sesión que permite transferir mensajes en un sentido; la negociación de estado de la transferencia es a través del vínculo y bidireccional entre las partes conectadas.

![][2]

Cualquier contenedor pueden crear vínculos en cualquier momento en una sesión existente, lo que hace que AMQP sea diferente de muchos otros protocolos, incluidos HTTP y MQTT, donde la iniciación de las transferencias y la ruta de la transferencia es un privilegio exclusivo de la parte que se crea la conexión de socket.

El contenedor que inicia el vínculo pide al contenedor opuesto que acepte un vínculo y elije un el rol de remitente o receptor. Por lo tanto, cualquier contenedor puede iniciar la creación de rutas de comunicación unidireccionales o bidireccionales, con esta última modelada como pares de vínculos.

Se asigna nombre a los vínculos y se asocian a los nodos. Como se indicó al principio, los nodos son las entidades que se comunican dentro de un contenedor.

En Service Bus, un nodo es directamente equivalente a una cola, un tema, una suscripción o una subcola de mensajes fallidos de una cola o suscripción. Por lo tanto, el nombre de nodo utilizado en AMQP es el nombre relativo de la entidad dentro del espacio de nombres de Service Bus. Si una cola se denomina **myqueue**, ese es también su nombre de nodo de AMQP. Una suscripción de un tema sigue la convención de API HTTP, ya que se ordena en una colección de recursos de "suscripciones" y, por consiguiente, una suscripción **sub** o un tema **mytopic** tienen el nombre de nodo de AMQP **mytopic/subscriptions/sub**.

El cliente que se conecta también debe usar un nombre de nodo local para crear los vínculos; Service Bus no es preceptivo acerca de esos nombres de nodo y no los interpreta. Normalmente, las pilas de cliente de AMQP 1.0 utilizan un esquema para asegurarse de que estos nombres de nodo efímero son únicos en el ámbito del cliente.

### <a name="transfers"></a>Transferencias

Una vez establecido un vínculo, los mensajes se pueden transferir a través de ese vínculo. En AMQP, se realiza una transferencia con un gesto de protocolo explícito (el performativo *transfer*) que mueve un mensaje del remitente al receptor a través de un vínculo. Una transferencia está completa cuando que se "determina", lo que significa que ambas partes han establecido una conocimiento compartido del resultado de esa transferencia.

![][3]

En el caso más simple, el remitente puede enviar mensajes "previamente determinados", lo que significa que el cliente no está interesado en el resultado y el receptor no proporciona ningún comentario sobre el resultado de la operación. Este modo es compatible con Service Bus en el nivel del protocolo AMQP, pero no se expone en ninguna de las API de cliente.

Lo normal es que los mensajes se envíen sin determinar y el receptor indique entonces la aceptación o el rechazo mediante el performativo *disposition*. Se produce un rechazo cuando el receptor no puede aceptar el mensaje por alguna razón y el mensaje de rechazo contiene información sobre el motivo, que es una estructura de error definida por AMQP. Si se rechazan los mensajes debido a errores internos dentro de Service Bus, el servicio devuelve información adicional dentro de esa estructura que puede utilizarse para proporcionar sugerencias de diagnóstico al personal de soporte si está registrando solicitudes de soporte técnico. Aprenderá más detalles acerca de los errores más adelante.

Una forma especial de rechazo es el estado *publicado*, que indica que el receptor no tiene ninguna objeción técnica a la transferencia, pero tampoco tiene ningún interés en determinarla. Ese es el caso, por ejemplo, cuando un mensaje se entrega a un cliente de Service Bus y el cliente elige "abandonar" el mensaje porque no puede realizar el trabajo resultante de procesar el mensaje, mientras que la entrega del mensaje en sí no tiene ningún error. Una variación de este estado es el estado *modificado*, que permite realizar cambios en el mensaje cuando se envía. En la actualidad, Service Bus no utiliza ese estado.

La especificación AMQP 1.0 define otro estado de disposición, denominado *recibido*, que ayuda específicamente a controlar la recuperación de vínculos. La recuperación de vínculos permite reconstituir el estado de un vínculo y las entregas pendientes sobre una nueva conexión y sesión, cuando se pierden la conexión y sesión anteriores.

Service Bus no admite la recuperación de vínculos; si el cliente pierde la conexión a Service Bus con una transferencia de mensaje sin determinar pendiente, dicha transferencia se pierde y el cliente debe volver a conectarse, restablecer el vínculo e intentar de nuevo la transferencia.

Como tal, Service Bus y Event Hubs admiten transferencias "al menos una vez", donde el remitente puede estar seguro de que el mensaje se ha almacenado y aceptado, pero no admite transferencias "exactamente una vez" en el nivel de AMQP, donde el sistema intentará recuperar el vínculo y continuar negociando el estado de entrega para evitar la duplicación de la transferencia del mensaje.

Para compensar posibles envíos duplicados, Service Bus admite la detección de duplicados como una característica opcional en colas y temas. La detección de duplicados registra los identificadores de todos los mensajes entrantes durante un período definido por el usuario y después elimina silenciosamente todos los mensajes enviados con los mismos identificadores de mensaje durante esa misma ventana.

### <a name="flow-control"></a>Control de flujo

Además del modelo de control de flujo en el nivel de sesión que se ha tratado anteriormente, cada vínculo tiene su propio modelo de control de flujo. El control de flujo en el nivel de sesión protege el contenedor para que no tenga que controlar muchas tramas de una vez; el control de flujo de nivel de vínculo pone la aplicación a cargo de cuántos mensajes desea controlar desde un vínculo y cuándo.

![][4]

En un vínculo, las transferencias solo se pueden producir si el remitente tiene suficiente *crédito del vínculo*. El crédito del vínculo es un contador establecido por el receptor con el performativo *flow*, que tiene un ámbito en un vínculo. Cuando el remitente tiene crédito del vínculo asignado, intenta utilizar ese crédito con la entrega de mensajes. Cada entrega de mensajes reduce en uno el crédito del vínculo restante. Cuando se agota el crédito del vínculo, las entregas se detienen.

Cuando Service Bus está en el rol de receptor, proporciona instantáneamente al remitente el crédito del vínculo suficiente para que los mensajes puedan enviarse de inmediato. Puesto que se utiliza el crédito del vínculo, Service Bus envía ocasionalmente un performativo *flow* al remitente para actualizar el saldo del crédito del vínculo.

En el rol de remitente, Service Bus envía mensajes para utilizar todo el crédito del vínculo pendiente.

Una llamada "recibir" en el nivel de API se traduce en un performativo *flow* enviado a Service Bus por el cliente; Service Bus usa ese crédito tomando el primer mensaje desbloqueado y disponible de la cola, bloqueándolo y transfiriéndolo. Si no hay ningún mensaje disponible para la entrega, todo el crédito pendiente por cualquier vínculo establecido con esa entidad concreta permanece registrado en orden de llegada, y los mensajes se bloquean y transfieren a medida que estén disponibles para usar el crédito pendiente.

Se libera el bloqueo de un mensaje cuando la transferencia se determina en uno de los estados terminales *aceptado*, *rechazado* o *publicado*. El mensaje se quita de Service Bus cuando el estado terminal *aceptado*. Permanece en Service Bus y se entrega al siguiente receptor cuando la transferencia alcance cualquiera de los otros estados. Service Bus pasa automáticamente el mensaje a la cola de mensajes fallidos de la entidad al alcanzar el número máximo de entregas permitido para la entidad debido a rechazos o lanzamientos repetidos.

Aunque las API de Service Bus no exponen directamente dicha opción en la actualidad, un cliente del protocolo AMQP de nivel inferior puede utilizar el modelo de crédito del vínculo para convertir la interacción de "estilo de extracción", que emite una unidad de crédito para cada solicitud de recepción, en un modelo de "estilo de inserción" al emitir un gran número de créditos del vínculo y, a continuación, recibir los mensajes cuando estén disponibles sin intervención adicional. Se admite la inserción mediante la configuración de las propiedades [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) o [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount). Si son distintas de cero, el cliente de AMQP las usa como crédito del vínculo.

En este contexto, es importante comprender que el reloj de la expiración del bloqueo en el mensaje dentro de la entidad se inicia cuando el mensaje se toma de la entidad, no cuando se coloca en la transferencia. Cada vez que el cliente indica que está preparado para recibir mensajes mediante la emisión de crédito del vínculo, se espera que extraiga activamente los mensajes a través de la red y que esté preparado para controlarlos. De lo contrario, el bloqueo del mensaje puede haber expirado incluso antes de que el mensaje se entregue. El uso del control de flujo del crédito del vínculo debe reflejar directamente la disponibilidad inmediata para tratar con mensajes disponibles enviados al receptor.

En resumen, las secciones siguientes proporcionan una introducción esquemática del flujo de performativos durante diferentes interacciones de API. Cada sección describe una operación lógica diferente. Algunas de esas interacciones pueden ser "perezosas", lo que significa que solo pueden realizarse cuando se solicitan. La creación de un remitente del mensaje puede no provocar una interacción de la red hasta que se envía o solicita el primer mensaje.

Las flechas de la tabla siguiente muestran la dirección de flujo de performativos.

#### <a name="create-message-receiver"></a>Creación del receptor del mensaje

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link id}<br/>) |El cliente se adjunta a la entidad como receptor |
| Service Bus responde asociando su extremo del vínculo |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>target={client link id}<br/>) |

#### <a name="create-message-sender"></a>Creación del remitente del mensaje

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link id},<br/>target={entity name}<br/>) |Ninguna acción |
| Ninguna acción |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link id},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Creación del remitente del mensaje (error)

| Cliente | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link id},<br/>target={entity name}<br/>) |Ninguna acción |
| Ninguna acción |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Cierre del remitente/receptor del mensaje

| Cliente | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |Ninguna acción |
| Ninguna acción |<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Envío (correcto)

| Cliente | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Ninguna acción |
| Ninguna acción |<-- disposition(<br/>role=receiver,<br/>first={delivery id},<br/>last={delivery id},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Envío (error)

| Cliente | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Ninguna acción |
| Ninguna acción |<-- disposition(<br/>role=receiver,<br/>first={delivery id},<br/>last={delivery id},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Recepción

| Cliente | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Ninguna acción |
| Ninguna acción |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery id},<br/>last={delivery id},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Ninguna acción |

#### <a name="multi-message-receive"></a>Recepción de múltiples mensajes

| Cliente | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Ninguna acción |
| Ninguna acción |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Ninguna acción |< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Ninguna acción |< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery id},<br/>last={delivery id+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Ninguna acción |

### <a name="messages"></a>error de Hadoop

En las siguientes secciones se explican las propiedades de las secciones de mensajes de AMQP estándar utilizadas por Service Bus y cómo se asignan al conjunto de API de Service Bus.

#### <a name="header"></a>encabezado

| Nombre del campo | Uso | Nombre de la API |
| --- | --- | --- |
| duradero |- |- |
| prioridad |- |- |
| ttl |Período de vida para este mensaje |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>propiedades

| Nombre del campo | Uso | Nombre de la API |
| --- | --- | --- |
| message-id |Identificador de formato libre definido por la aplicación para este mensaje. Se usa para la detección de duplicados. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| user-id |Identificador del usuario definido por la aplicación; no interpretado por Service Bus. |No es accesible a través de la API de Service Bus. |
| to |Identificador del destino definido por la aplicación; no interpretado por Service Bus. |[To](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| subject |Identificador del propósito de mensaje definido por la aplicación; no interpretado por Service Bus. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| reply-to |Indicador de la ruta de respuesta definido por la aplicación; no interpretado por Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| correlation-id |Identificador de la correlación definido por la aplicación; no interpretado por Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| content-type |Indicador del tipo de contenido definido por la aplicación para el cuerpo; no interpretado por Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| content-encoding |Indicador de la codificación de contenido definido por la aplicación para el cuerpo; no interpretado por Service Bus. |No es accesible a través de la API de Service Bus. |
| absolute-expiry-time |Declara en qué instante absoluto expira el mensaje. Se ignora en la entrada (se observa el TTL de encabezado), es autoritativo en la salida. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| creation-time |Declara en qué momento creó el mensaje. No usado por Service Bus |No es accesible a través de la API de Service Bus. |
| group-id |Identificador definido por la aplicación para un conjunto de mensajes relacionado. Se utiliza para sesiones de Service Bus. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| group-sequence |Contador que identifica el número de secuencia relativa del mensaje dentro de una sesión. Omitido por Service Bus. |No es accesible a través de la API de Service Bus. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

## <a name="advanced-service-bus-capabilities"></a>Funcionalidades avanzadas de Service Bus

En esta sección se tratan las funcionalidades avanzadas de Azure Service Bus que se basan en los borradores de las extensiones AMQP que actualmente se están desarrollando en el comité técnico de OASIS para AMQP. Service Bus implementa las versiones más recientes de estos borradores y adopta los cambios introducidos a medida que los borradores alcanzan el estado estándar.

> [!NOTE]
> Se admiten las operaciones avanzadas de mensajería de Service Bus mediante un modelo de solicitud y respuesta. Los detalles de estas operaciones se describen en el artículo [El protocolo AMQP 1.0 de Microsoft Azure Service Bus: operaciones de respuesta/solicitud](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Administración de AMQP

La especificación de la administración de AMQP es la primera de las extensiones de borrador que se analizan en este artículo. Esta especificación define un conjunto de protocolos superpuesto al protocolo AMQP que permite las interacciones de administración con la infraestructura de mensajería a través de AMQP. La especificación define operaciones genéricas como *crear*, *leer*, *actualizar* y *eliminar* para administrar entidades dentro de una infraestructura de mensajería y un conjunto de operaciones de consulta.

Todos los gestos requieren una interacción de solicitud/respuesta entre el cliente y la infraestructura de mensajería y, por tanto, la especificación define cómo modelar ese patrón de interacción sobre AMQP: el cliente se conecta a la infraestructura de mensajería, inicia una sesión y crea un par de vínculos. En un vínculo, el cliente actúa como remitente y en el otro actúa como receptor, creando así un par de vínculos que puede actuar como un canal bidireccional.

| Operadores lógicos | Cliente | Service Bus |
| --- | --- | --- |
| Creación de una ruta de acceso de respuesta de solicitud |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |Ninguna acción |
| Creación de una ruta de acceso de respuesta de solicitud |Ninguna acción |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| Creación de una ruta de acceso de respuesta de solicitud |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| Creación de una ruta de acceso de respuesta de solicitud |Ninguna acción |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

Con ese par de vínculos en su lugar, la implementación de la solicitud/respuesta es sencilla: una solicitud es un mensaje enviado a una entidad dentro de la infraestructura de mensajería que comprende este patrón. En ese mensaje de solicitud, el campo *reply-to* de la sección *properties* se establece en el identificador *target* para el vínculo en el que se va a entregar la respuesta. La entidad de control procesa la solicitud y después entrega la respuesta a través del vínculo cuyo identificador *target* coincide con el identificador *reply-to* indicado.

Obviamente, el patrón requiere que el contenedor del cliente y el identificador generado por el cliente para el destino de la respuesta sean únicos en todos los clientes y, por motivos de seguridad, también difíciles de predecir.

Los intercambios de mensajes usados para el protocolo de administración y para todos los demás protocolos que usan el mismo patrón se producen en el nivel de la aplicación; no definen nuevos gestos en el nivel de protocolo AMQP. Eso es intencionado para que las aplicaciones puedan aprovechar inmediatamente estas extensiones con pilas de AMQP 1.0 compatibles.

Service Bus no implementa actualmente ninguna de las características principales de la especificación de administración, pero el patrón de solicitud/respuesta definido por la especificación de administración es fundamental para la característica de seguridad basada en notificaciones y para casi todas las funcionalidades avanzadas que se tratan en las secciones siguientes.

### <a name="claims-based-authorization"></a>Autorización basada en notificaciones

El borrador de la especificación de autorización basada en notificaciones (CBS) de AMQP se basa en el patrón de solicitud/respuesta de la especificación de administración; describe un modelo generalizado para el uso de tokens de seguridad federados con AMQP.

El modelo de seguridad predeterminado de AMQP que se describe en la introducción se basa en SASL y se integra con el protocolo de enlace de conexión de AMQP. El uso de SASL tiene la ventaja de ofrecer un modelo extensible para el que se ha definido un conjunto de mecanismos del que se puede beneficiar cualquier protocolo que emplee formalmente SASL. Entre estos mecanismos están: "PLAIN" para la transferencia de usuarios y contraseñas, “EXTERNAL” para enlazar con la seguridad de nivel de TLS, “ANONYMOUS” para expresar la ausencia de autenticación y autorización explícita y una amplia variedad de mecanismos adicionales que permiten pasar credenciales de autenticación y autorización o tokens.

La integración de SASL de AMQP tiene dos inconvenientes:

* Todas las credenciales y los tokens se limitan al ámbito de la conexión. Una infraestructura de mensajería puede desear proporcionar un control de acceso diferenciado por entidad. Por ejemplo, permitiendo que el portador de un token envíe a la cola A pero no a cola B. Con el contexto de autorización anclado en la conexión, no es posible usar una sola conexión y utilizar tokens de acceso diferentes para la cola A y la cola B.
* Normalmente, los tokens de acceso solo son válidos durante un tiempo limitado. Esta validez obliga al usuario a volver a adquirir periódicamente los tokens y proporciona una oportunidad al emisor del token de rechazar la emisión de un nuevo token si los permisos de acceso del usuario han cambiado. Las conexiones de AMQP pueden durar períodos muy largos. El modelo SASL solo proporciona una oportunidad para establecer un token en tiempo de conexión, lo que significa que la infraestructura de mensajería tiene para desconectar el cliente cuando el token expira o debe aceptar el riesgo de permitir una comunicación continua con un cliente cuyos derechos de acceso pueden haberse revocado mientras tanto.

La especificación de CBS de AMQP, implementada por Service Bus, permite una solución elegante para estos dos problemas: permite que un cliente asocie los tokens de acceso a cada nodo y actualice esos tokens antes de que expiren, sin interrumpir el flujo de mensajes.

CBS define un nodo de administración virtual denominado *$cbs*, proporcionado por la infraestructura de mensajería. El nodo de administración acepta los tokens en nombre de los otros nodos de la infraestructura de mensajería.

El gesto del protocolo es un intercambio de solicitud y respuesta, tal como está definido en la especificación de administración. Esto significa que el cliente establece un par de vínculos con el nodo *$cbs*, pasa una solicitud en el vínculo de salida y, a continuación, espera la respuesta en el vínculo de entrada.

El mensaje de solicitud tiene las siguientes propiedades de la aplicación:

| Clave | Opcional | Tipo de valor | Contenido del valor |
| --- | --- | --- | --- |
| operación |No |string |**put-token** |
| type |No |string |Tipo del token que se coloca. |
| name |No |string |El "público" al que se aplica el token. |
| expiration |Sí |timestamp |La hora de expiración del token. |

La propiedad *name* identifica la entidad a la que se va a asociar el token. En Service Bus es la ruta de acceso a la cola, el tema o la suscripción. La propiedad *type* identifica el tipo de token:

| Tipo de token | Descripción del token | Tipo de cuerpo | Notas |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |Valor de AMQP (cadena) |No disponible todavía. |
| amqp:swt |Simple Web Token (SWT) |Valor de AMQP (cadena) |Solo se admite para los tokens SWT emitidos por AAD y ACS |
| servicebus.windows.net:sastoken |Token SAS de Service Bus |Valor de AMQP (cadena) |- |

Los tokens confieren derechos. Service Bus conoce tres derechos fundamentales: "Enviar" permite enviar, "Escuchar" permite recibir y "Administrar" permite manipular las entidades. Los tokens SWT emitidos por AAD/ACS incluyen explícitamente dichos derechos como notificaciones. Los tokens de SAS de Service Bus hacen referencia a las reglas configuradas en el espacio de nombres o la entidad, y dichas reglas se configuran con derechos. Firmar el token con la clave asociada a esa regla hace que el token exprese los derechos correspondientes. El token asociado a una entidad con *put-token* permite que el cliente conectado interactúe con la entidad de acuerdo con los derechos del token. Un vínculo en el que cliente adopta el rol *sender* requiere el derecho "Enviar"; adoptar el rol *receiver* requiere el derecho "Escuchar".

El mensaje de respuesta tiene los siguientes valores de *application-properties*

| Clave | Opcional | Tipo de valor | Contenido del valor |
| --- | --- | --- | --- |
| status-code |No |int |Código de respuesta HTTP**[RFC2616]**. |
| status-description |Sí |string |Descripción del estado. |

El cliente puede llamar a *put-token* repetidamente y para cualquier entidad de la infraestructura de mensajería. El ámbito de los tokens es el cliente actual y se anclan en la conexión actual, lo que significa que el servidor elimina todos los tokens retenidos cuando la conexión se interrumpe.

La implementación actual de Service Bus solo permite CBS junto con el método SASL “ANONYMOUS”. Siempre debe existir una conexión SSL/TLS antes del protocolo de enlace SASL.

Por tanto, el mecanismo ANONYMOUS debe ser compatible con el cliente de AMQP 1.0 elegido. El acceso anónimo significa que el protocolo de enlace de conexión inicial, incluida la creación de la sesión inicial, tiene lugar sin que Service Bus sepa quién está creando la conexión.

Una vez establecida la conexión y la sesión, las únicas operaciones permitidas son asociar los vínculos al nodo *$cbs* y enviar la solicitud *put-token*. Un token válido debe establecerse correctamente mediante una solicitud *put-token* para algún nodo de entidad en un plazo de 20 segundos después de establecer la conexión; de lo contrario, Service Bus interrumpe la conexión unilateralmente.

El cliente es responsable de realizar un seguimiento de la expiración del token. Cuando expira un token, Service Bus elimina rápidamente todos los vínculos de la conexión a la entidad correspondiente. Para evitarlo, el cliente puede reemplazar en cualquier momento el token del nodo por otro nuevo mediante el nodo de administración *$cbs* virtual con el mismo gesto *put-token* y sin interferir con el tráfico de carga útil que fluye en diferentes enlaces.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre AMQP, visite los siguientes vínculos:

* [Información general sobre AMQP para Service Bus]
* [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus]
* [AMQP de Service Bus para Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md
[Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
