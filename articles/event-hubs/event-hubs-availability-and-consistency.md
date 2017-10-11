---
title: Disponibilidad y coherencia en Azure Event Hubs | Microsoft Docs
description: "Cómo proporcionar el máximo nivel de disponibilidad y coherencia con Azure Event Hubs mediante el uso de particiones."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 681a9d1636d547492f6f827461c6b2494b918778
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidad y coherencia en Event Hubs

## <a name="overview"></a>Información general
Azure Event Hubs usa un [modelo de creación de particiones](event-hubs-features.md#partitions) para mejorar la disponibilidad y paralelización dentro de un solo centro de eventos. Por ejemplo, si un centro de eventos tiene cuatro particiones y una de ellas se mueve de un servidor a otro en una operación de equilibrio de carga, se puede enviar y recibir desde las otras tres. Además, tener un mayor número de particiones permite que más lectores simultáneos procesen los datos, lo que mejora el rendimiento agregado. Conocer las implicaciones de la creación de particiones y la ordenación de un sistema distribuido es un aspecto fundamental del diseño de soluciones.

Para ayudar a explicar el equilibrio entre ordenación y disponibilidad, consulte el [teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), que también se conoce como teorema de Brewer. Dicho teorema trata la elección entre coherencia, disponibilidad y tolerancia a la partición.

El teorema de Brewer define la coherencia y la disponibilidad de la forma siguiente:
* Tolerancia a la partición: la capacidad de un sistema de procesamiento de datos de continuar procesando datos aunque se produzcan errores en una partición.
* Disponibilidad: un nodo sin error devuelve una respuesta razonable en un plazo prudente (sin errores ni tiempos de espera).
* Coherencia: se garantiza que una lectura devuelva la última escritura de un cliente determinado.

## <a name="partition-tolerance"></a>Tolerancia a la partición
Event Hubs se basa en un modelo de datos con particiones. Se puede configurar el número de particiones en el centro de eventos durante la instalación, pero no se puede cambiar este valor más adelante. Puesto que se deben utilizar particiones con Event Hubs, debe tomar una decisión con respecto a la disponibilidad y la coherencia de la aplicación.

## <a name="availability"></a>Disponibilidad
La manera más sencilla de empezar a trabajar con Event Hubs es usar el comportamiento predeterminado. Si crea un objeto `EventHubClient` nuevo y usa el método `Send`, los eventos se distribuyen automáticamente entre las particiones del centro de eventos. Este comportamiento permite disfrutar del máximo tiempo de actividad.

Para los casos de uso que requieren el máximo tiempo de actividad, se prefiere este modelo.

## <a name="consistency"></a>Coherencia
En algunos escenarios, el orden de los eventos puede ser importante. Por ejemplo, puede que prefiera el sistema back-end para procesar un comando de actualización antes que un comando de eliminación. En este caso, puede establecer la clave de partición en un evento, o usar un objeto `PartitionSender` para enviar eventos solo a una determinada partición. De esta forma, se garantiza que, cuando se lean eventos de la partición, la lectura siga un orden.

Con esta configuración, tenga en cuenta que si la partición concreta a la que se realiza el envío no se encuentra disponible, recibirá una respuesta de error. Como punto de comparación, si no tiene una afinidad para una sola partición, el servicio Event Hubs envía el evento a la siguiente partición disponible.

Una posible solución para garantizar el orden, mientras también se maximiza el tiempo de actividad, sería agregar eventos como parte de la aplicación de procesamiento de eventos. La manera más fácil de lograr esto es marcar el evento con una propiedad de número de secuencia personalizada. El código siguiente muestra un ejemplo:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

Este ejemplo envía el evento a una de las particiones disponibles en el centro de eventos y establece el número de secuencia correspondiente a partir de la aplicación. Esta solución requiere que la aplicación de procesamiento conserve el estado, pero proporciona a los remitentes un punto de conexión con más probabilidades de estar disponible.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general sobre el servicio Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
