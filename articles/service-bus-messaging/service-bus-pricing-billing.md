---
title: "Precios y facturación de Service Bus | Microsoft Docs"
description: "Información general de la estructura de precios de Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 8ccb44b5009588c28bc79bb45e1a7640ead6c817
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-pricing-and-billing"></a>Precios y facturación de Service Bus

Azure Service Bus está disponible en los niveles Estándar y [Premium](service-bus-premium-messaging.md). Puede elegir un nivel de servicio para cada espacio de nombres del servicio Service Bus que cree y esta selección de nivel se aplica a todas las entidades creadas dentro de ese espacio de nombres.

> [!NOTE]
> Para más información sobre los precios actuales de Service Bus, consulte la [página de precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) y las [preguntas más frecuentes sobre Service Bus](service-bus-faq.md#pricing).
>
>

Service Bus usa los dos siguientes medidores para colas y temas o suscripciones:

1. **Operaciones de mensajería**: definidas como llamadas API en los puntos de conexión de servicio de colas o temas y suscripciones. Este medidor reemplaza los mensajes enviados o recibidos como unidad principal de uso facturable para colas y temas o suscripciones.
2. **Conexiones asincrónicas**: se definen como el número máximo de conexiones persistentes abiertas en colas, temas y suscripciones, durante un período de muestreo dado de una hora. Este medidor solo se aplica en el nivel Estándar, en el que puede abrir conexiones adicionales (anteriormente, las conexiones estaban limitadas a 100 por cola, tema o suscripción) por un módico precio por conexión.

El nivel **Estándar** tiene precios escalonados para las operaciones realizadas con colas y temas o suscripciones, lo que implica descuentos basados en volumen de hasta el 80 % para los niveles de máximo uso. También hay un cargo base para el nivel Estándar de 10 $ al mes, que le permite realizar hasta 12,5 millones de operaciones al mes sin ningún costo adicional.

El nivel **Premium** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium de Service Bus. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en Service Bus. Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido.

Tenga en cuenta que el cargo base del nivel Estándar se realiza solo una vez al mes por suscripción de Azure. Esto significa que, después de crear un espacio de nombres de Service Bus del nivel Estándar, puede crear tantos espacios de nombres Estándar como desee con la misma suscripción de Azure sin incurrir en gastos base adicionales.

En la tabla [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) se resumen las diferencias funcionales entre los niveles Estándar y Premium.

## <a name="messaging-operations"></a>Operaciones de mensajería

Las colas, los temas y las suscripciones se facturan por "operación", no por mensaje. Por "operación" se entiende cualquier llamada API que se realiza en un punto de conexión de servicio de un tema, una suscripción o una cola. Esto incluye las operaciones de administración, envío/recepción y estado de la sesión.

| Tipo de operación | DESCRIPCIÓN |
| --- | --- |
| Administración |Creación, lectura, actualización, eliminación (CRUD) en colas, temas o suscripciones. |
| Mensajería |Envío y recepción de mensajes con colas, temas o suscripciones. |
| Estado de la sesión |Obtención o establecimiento del estado de la sesión de una cola, un tema o una suscripción. |

Para obtener detalles sobre el costo, vea los precios indicados en la página [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="brokered-connections"></a>Conexiones asincrónicas

Las *conexiones asincrónicas* se adaptan a patrones de uso donde hay una gran cantidad de remitentes/receptores "conectados de forma persistente" a colas, temas o suscripciones. Los remitentes/receptores conectados de forma persistente son aquellos que se conectan mediante AMQP o HTTP con un tiempo de expiración de recepción distinto a cero (por ejemplo, HTTP long polling). Los remitentes y receptores con un tiempo de expiración inmediato no generarán conexiones asíncronas.

Para las cuotas de conexión y otros límites de servicio, vea el artículo [Cuotas de Service Bus](service-bus-quotas.md). Para obtener más información sobre las conexiones asincrónicas, consulte la sección [Preguntas más frecuentes](#faq) de este mismo tema.

El nivel Estándar elimina el límite de conexión asincrónica por espacio de nombres y cuenta el uso por conexión asincrónica agregado en toda la suscripción de Azure. Para más información, vea la tabla [Conexiones asincrónicas](https://azure.microsoft.com/pricing/details/service-bus/).

> [!NOTE]
> Con el nivel de mensajería Estándar (en el cargo base) se incluyen 1000 conexiones asincrónicas, que se pueden compartir entre todas las colas, temas y suscripciones de la suscripción de Azure asociada.
>
>

<br />

> [!NOTE]
> La facturación se basa en el número máximo de conexiones concurrentes y se prorratea por horas, basándose en 744 horas al mes.
>
>

### <a name="premium-tier"></a>Nivel Premium

No se cobran las conexiones asíncronas en el nivel Premium.

## <a name="faq"></a>Preguntas más frecuentes

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>¿Qué son las conexiones asíncronas y cómo se cobran?

Una conexión asíncrónica se define como una de estas dos posibilidades:

1. Una conexión AMQP desde un cliente a una cola, tema o suscripción de Service Bus.
2. Una llamada HTTP para recibir un mensaje desde un tema o una cola de Service Bus que tenga un valor de tiempo de expiración de recepción mayor que cero.

Service Bus cobra el número máximo de conexiones asíncronas concurrentes que superen la cantidad incluida (1.000 en el nivel Estándar). Los valores máximos se miden cada hora, que se prorratea dividiéndolos por 744 horas en un mes y sumándolos al periodo de facturación mensual. La cantidad incluida (1000 conexiones asíncrónicas al mes) se aplica al final del periodo de facturación contra la suma de los máximos de horas prorrateados.

Por ejemplo: 

1. Los 10 000 dispositivos se conectan mediante una conexión AMQP individual y reciben comandos desde un tema de Service Bus. Los dispositivos envían eventos de telemetría a un Centro de eventos. Si todos los dispositivos están conectados 12 horas al día, se aplican los siguientes cargos por conexión (además de los restantes cargos del tema de Service Bus): 10 000 conexiones * 12 horas * 31 días / 744 = 5000 conexiones asincrónicas. Después de permitir la conexión mensual de 1.000 conexiones asíncronas, se le cobrarían 4.000 conexiones asíncronas, con un importe de 0,03 $ por cada conexión asíncrona, que daría un total de 120 $.
2. 10.000 dispositivos reciben mensajes desde una cola de Service Bus mediante HTTP, especificando un tiempo de expiración distinto a cero. Si todos los dispositivos se conectan 12 horas al día, verá los siguientes cargos por conexión (además de los restantes cargos de Service Bus): 10 000 conexiones de recepción HTTP * 12 horas al día * 31 días / 744 horas = 5000 conexiones asincrónicas.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>¿Se aplican los cargos por conexión asíncrónica a las colas, los temas y las suscripciones?

Sí. No hay ningún cargo de conexión por el envío de eventos mediante HTTP, independientemente del número de sistemas o dispositivos emisores. La recepción de eventos con HTTP con un tiempo de expiración superior a cero, que a menudo se conoce como "long polling", genera cargos por conexión asíncrona. Las conexiones AMQP generan cargos por conexiones asíncronas, sin importar si las conexiones se usan para enviar o para recibir. Las mil primeras conexiones asíncrónicas de todos los espacios de nombres Estándar de una suscripción de Azure se incluyen sin cargo adicional (más allá del cargo base). Dado que estos números son suficientes para cubrir una gran cantidad de escenarios de mensajería entre servicios, los cargos por conexión asíncrona normalmente solo son relevantes si planea usar AMQP o HTTP long-polling con un gran número de clientes; por ejemplo, para conseguir una mayor eficiencia en el streaming de eventos o para habilitar la comunicación bidireccional con muchos dispositivos o instancias de aplicación.

## <a name="next-steps"></a>pasos siguientes

* Para información completa sobre precios de Service Bus, consulte la [página de precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Vea las [preguntas más frecuentes sobre Service Bus](service-bus-faq.md#pricing) para ver algunas preguntas comunes sobre los precios y la facturación de Service Bus.

[Azure portal]: https://portal.azure.com
