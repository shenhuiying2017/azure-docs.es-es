---
title: "Comparación de los servicios de mensajería de Azure"
description: "Compara Azure Event Grid, Event Hubs y Service Bus. Recomienda qué servicio usar para escenarios diferentes."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/15/2017
ms.author: tomfitz
ms.openlocfilehash: 94771578d94b5b9bc23451049a78506e80c87d26
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Elija entre los servicios de Azure de entrega de mensajes.

Azure ofrece tres servicios que le ayudan en la entrega de mensajes de evento en una solución. Estos servicios son los siguientes:

* [Event Grid](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

Aunque son similares, cada servicio se designa para escenarios determinados. Este artículo describe las diferencias entre estos servicios y le ayuda a entender cuál elegir para su aplicación. En muchos casos, los servicios de mensajería son complementarios y se pueden usar conjuntamente.

## <a name="event-vs-message-services"></a>Servicios de eventos frente a los de mensajería

Hay una diferencia importante entre los servicios que ofrecen un evento y los que entregan un mensaje.

### <a name="event"></a>Evento

Un evento es una notificación ligera de una acción o un cambio de estado. Los datos de evento contienen información acerca de lo que ha ocurrido, pero no tienen los datos que desencadenaron el evento. Por ejemplo, un evento notifica a los suscriptores que se ha creado un archivo. Puede contener información general sobre el archivo, pero no contiene el propio archivo. Por lo general, los eventos desencadenan que los controladores de eventos actúen en tiempo real.

### <a name="message"></a>Message

Un mensaje son datos sin procesar producidos por un servicio que se consumen o almacenan en otro lugar. El mensaje contiene los datos que desencadenó la canalización del mensaje. Este mensaje puede ser cualquier cosa, desde un pedido de comercio electrónico a telemetría de usuario. A diferencia de una notificación de eventos, el publicador de un mensaje puede esperar una respuesta. Por ejemplo, un mensaje contiene los datos sin procesar, pero espera que la siguiente parte del sistema cree un archivo de datos.

## <a name="comparison-of-services"></a>Comparación de servicios

| Servicio | Propósito | Tipo | Cuándo se deben usar |
| ------- | ------- | ---- | ----------- |
| Event Grid | Programación reactiva | Distribución de eventos | Reacción ante los cambios de estado |
| Event Hubs | Canalización de macrodatos | Streaming de eventos | Streaming de datos distribuidos y telemetría |
| Azure Service Bus | Mensajería empresarial de gran valor | Message | Procesamiento de pedidos y transacciones financieras |

### <a name="event-grid"></a>Event Grid

Event Grid es un panel posterior de eventos que habilita la programación orientada a eventos y reactiva. Utiliza un modelo de publicación-suscripción. Los publicadores emiten eventos, pero no tienen expectativas sobre qué eventos se administran. Los suscriptores deciden qué eventos quieren administrar.

Event Grid está totalmente integrado con los servicios de Azure y puede integrarse con servicios de terceros. Simplifica el consumo de eventos y reduce los costos mediante la eliminación de la necesidad de un sondeo constante. Event Grid enruta de forma eficiente y fiable eventos desde recursos de Azure y que no son de Azure. Distribuye los eventos a los puntos de conexión del suscriptor registrado. El mensaje del evento contiene la información que necesita para reaccionar a los cambios en los servicios y aplicaciones. Event Grid no es una canalización de datos y no entrega el objeto real que se ha actualizado.

Tiene las siguientes características:

* dinámicamente escalable
* bajo costo
* sin servidor

### <a name="event-hubs"></a>Event Hubs

Azure Event Hubs es una canalización de macrodatos. Facilita la captura, la retención y la reproducción de los datos de streaming de eventos y telemetría. Los datos pueden proceder de distintos orígenes simultáneos. Event Hubs permite que los datos de telemetría y eventos estén disponibles para una variedad de servicios de análisis e infraestructuras de procesamiento de streaming. Está disponible como transmisiones de datos o lotes de eventos agrupados. Este servicio proporciona una solución única que permite una recuperación de datos rápida para el procesamiento en tiempo real, así como una reproducción repetida de los datos sin procesar almacenados. Puede capturar los datos de streaming en un archivo para su procesamiento y análisis.

Tiene las siguientes características:

* baja latencia
* capacidad de recibir y procesar millones de eventos por segundo

### <a name="service-bus"></a>Azure Service Bus

Service Bus se ha diseñado para aplicaciones empresariales tradicionales. Estas aplicaciones empresariales requieren transacciones, ordenación, detección de duplicados y coherencia inmediata. Service Bus permite que las aplicaciones en la nube nativas proporcionen una administración de transición de estado de confianza para los procesos empresariales. Cuando se administran mensajes de gran valor que no se pueden perder o duplicarse, use Azure Service Bus. Service Bus también facilita la comunicación de alta seguridad en soluciones híbridas en la nube y pueden conectar sistema locales existentes a soluciones en la nube.

Service Bus es un sistema de mensajería asincrónica. Almacena los mensajes en un "agente" (por ejemplo, una cola) hasta que la parte consumidora esté preparada para recibirlos.

Tiene las siguientes características:

* entrega de mensajes asincrónica de confianza (mensajería empresarial como servicio) que requiere el sondeo.
* características de mensajería avanzada, como FIFO, procesamiento por lotes o sesiones, transacciones, colas de mensajes fallidos, control temporal, enrutamiento y filtrado, y detección de duplicados

## <a name="use-the-services-together"></a>Uso conjunto de servicios

En algunos casos, puede utilizar servicios en paralelo para cumplir con distintos roles. Por ejemplo, un sitio de comercio electrónico puede utilizar Service Bus para procesar el pedido, Event Hubs para capturar la telemetría del sitio y Event Grid para responder a eventos, como un artículo enviado.

En otros casos, puede vincular estas soluciones para formar una canalización de datos y eventos. Use Event Grid para responder a los eventos en los demás servicios. Para obtener un ejemplo sobre el uso de Event Grid con Event Hubs para migrar datos a un almacén de datos, vea [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md). En la siguiente imagen se muestra el flujo de trabajo para el streaming de datos.

![Información general sobre streaming de datos](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información acerca de los servicios de mensajería de Azure, vea la entrada de blog [Events, Data Points, and Messages - Choosing the right Azure messaging service for your data](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/) (Eventos, puntos de datos y mensajes: Elección del servicio de mensajería de Azure adecuado para sus datos).
* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
* Para comenzar a usar Event Hubs, vea [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md).
* Para comenzar a usar Service Bus, vea [Creación de un espacio de nombres de Service Bus mediante Azure Portal](../service-bus-messaging/service-bus-create-namespace-portal.md).