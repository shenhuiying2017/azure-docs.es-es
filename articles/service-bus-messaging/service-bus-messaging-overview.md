---
title: "Información general sobre la mensajería de Azure Service Bus | Microsoft Docs"
description: "Descripción de la mensajería de Service Bus y Azure Relay"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: e299ccfe587d37757cd67cb4367f019b21a09b4a
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Mensajería de Service Bus: entrega flexible de datos en la nube

Microsoft Azure Service Bus es un servicio de entrega de información confiable. El propósito de este servicio es facilitar la comunicación. Cuando dos o más partes quieren intercambiar información, necesitan un facilitador de la comunicación. Service Bus es un mecanismo de comunicación asincrónica o de terceros. Esto es similar a un servicio postal en el mundo físico. Los servicios postales facilitan el envío distintos tipos de cartas y paquetes, con una variedad de garantías de entrega, a cualquier lugar del mundo.

Service Bus es similar al servicio postal de entrega de cartas, ya que permite la entrega flexible de información entre el remitente y el destinatario. El servicio de mensajería garantiza que la información se entrega, incluso si las dos partes no están nunca conectados al mismo tiempo o si no están disponibles en el mismo momento exacto. De esta manera, la mensajería es similar al envío de una carta, mientras que la comunicación asíncrona es similar a la realización de una llamada telefónica (o como era la realización de llamadas, antes de la creación de servicios de identificación de llamada o llamada en espera, que son mucho más similares a la mensajería asíncrona).

El remitente del mensaje también puede requerir varias características de entrega, incluidas transacciones, detección de duplicados, expiración basada en el tiempo y procesamiento por lotes. Estos patrones también tienen analogías postales: repetición de la entrega, requerimiento de firma, cambio de dirección o devolución de llamada.

Service Bus admite dos patrones de mensajería distintos: *Azure Relay* y *Service Bus Messaging*.

## <a name="azure-relay"></a>Azure Relay

El componente [WCF Relay](../service-bus-relay/relay-what-is-it.md) de Azure Relay es un servicio centralizado (pero de carga muy equilibrada) que admite varios protocolos de transporte y estándares de servicios web diferentes. Incluye SOAP, WS-* e incluso REST. El [servicio de retransmisión](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) ofrece una variedad de opciones de conectividad de retransmisión diferentes y puede ayudar a facilitar la negociación de conexiones directas de punto a punto cuando sea posible. Service Bus está optimizado para los desarrolladores de .NET que usan Windows Communication Foundation (WCF), tanto en términos de rendimiento como de facilidad de uso, y ofrece acceso completo a su servicio de retransmisión a través de interfaces SOAP y REST. Esto permite que cualquier entorno de programación SOAP o REST se integre con Service Bus.

El servicio de retransmisión admite mensajería unidireccional tradicional, mensajería de solicitud/respuesta y mensajería de punto a punto. También admite la distribución de eventos en el ámbito de Internet para habilitar escenarios de publicación-suscripción y la comunicación de socket bidireccional para aumentar la eficacia punto a punto. En el patrón de mensajería retransmitida, un servicio local se conecta al servicio de relé mediante un puerto de salida y crea un socket bidireccional para la comunicación enlazada a una dirección de encuentro concreta. Después el cliente puede comunicarse con el servicio local enviando mensajes al servicio de retransmisión destinados a la dirección de rendezvous. El servicio de retransmisión “retransmite” los mensajes al servicio local a través del socket bidireccional existente. El cliente no necesita una conexión directa al servicio local ni es necesario saber dónde reside el servicio, y el servicio local no necesita ningún puerto de entrada abierto en el firewall.

La conexión entre el servicio local y el servicio de retransmisión se inicia mediante un conjunto de enlaces de “retransmisión” WCF. En segundo plano, los enlaces de retransmisión se asignan a elementos de enlace de transporte diseñados para crear componentes de canal WCF que se integran con Service Bus en la nube.

WCF Relay ofrece muchas ventajas, pero requiere que tanto el servidor como el cliente estén en línea al mismo tiempo para enviar y recibir mensajes. Esto no es óptimo para la comunicación de estilo HTTP, en la que puede que las solicitudes no sean normalmente de larga duración, ni para los clientes que solo se conectan ocasionalmente, como exploradores, aplicaciones móviles, etc. La mensajería asíncrona admite la comunicación desacoplada y tiene sus propias ventajas; los clientes y servidores se pueden conectar cuando sea necesario y realizar sus operaciones de forma asincrónica.

## <a name="brokered-messaging"></a>Mensajería asíncrona

Al contrario que el esquema de retransmisión, la mensajería de Service Bus con [colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md) se puede considerar asincrónica o "temporalmente desacoplada". Los productores (remitentes) y consumidores (receptores) no tienen que estar en línea al mismo tiempo. La infraestructura de mensajería almacena de forma fiable los mensajes en un "agente" (como, por ejemplo, una cola) hasta que la parte consumidora esté preparada para recibirlos. De esta forma, los componentes de la aplicación distribuida se pueden desconectar, ya sea voluntariamente, por ejemplo, para mantenimiento, o debido a un bloqueo del componente, sin que afecte a todo el sistema. Además, es posible que la aplicación receptora solo tenga que estar en línea durante determinadas horas del día, por ejemplo, como un sistema de administración de inventario que solo es necesario ejecutarse al final del día laborable.

Los componentes principales de la infraestructura de mensajería de Service Bus son las colas, los temas y las suscripciones. La principal diferencia es que los temas admiten funcionalidades de publicación o suscripción que se pueden usar para las características de enrutamiento basado en contenido y entrega lógica, incluido el envío a varios destinatarios. Estos componentes permiten nuevos escenarios de mensajería asincrónicos, como desacoplamiento temporal, publicación/suscripción y equilibrio de carga. Para más información sobre estas entidades de mensajería, vea [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md).

Al igual que con la infraestructura de WCF Relay, se ofrece funcionalidad de mensajería asincrónica para los programadores de WCF y de .NET Framework, y también a través de REST.

## <a name="next-steps"></a>pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas.

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

