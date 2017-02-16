---
title: "Información general de los niveles de precios de mensajería Estándar y Premium de Service Bus|Microsoft Docs"
description: "Mensajería Premium y Estándar del Bus de servicio"
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
ms.date: 01/18/2017
ms.author: darosa;sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 9dd2696ebf4e2c4749471e5adcbc446c3945babf


---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Niveles de mensajería Premium y Estándar del Bus de servicio
La mensajería de Service Bus, que incluye entidades de mensajería como colas y temas, combina funcionalidades de mensajería con una completa semántica de publicación-suscripción en la escala de nube. La mensajería de Service Bus se utiliza como la red troncal de comunicación para muchas soluciones sofisticadas en la nube.

El nivel *Premium* de la mensajería de Service Bus atiende solicitudes comunes de los clientes con relación a la escala, el rendimiento y la disponibilidad para aplicaciones fundamentales. Aunque los conjuntos de características son prácticamente idénticos, estos dos niveles de mensajería de Service Bus están diseñados para usarse en distintas situaciones.

En la tabla siguiente, se resaltan algunas de las principales diferencias.

| Premium | Estándar |
| --- | --- |
| Capacidad de proceso elevada |Capacidad de proceso variable |
| Rendimiento predecible |Latencia variable |
| Precio fijo |Precios según la variante de pago por uso |
| Posibilidad de escalar y de reducir verticalmente la carga de trabajo |N/D |
| Tamaño de mensaje de hasta 1 MB |Tamaño de mensaje de hasta 256 KB |

La **mensajería Premium de Service Bus** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium del Bus de servicio. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en el Bus de servicio.

Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. La mensajería Premium de Service Bus se basa en el motor de almacenamiento presentado en [Azure Events Hubs](https://azure.microsoft.com/services/event-hubs/). Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

## <a name="premium-messaging-technical-differences"></a>Diferencias técnicas de la mensajería Premium
A continuación se presentan algunas diferencias existentes entre los niveles de mensajería Estándar y Premium.

### <a name="partitioned-queues-and-topics"></a>Temas y colas con particiones
Las colas y los temas con particiones se admiten en la mensajería Premium, pero no funcionan de la misma forma que en los niveles Estándar y Básico de la mensajería de Service Bus. La mensajería Premium no utiliza SQL como almacén de datos y ya no tiene la posible competencia de recursos asociada a una plataforma compartida. Por consiguiente, no es necesario crear particiones para el rendimiento. Además, se cambió la cantidad de particiones desde la cifra de 16 particiones en la mensajería Estándar a 2 particiones en Premium. Tener dos particiones garantiza la disponibilidad y es un número más apropiado para el entorno de tiempo de ejecución Premium. Para más información sobre las particiones, consulte [Temas y colas con particiones](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades exprés
Dado que la mensajería Premium se ejecuta en un entorno de tiempo de ejecución completamente aislado, no se admiten entidades exprés en los espacios de nombres Premium. Para más información sobre la característica exprés, consulte la propiedad [QueueDescription.EnableExpress](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

## <a name="get-started-with-premium-messaging"></a>Introducción a la Mensajería premium

La introducción a la mensajería premium es muy sencilla y el proceso es similar al de la mensajería estándar. Comience con la [creación de un espacio de nombres](service-bus-create-namespace-portal.md). Asegúrese de que selecciona **Premium** en **Plan de tarifa**.

![create-premium-namespace][create-premium-namespace]

También puede crear un [espacio de nombres premium con plantillas de Azure Resource Manager](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/).


## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la mensajería de Service Bus, consulte los siguientes temas.

* [Introducción a la mensajería Premium de Azure Service Bus (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducción a la mensajería Premium de Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Introducción a la mensajería de Service Bus](service-bus-messaging-overview.md)
* [Utilización de las colas del Bus de servicio](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png



<!--HONumber=Jan17_HO3-->


