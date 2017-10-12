---
title: "Introducción a la capacidad dedicada de Microsoft Azure Event Hubs | Microsoft Docs"
description: "Introducción a la capacidad dedicada de Microsoft Azure Event Hubs."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: db8b119178de0e565b2064e9a52d5e9989d60d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Introducción a Event Hubs dedicado

La capacidad *dedicada de Event Hubs* ofrece implementaciones de un único inquilino para los clientes con los requisitos más exigentes. A escala completa, Azure Event Hubs puede incorporar más de dos millones de eventos por segundo o hasta 2 GB por segundo de telemetría con un almacenamiento totalmente duradero y una latencia inferior al segundo. Esto también permite soluciones integradas mediante el procesamiento en tiempo real y por lotes en el mismo sistema. Con [Event Hubs Capture](event-hubs-capture-overview.md) incluido en la oferta, un solo flujo permite canalizaciones en tiempo real y basadas en lotes, lo cual reduce la complejidad de la solución.

En la tabla siguiente se comparan niveles de servicio disponibles de Event Hubs. La oferta de Event Hubs Dedicado consiste en un precio mensual fijo, frente a los precios por uso de la mayoría de las características del nivel Estándar. El nivel Dedicado ofrece las características del plan estándar, pero con capacidad de escala de empresa para clientes con cargas de trabajo exigentes. 

| Característica | Estándar | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pago por millones de eventos | Se incluye |
| Unidad de rendimiento (entrada de 1 MB/seg., salida de 2 MB/seg.) | Pago por hora | Se incluye |
| Tamaño de los mensajes | 256 KB | 1 MB |
| Directivas de publicadores | Sí | Sí |   
| Grupos de consumidores | 20 | | 20 | |
| Redifusión de mensajes | Sí | Sí |
| Unidades de rendimiento máximo | 20 (flexible a 100)   | 1 CU≈200 |
| Conexiones asincrónicas | 1000 incluidos | 100 000 incluidos |
| Conexiones desacopladas adicionales | Sí | Sí |
| Retención de mensajes | 1 día incluido | Hasta 7 días incluidos |
| Capture | Pago por hora | Se incluye |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Ventajas de Event Hubs con capacidad dedicada

Dispone de las siguientes ventajas cuando utiliza Event Hubs con capacidad dedicada:

* Hospedaje de un solo inquilino sin ruido de otros inquilinos.
* El tamaño de los mensajes aumenta a 1 MB frente a los 256 KB del nivel Estándar.
* Rendimiento repetible cada vez.
* Capacidad garantizada para satisfacer sus necesidades de ráfagas.
* Escalable entre 1 y 8 unidades de capacidad: proporciona un máximo de 2 millones de eventos de entrada por segundo.
  * Las unidades de capacidad administran la escala de Event Hubs dedicado, donde cada unidad puede proporcionar aproximadamente el equivalente de 200 unidades de rendimiento.
* Cero mantenimiento: nosotros administramos el equilibrio de carga, las actualizaciones del sistema operativo, las revisiones de seguridad y la creación de particiones.
* Precio fijo mensual.

Event Hubs dedicado también elimina algunas de las limitaciones de rendimiento de la oferta Estándar. Las unidades de procesamiento del nivel Estándar proporcionan 1000 eventos por segundo o 1 MB por segundo de entrada por unidad de procesamiento, así como el doble de esa cantidad de salida. La oferta de escala Dedicado no tiene ninguna restricción en la cantidad de eventos de entrada y de salida. Estos límites solo se rigen por la capacidad de procesamiento de los centros de eventos adquiridos.

Este servicio está dirigido a los usuarios de telemetría más grandes y está disponible para los clientes con un contrato Enterprise.

## <a name="how-to-onboard"></a>¿Cómo incorporarlo?

La plataforma de Event Hubs dedicado se ofrece al público a través de un contrato Enterprise con distintos tamaños de unidades de capacidad. Cada unidad de capacidad proporciona aproximadamente el equivalente a 200 unidades de procesamiento. Puede aumentar o reducir verticalmente su capacidad a lo largo del mes para satisfacer sus necesidades agregando o quitando unidades de rendimiento. El plan dedicado es único en el sentido de que tendrá más ayuda del equipo del producto Event Hubs en el proceso de incorporación con el fin de obtener la implementación flexible adecuada. 

## <a name="next-steps"></a>Pasos siguientes
Póngase en contacto con su representante de ventas de Microsoft o el Servicio de soporte técnico de Microsoft para obtener detalles adicionales sobre la capacidad Event Hubs Dedicado. Para más información acerca de Event Hubs, visite los vínculos siguientes:

Para obtener información detallada sobre el precio, consulte los siguientes vínculos:

- [Precios de Event Hubs dedicado](https://azure.microsoft.com/pricing/details/event-hubs/). También puede ponerse en contacto con su representante de ventas de Microsoft o el Servicio de soporte técnico de Microsoft para obtener detalles adicionales sobre la capacidad dedicada de Event Hubs.
- En [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md) se proporciona información sobre los precios, así como respuestas a algunas preguntas frecuentes acerca de Event Hubs. 
