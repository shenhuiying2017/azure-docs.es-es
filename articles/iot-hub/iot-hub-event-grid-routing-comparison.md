---
title: "Comparación del enrutamiento de mensajes y Event Grid para IoT Hub | Microsoft Docs"
description: "IoT Hub ofrece su propio servicio de enrutamiento de mensajes, pero también se integra con Event Grid para la publicación de eventos. Compare las dos características."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparación del enrutamiento de mensajes y Event Grid para IoT Hub

Azure IoT Hub proporciona la capacidad de transmitir datos desde los dispositivos conectados e integrar esos datos en las aplicaciones empresariales. IOT Hub ofrece dos métodos para integrar eventos de IoT en otros servicios de Azure o en aplicaciones empresariales. En este artículo se tratan las dos características que proporcionan esta funcionalidad, de modo que pueda elegir qué opción es la más adecuada para su escenario.

* **Enrutamiento de mensajes de IoT Hub**: esta característica de IoT Hub permite a los usuarios [enrutar los mensajes del dispositivo a la nube](iot-hub-devguide-messages-read-custom.md) a los puntos de conexión de servicio como contenedores de Azure Storage, Event Hubs, colas de Service Bus y temas de Service Bus. Las reglas de enrutamiento proporcionan la flexibilidad para realizar rutas basadas en consultas. También habilitan [alertas críticas](iot-hub-devguide-messages-d2c.md) que desencadenan acciones a través de consultas y pueden basarse en los encabezados y el cuerpo del mensaje. 
* **Integración de IoT Hub con Event Grid**: Azure Event Grid es un servicio de enrutamiento de eventos totalmente administrado que utiliza un modelo de publicación-suscripción. Hub IoT y Event Grid trabajan juntos para [integrar eventos de IoT Hub en servicios de Azure y no de Azure](iot-hub-event-grid.md), en tiempo casi real. 

## <a name="similarities-and-differences"></a>Similitudes y diferencias

Aunque tanto el enrutamiento de mensajes como Event Grid habilitan la configuración de alertas, hay algunas diferencias principales entre ambos. Consulte la tabla siguiente para más detalles:

