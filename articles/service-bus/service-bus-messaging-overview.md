<properties
	pageTitle="Introducción a la mensajería de Service Bus - Azure"
	description="Mensajería de Bus de servicio: entrega flexible de datos en la nube"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sethm"/>


# Mensajería de Bus de servicio: entrega flexible de datos en la nube

La mensajería de Bus de servicio es un servicio confiable de entrega de información. El propósito de este servicio es facilitar la comunicación. Cuando dos o más partes quieren intercambiar información, necesitan un mecanismo de comunicación. La mensajería de Service Bus es un mecanismo de comunicación asíncrona o de terceros. Esto es similar a un servicio postal en el mundo físico. Los servicios postales facilitan el envío distintos tipos de cartas y paquetes, con una variedad de garantías de entrega, a cualquier lugar del mundo.

La mensajería de Azure Service Bus es similar al servicio postal de entrega de cartas, ya que trata de la entrega flexible de información entre el remitente y el destinatario. El servicio de mensajería garantiza que la información se entrega, incluso si las dos partes no están nunca conectados al mismo tiempo o si no están disponibles en el mismo momento exacto. De esta manera, la mensajería es similar al envío de una carta, mientras que la comunicación asíncrona es similar a la realización de una llamada telefónica (o como era la realización de llamadas, antes de la creación de servicios de identificación de llamada o llamada en espera, que son mucho más similares a la mensajería asíncrona).

El remitente del mensaje también puede requerir una variedad de características de entrega, incluidas transacciones, detección de duplicados, expiración basado en el tiempo y el procesamiento por lotes. Estos patrones también tienen analogías postales: repetición de la entrega, requerimiento de firma, cambio de dirección o devolución de llamada.

La mensajería de Service Bus tiene dos características independientes: colas y temas. Ambas entidades de mensajería admiten todos los conceptos presentados anteriormente, entre otros. La principal diferencia es que los temas admiten capacidades de publicación o suscripción que se pueden usar el sofisticado enrutamiento basado en contenido y entrega lógica, incluido el envío a varios destinatarios.

## Pasos siguientes

Para obtener más información sobre la mensajería de Bus de servicio, consulte los siguientes temas:

- [Información general sobre la arquitectura de Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)

- [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)

- [Cómo usar temas de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=July15_HO4-->