---
title: Envío de notificaciones programas | Microsoft Docs
description: En este tema se describe cómo usar Notificaciones programadas con Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
keywords: notificaciones push, notificación push, programación de notificaciones push
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776984"
---
# <a name="how-to-send-scheduled-notifications"></a>Envío de notificaciones programadas
## <a name="overview"></a>Información general
Si tiene un escenario en el que desea enviar una notificación en algún momento del futuro, pero no tiene una forma sencilla de activar el código de back-end para enviar la notificación. Notification Hubs de nivel estándar admite una característica que le permite programar notificaciones para hasta siete días en el futuro.

Cuando envíe una notificación, simplemente use la clase [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) en los SDK de Notification Hubs, tal como se muestra en el ejemplo siguiente:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Además, puede cancelar una notificación programada anteriormente con su notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

No hay límite de notificaciones programadas que puede enviar.

