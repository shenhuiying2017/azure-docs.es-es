---
title: Filtros de tema de Azure Service Bus | Microsoft Docs
description: Filtros de temas de Azure Service Bus
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: sethm
ms.openlocfilehash: b3fe467b7d6ae9b207956ece4980bf558a69761f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="topic-filters-and-actions"></a>Filtros y acciones de temas

Los suscriptores pueden definir los mensajes que quieren recibir de un tema. Estos mensajes se especifican en forma de una o varias reglas de suscripción con nombre. Cada regla está formada por una condición que selecciona mensajes concretos y una acción que anota el mensaje seleccionado. Con cada condición de regla de coincidencia, la suscripción crea una copia del mensaje, que se puede anotar de manera diferente para cada regla coincidente.

Cada suscripción de tema recién creada tiene una regla de suscripción predeterminada inicial. Si no especifica explícitamente una condición de filtro para la regla, el filtro aplicado es el filtro **true** que permite que se seleccionen todos los mensajes de la suscripción. La regla predeterminada no tiene ninguna acción de anotación asociada.

Service Bus admite tres condiciones de filtro:

-   *Filtros booleanos*: el filtro **TrueFilter** y **FalseFilter** hace que se seleccionen todos los mensajes entrantes (**true**) o ninguno de los mensajes entrantes (**false**) de la suscripción.

-   *Filtros SQL*: una propiedad **SqlFilter** contiene una expresión condicional de tipo SQL que se evalúa en el agente contra las propiedades del sistema y las propiedades definidas por el usuario de los mensajes entrantes. Todas las propiedades del sistema deben ir precedidas de `sys.` en la expresión condicional. El [subconjunto de lenguaje SQL para condiciones de filtro](service-bus-messaging-sql-filter.md) demuestra la existencia de propiedades (EXISTS), así como de valores null (IS NULL), operadores lógicos NOT/AND/OR, operadores relacionales, aritmética numérica simple y coincidencia de patrones de texto con LIKE.

-   *Filtros de correlación*: una propiedad **CorrelationFilter** contiene un conjunto de condiciones para las que se busca la coincidencia con una o varias de las propiedades del usuario y del sistema de un mensaje entrante. Un uso común es buscar la coincidencia con la propiedad **CorrelationId**, pero la aplicación puede elegir también buscarla con **ContentType**, **Label**, **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **To** y cualquier propiedad definida por el usuario. Existe una coincidencia cuando el valor de un mensaje entrante de una propiedad es igual al valor especificado en el filtro de correlación. En las expresiones de cadena, la comparación distingue mayúsculas de minúsculas. Al especificar varias propiedades de coincidencia, el filtro las combina como una condición AND lógica, lo que significa que para que el filtro realice la coincidencia, todas las condiciones deben coincidir.

Todos los filtros evalúan propiedades del mensaje. Los filtros no pueden evaluar el cuerpo del mensaje.

Las reglas de filtro complejas requieren capacidad de procesamiento. En concreto, el uso de reglas de filtro SQL da como resultado un menor rendimiento general del mensaje en el nivel de suscripción, tema y espacio de nombres. Siempre que sea posible, las aplicaciones deben elegir filtros de correlación por encima de filtros de tipo SQL, ya que son mucho más eficaces en el procesamiento y, por tanto, tienen un impacto menor en el rendimiento.

## <a name="actions"></a>Acciones

Con las condiciones de filtro SQL y solo con ellas, puede definir una acción que puede anotar el mensaje mediante la adición, eliminación o sustitución de propiedades y sus valores. La acción [usa una expresión de tipo SQL](service-bus-messaging-sql-filter.md) que se basa ligeramente en la sintaxis de la instrucción UPDATE de SQL. La acción se realiza en el mensaje después de que se ha correspondido y antes de que el mensaje se seleccione en el tema. Los cambios en las propiedades del mensaje son privados para el mensaje copiado en la suscripción.

## <a name="usage-patterns"></a>Patrones de uso

El escenario de uso más sencillo de un tema es que cada suscripción obtenga una copia de cada mensaje enviado a un tema, lo que permite un patrón de difusión.

Los filtros y las acciones permiten dos grupos adicionales de patrones: creación de particiones y enrutamiento.

En la creación de particiones se usan filtros para distribuir los mensajes entre varias suscripciones a temas existentes de una manera predecible y mutuamente excluyente. El patrón de creación de particiones se usa cuando un sistema se escala horizontalmente para controlar muchos contextos diferentes en compartimientos funcionalmente idénticos que contiene cada uno de ellos un subconjunto de los datos generales; por ejemplo, información del perfil del cliente. Con la creación de particiones, un editor envía el mensaje en un tema sin necesidad de ningún conocimiento del modelo de creación de particiones. A continuación, el mensaje se mueve a la suscripción correcta desde la que el controlador de mensajes de la partición puede recuperarlo.

En el enrutamiento se usan filtros para distribuir los mensajes entre las suscripciones a temas de un modo predecible, pero no necesariamente exclusivo. Junto con la característica de [reenvío automático](service-bus-auto-forwarding.md), pueden usarse filtros de tema para crear gráficos complejos de enrutamiento dentro de un espacio de nombres de Service Bus para la distribución de mensajes dentro de una región de Azure. Con Azure Functions o Azure Logic Apps actuando como puente entre los espacios de nombres de Azure Service Bus, puede crear topologías globales complejas con integración directa en las aplicaciones de línea de negocio.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Service Bus, consulte los siguientes temas:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Sintaxis de SQLFilter](service-bus-messaging-sql-filter.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)