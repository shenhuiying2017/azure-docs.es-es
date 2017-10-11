---
title: Expresiones de etiqueta y enrutamiento
description: En este tema se explican las expresiones de etiqueta y enrutamiento de los Centros de notificaciones de Azure.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 18faa88641623e1248d6a33bc2d87099e1c9f624
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="routing-and-tag-expressions"></a>Expresiones de etiqueta y enrutamiento
## <a name="overview"></a>Información general
Las expresiones de etiqueta le permiten dirigirse a conjuntos específicos de dispositivos, o más específicamente a registros, al enviar una notificación push a través de Centros de notificaciones.

## <a name="targeting-specific-registrations"></a>Selección del destino de registros específicos
La única forma de seleccionar el destino de registros de notificaciones específicos es asociar etiquetas con ellos y, a continuación, seleccionar el destino de esas etiquetas. Como se describe en [Administración de registros](notification-hubs-push-notification-registration-management.md), con el fin de recibir notificaciones de inserción, una aplicación tiene que registrar un identificador de dispositivo en un centro de notificaciones. Una vez que se crea un registro en un centro de notificaciones, el back-end de la aplicación puede enviar notificaciones de inserción a este.
El back-end de la aplicación puede elegir a qué registros dirigirse con una notificación específica de las maneras siguientes:

1. **Difusión**: todos los registros del centro de notificaciones reciben la notificación.
2. **Etiquetar**: todos los registros que contienen la etiqueta especificada reciben la notificación.
3. **Expresión de etiqueta**: todos los registros cuyo conjunto de etiquetas coincide con la expresión especificada reciben la notificación.

## <a name="tags"></a>Etiquetas
Una etiqueta puede ser cualquier cadena, hasta 120 caracteres, que contiene y los siguientes caracteres no alfanuméricos: '_', ' @', '#', '. ',':', '-'. En el ejemplo siguiente se muestra una aplicación desde la que puede recibir notificaciones del sistema sobre grupos musicales específicos. En este escenario, una manera sencilla de enrutar notificaciones es etiquetar los registros con etiquetas que representan las distintas bandas, como se muestra en la siguiente imagen.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

En esta imagen, el mensaje etiquetado **Beatles** llega solamente a la tableta registrada con la etiqueta **Beatles**.

Para obtener más información acerca de la creación de registros de etiquetas, consulte [Administración de registros](notification-hubs-push-notification-registration-management.md).

Puede enviar notificaciones a etiquetas mediante los métodos de las notificaciones de envío de la clase `Microsoft.Azure.NotificationHubs.NotificationHubClient` en el SDK de [Centros de notificaciones de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . También puede usar Node.js o las API de REST de notificaciones de inserción.  A continuación se facilita un ejemplo mediante el uso del SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Las etiquetas no tienen que aprovisionarse previamente y pueden hacer referencia a varios conceptos específicos de la aplicación. Por ejemplo, los usuarios de esta aplicación de ejemplo pueden comentar sobre bandas y desean recibir notificaciones del sistema, no solo de los comentarios sobre sus bandas preferidas, sino también de todos los comentarios de sus amigos, independientemente de la banda sobre la que estén comentando. En la siguiente imagen se muestra un ejemplo de este escenario:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

En esta imagen, Ana está interesada en recibir actualizaciones sobre los Beatles y Pedro está interesado en las actualizaciones sobre los Wailers. Bob también está interesado en los comentarios de Juan Carlos, y Juan Carlos está interesado en los Wailers. Cuando se envía una notificación sobre el comentario de Juan Carlos acerca de los Beatles, Ana y Pedro la reciben.

Aunque se pueden codificar varios intereses en etiquetas (por ejemplo, "banda_Beatles" o "sigue_Juan Carlos"), las etiquetas son cadenas simples y no propiedades con valores. Los registros coinciden solo con la presencia o ausencia de una etiqueta específica.

Para obtener un tutorial completo detallado sobre cómo usar etiquetas para enviar a grupos de interés, consulte [Noticias de última hora](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Uso de etiquetas para los usuarios de destino
Otra forma de usar etiquetas es identificar todos los dispositivos de un usuario concreto. Los registros se pueden etiquetar con una etiqueta que contenga un identificador de usuario, como se muestra en la siguiente imagen:

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

En esta imagen, el mensaje etiquetado mediante uid:Ana llega a todos los registros etiquetados como uid:Ana; por lo tanto, a todos los dispositivos de Ana.

## <a name="tag-expressions"></a>Expresiones de etiqueta
Hay casos en que una notificación tiene como destino un conjunto de registros identificado no por una sola etiqueta, sino por una expresión booleana en las etiquetas.

Tome una aplicación de deportes que envía un recordatorio a todas las personas de Boston sobre un partido entre los Red Sox y los Cardinals. Si la aplicación cliente registra etiquetas sobre intereses en equipos y ubicaciones, la notificación deberá dirigirse a todas las personas de Boston interesadas en los Red Sox o en los Cardinals. Esta condición se puede expresar mediante la siguiente expresión booleana:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Las expresiones de etiqueta pueden contener todos los operadores booleanos, por ejemplo, AND (& &), OR (||) y NOT (!). También pueden contener paréntesis. Las expresiones de etiqueta se limita a 20 etiquetas si contienen solamente OR; de lo contrario, se limitan a 6 etiquetas.

A continuación se muestra un ejemplo de envío de notificaciones con expresiones de etiqueta mediante el SDK.

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)";    

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