| Característica | Enrutamiento de mensajes de IoT Hub | Integración de IoT Hub con Event Grid |
| ------- | --------------- | ---------- |
| **Mensajes del dispositivo** | Sí, se puede usar el enrutamiento de mensajes para los datos de telemetría. | No, Event Grid solo puede utilizarse para eventos de IoT Hub que no son de telemetría. |
| **Tipo de evento** | Sí, el enrutamiento de mensajes puede notificar cambios gemelos y eventos del ciclo de vida del dispositivo. | Sí, Event Grid puede notificar cuándo los dispositivos se registran en una instancia de IoT Hub y cuándo se eliminan los dispositivos. |
| **Orden** | Sí, se mantiene el orden de los eventos.  | No, no se garantiza el orden de los eventos. | 
| **Tamaño de mensaje máximo** | 256 KB, del dispositivo a la nube | 64 KB |
| **Filtrado** | El filtrado enriquecido mediante el lenguaje SQL es compatible con el filtrado en los encabezados y cuerpo de los mensajes. Para obtener ejemplos, consulte [Lenguaje de consulta de IoT Hub](iot-hub-devguide-query-language.md). | Filtrado basado en el sufijo o prefijo de los identificadores de dispositivo, que funciona bien para servicios jerárquicos como el almacenamiento. |
| **Extremos** | <ul><li>Centro de eventos</li> <li>Blob de almacenamiento</li> <li>Cola de Service Bus</li> <li>Temas de Service Bus</li></ul><br>Los SKU de IoT Hub de pago (S1, S2 y S3) están limitados a 10 puntos de conexión personalizados. Se pueden crear 100 rutas por instancia de IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Centro de eventos</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Servicios de terceros mediante webhooks</li></ul><br>Para obtener la lista más actualizada de los puntos de conexión, consulte [Controladores de eventos de Event Grid](../event-grid/overview.md#event-handlers). |
| **Costee | El enrutamiento de mensajes no se cobra aparte. Solo se cobra una entrada de telemetría en IoT Hub. Por ejemplo, si tiene un mensaje enrutado a tres puntos de conexión diferentes, se le facturará por un único mensaje. | No hay cargos desde IoT Hub. Event Grid ofrece las primeras 100 000 operaciones por mes de forma gratuita, y luego 0,60 USD por millón de operaciones. |

El enrutamiento de mensajes de IoT Hub y Event Grid también tienen similitudes, algunas de las cuales se detallan en la siguiente tabla:

| Característica | Enrutamiento de mensajes de IoT Hub | Integración de IoT Hub con Event Grid |
| ------- | --------------- | ---------- |
| **Confiabilidad** | Alta: entrega cada mensaje al punto de conexión por lo menos una vez en cada ruta. Expira todos los mensajes que no se entregan dentro de una hora. | Alta: entrega cada mensaje al webhook por lo menos una vez en cada suscripción. Expira todos los eventos que no se entregan en 24 horas. | 
| **Escalabilidad** | Alta: está optimizado para admitir millones de dispositivos conectados al mismo tiempo que envían miles de millones de mensajes. | Alta: capaz de enrutar 10 000 000 eventos por segundo por región. |
| **Latency** | Baja: casi en tiempo real. | Baja: casi en tiempo real. |
| **Envío a varios puntos de conexión** | Sí, envíe un único mensaje a varios puntos de conexión. | Sí, envíe un único mensaje a varios puntos de conexión.  | 
| **Seguridad** | IoT Hub ofrece identidad por dispositivo y control de acceso revocable. Para más información, consulte [Control de acceso a IoT Hub](iot-hub-devguide-security.md). | Event Grid proporciona validación en tres puntos: suscripciones a eventos, publicación de eventos y entrega de eventos de webhook. Para más información, vea [Event Grid security and authentication](../event-grid/security-authentication.md) (Seguridad y autenticación de Event Grid). |

## <a name="how-to-choose"></a>Cómo elegir

El enrutamiento de mensajes IoT Hub y la integración de Hub IoT con Event Grid realizan diferentes acciones para lograr resultados similares. Ambos toman información de la solución de IoT Hub y la transmiten para que otros servicios puedan reaccionar. Entonces, ¿cómo se decide cuál usar? Además de los datos de la sección anterior, utilice las siguientes preguntas para guiar su decisión: 

* **¿Qué tipo de datos se envían a los puntos de conexión?**

   Utilice el enrutamiento de mensajes de IoT Hub cuando tenga que enviar datos de telemetría a otros servicios. El enrutamiento de mensajes también permite consultar los encabezados y el cuerpo de los mensajes. 

   La integración de Hub de IoT con Event Grid funciona con eventos que tienen lugar en el servicio IoT Hub. Estos eventos de IoT Hub incluyen la eliminación y creación de dispositivos. 

* **¿Qué puntos de conexión deben recibir esta información?**

   El enrutamiento de mensajes de IoT Hub admite puntos de conexión limitados, pero puede crear conectores para redirigir los datos y eventos a puntos de conexión adicionales. Para obtener una lista completa de los puntos de conexión admitidos, consulte la tabla de la sección anterior. 

   La integración de IoT Hub con Event Grid admite varios puntos de conexión. También trabaja con webhooks para extender el enrutamiento fuera del ecosistema de los servicios de Azure y en aplicaciones empresariales de terceros. 

* **¿Importa si los datos llegan en orden?**

   El enrutamiento de mensajes de IoT Hub mantiene el orden en el que se envían los mensajes, de modo que lleguen de la misma manera.

   Event Grid no garantiza que los puntos de conexión reciban eventos en el mismo orden en que se produjeron. Sin embargo, el esquema del evento incluye una marca de tiempo que se puede utilizar para identificar el orden después de que los eventos lleguen al punto de conexión. 

## <a name="next-steps"></a>pasos siguientes

* Más información sobre el [enrutamiento de mensajes de IoT Hub](iot-hub-devguide-messages-d2c.md) y [los puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md).

* Más información sobre [Azure Event Grid](../event-grid/overview.md)

* Pruebe la integración de Event Grid mediante el [envío de notificaciones por correo electrónico sobre los eventos de Azure IoT Hub con Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).