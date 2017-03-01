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
ms.date: 02/21/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: 7587b1bed2f809fa2c4bab78c54396eed778b9ef
ms.openlocfilehash: df2d79fdb4a26509f3c7c1f8f3a8adcaa6b24f9d
ms.lasthandoff: 02/21/2017

---

# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidad y coherencia en Event Hubs

## <a name="overview"></a>Información general
Azure Event Hubs usa un [modelo de creación de particiones](event-hubs-what-is-event-hubs.md#partitions) para mejorar la disponibilidad y paralelización dentro de un solo centro de eventos. Por ejemplo, si un centro de eventos tiene cuatro particiones y una de estas particiones se mueve desde un servidor a otro en una operación de equilibrio de carga, puede enviar y recibir desde otras tres particiones. Además, varias particiones permiten disponer de más lectores simultáneos para el procesamiento de los datos, lo que supone una mejora del rendimiento agregado. Conocer las implicaciones de la creación de particiones y la ordenación de un sistema distribuido es un aspecto fundamental del diseño de soluciones.

Para ayudar a explicar el equilibrio entre la ordenación y la disponibilidad, se puede consultar el [teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), también conocido como teorema de Brewer. El teorema indica que hay que elegir entre coherencia, disponibilidad y tolerancia a la partición.

El teorema define la coherencia y la disponibilidad de la forma siguiente:
* Tolerancia a la partición: la capacidad de un sistema de procesamiento de datos de continuar procesando datos aunque se produzcan errores en una partición.
* Disponibilidad: un nodo sin error devuelve una respuesta razonable en un plazo prudente (sin errores ni tiempos de espera).
* Coherencia: se garantiza que una lectura devuelva la última lectura de un cliente determinado.

## <a name="partition-tolerance"></a>Tolerancia a la partición
Event Hubs se basa en un modelo con particiones. Puede configurar el número de particiones en el centro de eventos durante la instalación, pero no se puede cambiar este valor más adelante. Puesto que se deben utilizar particiones con Event Hubs, solo necesita tomar una decisión con respecto a la disponibilidad y la coherencia de la aplicación.

## <a name="availability"></a>Disponibilidad
La manera más sencilla de empezar a trabajar con Event Hubs es usar el comportamiento predeterminado. Si crea un nuevo `EventHubClient` y usa la función de envío, los eventos se distribuyen automáticamente entre las particiones en el centro de eventos. Este comportamiento permite disfrutar del máximo tiempo de actividad.

Para los casos de uso que requieren el máximo tiempo de actividad, se prefiere este modelo.

## <a name="consistency"></a>Coherencia
En escenarios concretos, el orden de los eventos puede ser importante. Por ejemplo, puede que prefiera el sistema back-end para procesar un comando de actualización antes que un comando de eliminación. En este caso, puede establecer la clave de partición en un evento, o usar un `PartitionSender` para solo enviar eventos a una determinada partición. De esta forma, se garantiza que, cuando se lean eventos de la partición, la lectura siga un orden.

Con este tipo de configuración, debe tener en cuenta que si la partición concreta a la que se realiza el envío no se encuentra disponible, recibirá una respuesta de error. Como punto de comparación, si no tiene una afinidad para una sola partición, el servicio Event Hubs enviaría el evento a la siguiente partición disponible.

Una posible solución para garantizar la ordenación, al mismo tiempo que se maximiza el tiempo de actividad, consistiría en agregar eventos como parte de la aplicación de procesamiento de eventos. La manera más fácil de lograr esto sería marcar el evento con una propiedad de número de secuencia personalizada. A continuación se muestra un ejemplo:

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

El ejemplo anterior podría enviar el evento a una de las particiones disponibles en el centro de eventos y establecer el número de secuencia correspondiente de la aplicación. Esta solución requiere que la aplicación de procesamiento conserve el estado, pero se proporcionaría a los remitentes un punto de conexión con más probabilidades de estar disponible.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)

