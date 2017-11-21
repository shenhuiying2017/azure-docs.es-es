---
title: "Información general acerca de los planes de tarifa de mensajería Estándar y Premium de Azure Service Bus|Microsoft Docs"
description: "Niveles de mensajería Premium y Estándar de Service Bus"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: sethm
ms.openlocfilehash: 613bb074063e436cdbd54fe5aee9c49109a2d8f2
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Niveles de mensajería Premium y Estándar de Service Bus

La mensajería de Service Bus, que incluye entidades como colas y temas, combina las funcionalidades de la mensajería empresarial con una completa semántica de publicación-suscripción en la escala de nube. La mensajería de Service Bus se utiliza como la red troncal de comunicación para muchas soluciones sofisticadas en la nube.

El nivel *Premium* de la mensajería de Service Bus atiende solicitudes comunes de los clientes con relación a la escala, el rendimiento y la disponibilidad para aplicaciones fundamentales. Aunque los conjuntos de características son prácticamente idénticos, estos dos niveles de mensajería de Service Bus están diseñados para usarse en distintas situaciones.

En la tabla siguiente, se resaltan algunas de las principales diferencias.

| Premium | Estándar |
| --- | --- |
| Capacidad de proceso elevada |Capacidad de proceso variable |
| Rendimiento predecible |Latencia variable |
| Precio fijo |Precios según la variante de pago por uso |
| Posibilidad de escalar y de reducir verticalmente la carga de trabajo |N/D |
| Tamaño de mensaje de hasta 1 MB |Tamaño de mensaje de hasta 256 KB |

La **mensajería Premium de Service Bus** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium de Service Bus. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en Service Bus.

Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. La mensajería Premium de Service Bus se basa en el motor de almacenamiento presentado en [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

## <a name="premium-messaging-technical-differences"></a>Diferencias técnicas de la mensajería Premium

En las secciones siguientes se describen algunas diferencias existentes entre los niveles de mensajería Estándar y Premium.

### <a name="partitioned-queues-and-topics"></a>Temas y colas con particiones

Los temas y colas con particiones se admiten en la mensajería Premium; de hecho, estas entidades siempre tienen particiones (y no se pueden deshabilitar). Sin embargo, las colas y los temas con particiones Premium no funcionan de la misma forma que en el nivel Estándar de la mensajería de Service Bus. La mensajería Premium no utiliza SQL como almacén de datos y ya no tiene la posible competencia de recursos asociada a una plataforma compartida. Como consecuencia, las particiones no son necesarias para mejorar el rendimiento. Además, se cambió la cantidad de particiones desde la cifra de 16 particiones en la mensajería Estándar a 2 particiones en Premium. Tener dos particiones garantiza la disponibilidad y es un número más apropiado para el entorno de tiempo de ejecución Premium. 

Con la mensajería premium, cuando se especifica el tamaño de una entidad con [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes), dicho tamaño se divide por igual entre las dos particiones, a diferencia de las [entidades con particiones estándar](service-bus-partitioning.md#standard), en las que el tamaño total es 16 veces el tamaño especificado. 

Para más información sobre las particiones, consulte [Temas y colas con particiones](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades exprés

Dado que la mensajería premium se ejecuta en un entorno de tiempo de ejecución completamente aislado, no se admiten entidades rápidas en los espacios de nombres Premium. Para más información sobre la característica exprés, consulte la propiedad [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Si tiene código en ejecución en mensajería estándar y desea migrarlo al nivel Premium, asegúrese de que la propiedad [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) esté establecida en **false** (valor predeterminado).

## <a name="get-started-with-premium-messaging"></a>Introducción a la Mensajería premium

La introducción a la mensajería premium es muy sencilla y el proceso es similar al de la mensajería estándar. Comience por [crear un espacio de nombres](service-bus-create-namespace-portal.md) en [Azure portal](https://portal.azure.com). Asegúrese de que selecciona **Premium** en **Elegir su plan de tarifa**.

![create-premium-namespace][create-premium-namespace]

También puede crear un [espacios de nombres premium con plantillas de Azure Resource Manager](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas.

* [Introducción a la mensajería Premium de Azure Service Bus (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducción a la mensajería Premium de Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Introducción a la mensajería de Service Bus](service-bus-messaging-overview.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
