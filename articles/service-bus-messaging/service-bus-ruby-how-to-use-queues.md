---
title: Uso de colas de Service Bus de Azure con Ruby | Microsoft Docs
description: "Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Ruby."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 357a7277dd42b6973cf35a9f642b8eec36a745e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Uso de colas de Service Bus con Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Esta guía describe cómo utilizar las colas del Bus de servicio. Los ejemplos están escritos en Ruby y usan la gema de Azure. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas, envío y recepción de mensajes** y **eliminación de colas**. Para obtener más información sobre las colas de Service Bus, vea la sección [Pasos siguientes](#next-steps).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Creación de una cola
El objeto **Azure::ServiceBusService** le permite trabajar con colas. Para crear una cola, use el método `create_queue()`. En el siguiente ejemplo se crea una cola o se imprime el error.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

También puede pasar un objeto **Azure::ServiceBus::Queue** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Envío de mensajes a una cola
Para enviar un mensaje a una cola de Service Bus, la aplicación debe llamar al método `send_queue_message()` del objeto **Azure::ServiceBusService**. Los mensajes enviados a las colas de Service Bus (y recibidos de ellas) son objetos **Azure::ServiceBus::BrokeredMessage**, y tienen un conjunto de propiedades estándar (como `label` y `time_to_live`), un diccionario que se usa para mantener las propiedades específicas de la aplicación personalizadas y un conjunto de datos arbitrarios de la aplicación. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena como mensaje, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

En el ejemplo siguiente se muestra cómo enviar un mensaje de prueba a la cola `test-queue` mediante `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

El tamaño máximo de mensaje que admiten las colas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Recepción de mensajes de una cola
Los mensajes se reciben de una cola utilizando el método `receive_queue_message()` del objeto **Azure::ServiceBusService**. De forma predeterminada, los mensajes se leen y bloquean sin que se eliminen de la cola. Sin embargo, puede eliminar mensajes de la cola a medida que se leen estableciendo la opción `:peek_lock` en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que también hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método `delete_queue_message()` y facilitando el mensaje que se va a eliminar a modo de parámetro. El método `delete_queue_message()` marcará el mensaje como consumido y lo eliminará de la cola.

Si el parámetro `:peek_lock` se establece en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como Service Bus ha marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el ejemplo siguiente se muestra cómo recibir y procesar mensajes mediante `receive_queue_message()`. El ejemplo primero recibe y elimina un mensaje mediante `:peek_lock` establecido en **false**; después, recibe otro mensaje y luego elimina el mensaje mediante `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer
El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método `unlock_queue_message()` del objeto **Azure::ServiceBusService**. Esta llamada hará que Service Bus desbloquee el mensaje de la cola y esté disponible para volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método `delete_queue_message()`, entonces el mensaje se vuelve a entregar a la aplicación cuando esta se reinicie. Este proceso habitualmente se denomina *Al menos un procesamiento*, es decir, cada mensaje se procesará al menos una vez; sin embargo, en determinadas situaciones, podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue con la propiedad `message_id` del mensaje, que permanece constante en todos los intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos de las colas del Bus de servicio, siga estos vínculos para obtener más información.

* Información general de [colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md).
* Visite el repositorio de [SDK de Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.

Para ver una comparación entre las colas de Azure Service Bus de este artículo y el servicio de colas de Azure que se describe en el artículo [Uso del almacenamiento de colas de Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md), vea [Colas de Service Bus y colas de Azure: comparación y diferencias](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

