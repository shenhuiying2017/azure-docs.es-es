---
title: "Envío de notificaciones programas | Microsoft Docs"
description: "En este tema se describe cómo usar Notificaciones programadas con Centros de notificaciones de Azure."
services: notification-hubs
documentationcenter: .net
keywords: "notificaciones push, notificación push, programación de notificaciones push"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Envío de notificaciones programadas
## <a name="overview"></a>Información general
Si tiene un escenario en el que desea enviar una notificación en algún momento del futuro, pero no tiene una forma sencilla de activar el código de back-end para enviar la notificación. Los Centros de notificaciones de nivel estándar admiten una característica que le permite programar notificaciones para hasta 7 días en el futuro.

Cuando envíe una notificación, simplemente use la clase [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) en los SDK de Centros de notificaciones, tal como se muestra en el ejemplo siguiente:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Además, puede cancelar una notificación programada anteriormente con su notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

No hay límite de notificaciones programadas que puede enviar.

