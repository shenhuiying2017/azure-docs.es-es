---
title: Uso de temas de Service Bus (Ruby) | Microsoft Docs
description: "Aprenda a usar los temas y las suscripciones del Bus de servicio de Azure. Los ejemplos de código están escritos para aplicaciones Ruby."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Uso de temas y suscripciones de Service Bus con Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este artículo se describe cómo usar los temas y las suscripciones de Service Bus desde aplicaciones Ruby. Entre los escenarios tratados se incluyen la **creación de temas y suscripciones, la creación de filtros de suscripción, el envío de mensajes** a un tema, la **recepción de mensajes de una suscripción** y la **eliminación de temas y suscripciones**. Para más información sobre los temas y las suscripciones, vea la sección [Pasos siguientes](#next-steps).

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Creación de un tema
El objeto **Azure::ServiceBusService** le permite trabajar con temas. El siguiente código crea un objeto **Azure::ServiceBusService**. Para crear un tema, use el método `create_topic()`. En el siguiente ejemplo se crea un tema o se imprime el error si lo hubiera.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

También puede pasar un objeto **Azure::ServiceBus::Topic** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Creación de suscripciones
Las suscripciones a temas también se crean con el objeto **Azure::ServiceBusService**. A las suscripciones se les asigna un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que se entregan a su cola virtual.

Las suscripciones son permanentes y seguirán existiendo hasta que se eliminen, o se elimine el tema al que están asociadas. Si la aplicación contiene lógica para crear una suscripción, primero debe comprobar si esta ya existe utilizando el método getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creación de una suscripción con el filtro predeterminado (MatchAll)
El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "all-messages" que usa el filtro predeterminado **MatchAll**.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Creación de suscripciones con filtros
También puede definir filtros que le permitan especificar qué mensajes enviados a un tema deben mostrarse dentro de una suscripción a una suscripción determinada.

El tipo de filtro más flexible compatible con las suscripciones es **Azure::ServiceBus::SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre las expresiones que se pueden usar con un filtro de SQL, revise la sintaxis de [SqlFilter](service-bus-messaging-sql-filter.md).

Es posible agregar filtros a una suscripción a través del método `create_rule()` del objeto **Azure::ServiceBusService**. Este método le permite agregar nuevos filtros a una suscripción existente.

Dado que el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe eliminar el filtro predeterminado si no quiere que **MatchAll** anule todos los otros filtros que especifique. Puede eliminar la regla predeterminada utilizando el método `delete_rule()` en el objeto **Azure::ServiceBusService**.

En el ejemplos siguiente se crea una suscripción llamada "high-messages" con **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes que tienen una propiedad `message_number` personalizada superior a 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Del mismo modo, en el ejemplo que aparece a continuación, se crea una suscripción denominada `low-messages` con un filtro **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes cuyo valor de la propiedad `message_number` es menor o igual que 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Cuando ahora se envía un mensaje a `test-topic`, siempre se entrega a los destinatarios que están suscritos al tema `all-messages`, mientras que se entrega de forma selectiva a los que están suscritos a los temas `high-messages` y `low-messages` (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Envío de mensajes a un tema
Para enviar un mensaje a un tema de Service Bus, la aplicación debe utilizar el método `send_topic_message()` en el objeto **Azure::ServiceBusService**. Los mensajes enviados a los temas de Service Bus son instancias de los objetos **Azure::ServiceBus::BrokeredMessage**. Los objetos **Azure::ServiceBus::BrokeredMessage** cuentan con un conjunto de propiedades estándar (como `label` y `time_to_live`), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos de cadenas. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena al método `send_topic_message()`, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

En el ejemplo siguiente se demuestra cómo enviar cinco mensajes de prueba a `test-topic`. Tenga en cuenta que el valor de la propiedad personalizado `message_number` de cada mensaje varía en función de la iteración del bucle (lo que determina qué suscripción lo recibe):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

El tamaño máximo de mensaje que admiten los temas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB).

## <a name="receive-messages-from-a-subscription"></a>Recepción de mensajes de una suscripción
Los mensajes se reciben de una suscripción utilizando el método `receive_subscription_message()` del objeto **Azure::ServiceBusService**. De forma predeterminada, los mensajes se leen (máximo) y bloquean sin que se eliminen de la suscripción. Puede leer y eliminar el mensaje de la suscripción estableciendo la opción `peek_lock` en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que también hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método `delete_subscription_message()` y facilitando el mensaje que se va a eliminar a modo de parámetro. El método `delete_subscription_message()` marcará el mensaje como consumido y lo eliminará de la suscripción.

Si el parámetro `:peek_lock` se establece en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes mediante `receive_subscription_message()`. El ejemplo primero recibe y elimina un mensaje de la suscripción `low-messages` mediante `:peek_lock` establecido en **false**; después, recibe otro mensaje de `high-messages` y, por último, elimina el mensaje mediante `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer
El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método `unlock_subscription_message()` del objeto **Azure::ServiceBusService**. Esto hace que Service Bus desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces Service Bus desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método `delete_subscription_message()`, entonces el mensaje se vuelve a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina *Al menos un procesamiento*, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esta lógica se consigue usando la propiedad `message_id` del mensaje, que permanecerá constante en todos los intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminación de temas y suscripciones
Los temas y las suscripciones son permanentes, por lo que deben eliminarse explícitamente a través del [Azure Portal][Azure portal] o mediante programación. En el ejemplo siguiente se muestra cómo eliminar el tema llamado `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El código siguiente muestra cómo eliminar la suscripción denominada `high-messages` del tema `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos de los temas del Bus de servicio, siga estos vínculos para obtener más información.

* Vea [Colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md).
* Referencia de API para [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Visite el repositorio de [SDK de Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.

[Azure portal]: https://portal.azure.com
