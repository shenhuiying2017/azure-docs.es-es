---
title: "Introducción a Azure Event Hubs Dedicado | Microsoft Docs"
description: Microsoft Azure Event Hubs
services: event-hubs
author: banisadr
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 6aebaeccc502d20c8a80297972b8f3b5e8e363c6
ms.openlocfilehash: 3cd3d3fa7a540f9608ac07172bb78ee719d6b0a6


---


# <a name="dedicated-event-hubs--an-overview"></a>Event Hubs Dedicado: introducción

La capacidad de Event Hubs Dedicado ofrece implementaciones de un único inquilino para los clientes con los requisitos más exigentes. A escala completa, Azure Event Hubs puede incorporar más de dos millones de eventos por segundo o hasta 2 GB por segundo de telemetría con un almacenamiento totalmente duradero y una latencia inferior al segundo. Esto también permite soluciones integradas mediante el procesamiento en tiempo real y por lotes en el mismo sistema. Gracias a la característica Event Hubs Archive incluida en la oferta, una sola transmisión permite canalizaciones en tiempo real y basadas en lotes, lo que reduce la complejidad de su solución.

En la tabla siguiente se comparan niveles de servicio disponibles de Event Hubs. La oferta de Event Hubs Dedicado consiste en un precio mensual fijo, frente a los precios por uso para la mayoría de las características de los niveles Estándar y Básico. El nivel Dedicado ofrece las características del plan estándar, pero con capacidad de escala de empresa para clientes con cargas de trabajo exigentes.

|  | Básico | Estándar | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pago por millones de eventos | Pago por millones de eventos | Se incluye |
| Unidad de rendimiento (entrada de 1 MB/s, salida de 2 MB/s) | Pago por hora | Pago por hora | Se incluye |
| Tamaño de los mensajes | 256 KB | 256 KB | 1MB |
| Directivas de publicadores | N/D | ✓ | ✓ |     
| Grupos de consumidores | 1: predeterminado | 20 | | 20 | |
| Redifusión de mensajes | ✓ | ✓  | ✓ |
| Unidades de rendimiento máximo | 20 | | 20 (flexible a 100)  | 1 CU≈200 |
| Conexiones asincrónicas | 100 incluidos | 1000 incluidos | 100&000; incluidos |
| Conexiones desacopladas adicionales | N/D | ✓ | ✓ |
| Retención de mensajes | 1 día incluido | 1 día incluido | Hasta 7 días incluidos |
| Archive  <sup>Versión preliminar</sup> | N/D  | Pago por hora | Se incluye |

## <a name="benefits-of-event-hubs-at-dedicated-capacity-include"></a>Las ventajas de Event Hubs con la capacidad Dedicado incluyen:

* Hospedaje de un solo inquilino sin ruido de otros inquilinos
* El tamaño de los mensajes aumenta a 1 MB frente a 256 KB de los niveles Estándar y Básico
* Rendimiento repetible cada vez
* Capacidad garantizada para satisfacer sus necesidades de ráfagas
* Escalable entre 1 y 8 unidades de capacidad: proporciona un máximo de 2 millones de eventos de entrada por segundo
  * Las unidades de capacidad administran la escala para Event Hubs Dedicado, donde cada unidad puede proporcionar aproximadamente el equivalente de 200 unidades de rendimiento.
* Cero mantenimiento: nosotros administramos el equilibrio de carga, las actualizaciones del sistema operativo, las revisiones de seguridad y la creación de particiones
* Precio fijo mensual

Event Hubs Dedicado también elimina algunas de las limitaciones de rendimiento de la oferta Estándar. La unidades de rendimiento en los niveles Estándar y Básico proporcionan a los clientes 1000 eventos por segundo o 1 Mbps de entrada por unidad y dos veces esa cantidad de salida. La oferta de escala Dedicado no tiene ninguna restricción en la cantidad de eventos de entrada y de salida. Estos límites solo se rigen por la capacidad de procesamiento de Event Hubs Dedicado adquirida.

Este servicio está dirigido a los usuarios de telemetría más grandes y está disponible para los clientes con un contrato Enterprise.

## <a name="how-to-onboard"></a>¿Cómo incorporarlo?

La plataforma Event Hubs Dedicado se ofrece al público a través de un contrato Enterprise con distinto tamaño de unidades de capacidad. Cada unidad de capacidad proporciona aproximadamente el equivalente a 200 unidades de rendimiento y se factura a 31 USD la hora. Puede aumentar o reducir verticalmente su capacidad a lo largo del mes para satisfacer sus necesidades agregando o quitando unidades de rendimiento. El plan Dedicado es único en el sentido de que tendrá más ayuda del equipo del producto de Event Hubs para la incorporación con el fin de obtener la implementación flexible adecuada para usted. 


## <a name="next-steps"></a>Pasos siguientes

[Precios de Event Hubs Dedicado](https://azure.microsoft.com/en-us/pricing/details/event-hubs/)

Póngase en contacto con su representante de ventas de Microsoft o el Servicio de soporte técnico de Microsoft para obtener detalles adicionales sobre la capacidad Event Hubs Dedicado.


<!--HONumber=Jan17_HO4-->


