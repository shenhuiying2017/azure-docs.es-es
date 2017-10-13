---
title: Utilizar emoticonos Emoji dentro de Azure Mobile Engagement
description: Uso de emoticonos Emoji dentro de las notificaciones push
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Uso de emoticonos Emoji dentro de notificaciones push
Puede incluir emoticonos Emoji en las notificaciones de inserción en unos cuantos pasos sencillos. 

1. En primer lugar, debe encontrar el Emoji que desea enviar en el mensaje. Asegúrese de que el Emoji que está seleccionando se admitirá en el dispositivo de destino, ya que los fabricantes de dispositivos tardan algún tiempo en agregar Emojis aprobados recientemente a las plataformas de dispositivos. 
2. En **Windows** puede navegar a esta [vínculo](http://apps.timwhitlock.info/emoji/tables/unicode) y copiar el icono “Native”.
   
    ![][7] 
3. En **Mac** puede encontrar los Emojis en la aplicación del diccionario en Edición -> Emoji y símbolos.
   
    ![][6] 
4. Ahora vaya a la pestaña **Reach** en el portal de Azure Mobile Engagement. Seleccione el tipo de notificación push (anuncio, encuestas, etc.). En este ejemplo, elegimos una inserción de anuncio.
5. Especifique los distintos campos de la notificación hasta que llegue al texto de la notificación. Aquí es donde agregará el emoticono Emoji. Puede colocarlo en el título, en el mensaje o en ambos. Debe arrastrar y soltar o copiar el Emoji que encontrará en las ubicaciones anteriores. 
   
    ![][1]
6. Complete los demás campos de la notificación y guárdela. 
7. Al ejecutar una prueba o activar el anuncio, verá una notificación con el emoticono tal y como lo especificó.   
   
    ![][3]![][4]![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

