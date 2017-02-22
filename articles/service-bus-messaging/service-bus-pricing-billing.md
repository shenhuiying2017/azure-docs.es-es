---
title: "Precios y facturación de Service Bus | Microsoft Docs"
description: "Información general de la estructura de precios de Bus de servicio."
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
ms.date: 02/16/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: bd042908fec2dcf499dd1cb5230f62ec4be9fdea


---
# <a name="service-bus-pricing-and-billing"></a>Precios y facturación de Bus de servicio
Service Bus está disponible en los niveles Básico, Estándar y [Premium](service-bus-premium-messaging.md). Puede elegir un nivel de servicio para cada espacio de nombres del servicio Service Bus que cree y esta selección de nivel se aplica a todas las entidades creadas dentro de ese espacio de nombres.

> [!NOTE]
> Para más información sobre los precios actuales de Service Bus, consulte la [página de precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) y las [preguntas más frecuentes sobre Service Bus](service-bus-faq.md#pricing).
>
>

Service Bus usa los dos siguientes medidores para colas y temas o suscripciones:

1. **Operaciones de mensajería**: definidas como llamadas API en los puntos de conexión de servicio de colas o temas y suscripciones. Este medidor reemplazará los mensajes enviados o recibidos como unidad principal de uso facturable para las colas y los temas o las suscripciones.
2. **Conexiones asincrónicas**: se definen como el número máximo de conexiones persistentes abiertas en colas, temas y suscripciones, durante un período de muestreo dado de una hora. Este medidor solo se aplicará en el nivel Estándar, en el que puede abrir conexiones adicionales (anteriormente, las conexiones estaban limitadas a 100 por cola, tema o suscripción) por un módico precio por conexión.

El nivel **Estándar** tiene precios escalonados para las operaciones realizadas con colas y temas o suscripciones, lo que implica descuentos basados en volumen de hasta el 80 % para los niveles de máximo uso. También hay un cargo base para el nivel Estándar de 10 $ al mes, que le permite realizar hasta 12,5 millones de operaciones al mes sin ningún costo adicional.

El nivel **Premium** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium del Bus de servicio. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en el Bus de servicio. Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. La mensajería Premium de Bus de servicio de Azure se basa en el motor de almacenamiento presentado en los Centros de eventos de Azure. Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

Tenga en cuenta que el cargo base estándar se realiza solo una vez al mes por suscripción de Azure, lo que significa que después de crear un espacio de nombres de Service Bus de nivel Estándar o Premium, podrá crear tantos espacios de nombre de nivel Estándar o Premium como desee dentro de la misma suscripción de Azure, sin incurrir en cargos base adicionales.

Todos los espacios de nombres de Bus de servicio existentes creados antes del 1 de noviembre de 2014 pasarán automáticamente a formar parte del nivel Estándar. Esto garantiza que continuará teniendo acceso a todas las características disponibles actualmente con Bus de servicio. Posteriormente, puede usar el [Portal de Azure clásico][Azure classic portal] para cambiar al nivel Básico, si lo desea.

En la tabla siguiente se resumen las diferencias funcionales entre los niveles Básico, Estándar y Premium.

| Capacidad | Básica | Estándar/Premium |
| --- | --- | --- |
| Colas |Sí |Sí |
| Mensajes programados |Sí |Sí |
| Temas/suscripciones |No |Sí |
| Relés |No |Sí |
| Transacciones |No |Sí |
| Desduplicación |No |Sí |
| Sesiones |No |Sí |
| Mensajes grandes |No |Sí |
| ForwardTo |No |Sí |
| SendVia |No |Sí |
| Conexiones asíncronas (incluidas) |100 por cada espacio de nombres de Bus de servicio |1000 por cada suscripción de Azure |
| Conexiones asíncronas (se permite el uso por encima del límite) |No |SÍ (facturable) |

## <a name="messaging-operations"></a>Operaciones de mensajería
Como parte del nuevo modelo de fijación de precios, la facturación de colas, temas y suscripciones está cambiando. Estas entidades están pasando de una facturación por mensaje a una facturación por operación. Una "operación" hace referencia a cualquier llamada API que se realiza a un punto de conexión de servicio de una cola, un temas o una suscripción. Esto incluye las operaciones de administración, envío/recepción y estado de la sesión.

| Tipo de operación | Description |
| --- | --- |
| Administración |Creación, lectura, actualización, eliminación (CRUD) en colas, temas o suscripciones. |
| Mensajería |Envío y recepción de mensajes con colas, temas o suscripciones. |
| Estado de la sesión |Recuperación o establecimiento del estado de la sesión en una cola, un tema o una suscripción. |

Los siguientes precios están vigentes desde el 1 de noviembre de 2014:

| Básica | Coste |
| --- | --- |
| Operaciones |0,05 $ por cada millón de operaciones |

| Estándar | Coste |
| --- | --- |
| Cargo base |10 $/mes |
| Los 12,5 millones de operaciones primeras al mes |Se incluye |
| De&12;,5 a&100; millones de operaciones en un mes |0,80 $ por cada millón de operaciones |
| De&100; a&2;.500 millones de operaciones en un mes |0,50 $ por cada millón de operaciones |
| Por encima de 2.500 millones de operaciones en un mes |0,20 $ por cada millón de operaciones |

| Premium | Coste |
| --- | --- |
| Diario |11,13 $ de tarifa fija por unidad de mensaje |

## <a name="brokered-connections"></a>Conexiones asincrónicas
Las *conexiones asincrónicas* se adaptan a los patrones de uso de clientes que tienen que ver con una gran cantidad de remitentes/receptores "conectados de forma persistente" a colas, temas o suscripciones. Los remitentes/receptores conectados de forma persistente son aquellos que se conectan mediante AMQP o HTTP con un tiempo de expiración de recepción distinto a cero (por ejemplo, HTTP long polling). Los remitentes y receptores con un tiempo de expiración inmediato no generarán conexiones asíncronas.

Anteriormente, las colas, los temas y las suscripciones tenían un límite de 100 conexiones concurrentes por dirección URL. El esquema de facturación actual quita el límite por dirección URL de las colas, los temas y las suscripciones, e implementa cuotas y mediciones en las conexiones asincrónicas en los niveles de suscripción de Azure y de espacio de nombres de Service Bus.

El nivel Básico incluye 100 conexiones asíncronas (y está estrictamente limitado a ellas) por cada espacio de nombres de Bus de servicio. Las conexiones por encima de este número se rechazarán en el nivel Básico. El nivel Estándar elimina el límite por espacio de nombres y cuenta el uso por conexión asincrónica agregado en toda la suscripción de Azure. En el nivel Estándar, se permitirán 1000 conexiones asíncrónicas por cada suscripción de Azure sin costo adicional (más allá del cargo base). Si se usan más de 1000 conexiones asíncrónicas totales en los espacios de nombres de Service Bus de nivel Estándar en una suscripción de Azure, la facturación se realizará de forma escalonada, como se muestra en la siguiente tabla.

| Conexiones asíncronas (nivel Estándar) | Coste |
| --- | --- |
| Primeras 1.000 del mes |Incluidas con el cargo base |
| De&1;.000 a&100;.000 en un mes |0,03 $ por conexión y mes |
| De&100;.000 a&500;.000 en un mes |0,025 $ por conexión y mes |
| Más de 500.000 en un mes |0,015 $ por conexión y mes |

> [!NOTE]
> Con el nivel de mensajería Estándar (en el cargo base) se incluyen&1000; conexiones asincrónicas, que se pueden compartir entre todas las colas, temas y suscripciones de la suscripción de Azure asociada.
>
>

<br />

> [!NOTE]
> La facturación se basa en el número máximo de conexiones concurrentes y se prorratea por horas, basándose en 744 horas al mes.
>
>

| Nivel Premium |
| --- |
| No se cobran las conexiones asíncronas en el nivel Premium. |

Para más información sobre las conexiones asincrónicas, consulte la sección [Preguntas más frecuentes](#faq) de este mismo tema.

## <a name="wcf-relay"></a>Retransmisión de WCF
Las retransmisiones de WCF solo están disponibles en los espacios de nombres de nivel Estándar. En cualquier otro caso, el precio y las cuotas de conexión de las retransmisiones permanecen igual. Esto significa que las retransmisiones seguirán cobrándose según el número de mensajes (y no de operaciones) y las horas de retransmisión.

| Precios de WCF Relay | Coste |
| --- | --- |
| Horas de WCF Relay |0,10 $ por cada 100 horas de retransmisión |
| error de Hadoop |0,01 $ por cada 10.000 mensajes |

## <a name="faq"></a>P+F
### <a name="how-is-the-wcf-relay-hours-meter-calculated"></a>¿Cómo se calcula el contador de horas de WCF Relay?
Consulte [este tema](service-bus-faq.md).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>¿Qué son las conexiones asíncronas y cómo se cobran?
Una conexión asíncrónica se define como una de estas dos posibilidades:

1. Una conexión AMQP desde un cliente a una cola, tema o suscripción de Service Bus.
2. Una llamada HTTP para recibir un mensaje desde un tema o una cola de Service Bus que tenga un valor de tiempo de expiración de recepción mayor que cero.

Bus de servicio cobra el número máximo de conexiones asíncronas concurrentes que superen la cantidad incluida (1.000 en el nivel Estándar). Los valores máximos se miden cada hora, que se prorratea dividiéndolos por 744 horas en un mes y sumándolos al periodo de facturación mensual. La cantidad incluida (1000 conexiones asíncrónicas al mes) se aplica al final del periodo de facturación contra la suma de los máximos de horas prorrateados.

Por ejemplo:

1. Los 10 000 dispositivos se conectan mediante una conexión AMQP individual y reciben comandos desde un tema de Service Bus. Los dispositivos envían eventos de telemetría a un Centro de eventos. Si todos los dispositivos están conectados 12 horas al día, se aplican los siguientes cargos por conexión (además de los restantes cargos del tema de Service Bus): 10 000 conexiones * 12 horas * 31 días / 744 = 5000 conexiones asíncrónicas. Después de permitir la conexión mensual de 1.000 conexiones asíncronas, se le cobrarían 4.000 conexiones asíncronas, con un importe de 0,03 $ por cada conexión asíncrona, que daría un total de 120 $.
2. 10.000 dispositivos reciben mensajes desde una cola de Bus de servicio mediante HTTP, especificando un tiempo de expiración distinto a cero. Si todos los dispositivos se conectan 12 horas al día, verá los siguientes cargos por conexión (además de los restantes cargos de Service Bus): 10 000 conexiones de recepción HTTP * 12 horas al día * 31 días / 744 horas = 5000 conexiones asíncrónicas.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>¿Se aplican los cargos por conexión asíncrónica a las colas, los temas y las suscripciones?
Sí. No hay ningún cargo de conexión por el envío de eventos mediante HTTP, independientemente del número de sistemas o dispositivos emisores. La recepción de eventos con HTTP con un tiempo de expiración superior a cero, que a menudo se conoce como "long polling", genera cargos por conexión asíncrona. Las conexiones AMQP generan cargos por conexiones asíncronas, sin importar si las conexiones se usan para enviar o para recibir. Tenga en cuenta que se permiten 100 conexiones asíncronas sin cargo en un espacio de nombres Básico. Este es también el número máximo de conexiones asíncrónicas que se permiten para la suscripción de Azure. Las mil primeras conexiones asíncrónicas de todos los espacios de nombres Estándar de una suscripción de Azure se incluyen sin cargo adicional (más allá del cargo base). Dado que estos números son suficientes para cubrir una gran cantidad de escenarios de mensajería entre servicios, los cargos por conexión asíncrona normalmente solo son relevantes si planea usar AMQP o HTTP long-polling con un gran número de clientes; por ejemplo, para conseguir una mayor eficiencia en el streaming de eventos o para habilitar la comunicación bidireccional con muchos dispositivos o instancias de aplicación.

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de los precios de Service Bus, consulte la [página de precios de Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Consulte las [preguntas más frecuentes sobre Service Bus](service-bus-faq.md#pricing) para ver algunas preguntas comunes relacionadas con los precios y la facturación de Service Bus.

[Azure classic portal]: http://manage.windowsazure.com



<!--HONumber=Feb17_HO2-->


