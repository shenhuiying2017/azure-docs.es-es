<properties
	pageTitle="Envío de notificaciones programas | Microsoft Azure"
	description="En este tema se describe cómo usar Notificaciones programadas con Centros de notificaciones de Azure."
	services="notification-hubs"
	documentationCenter=".net"
	keywords="notificaciones push, notificación push, programación de notificaciones push"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="wesmc"/>

# Envío de notificaciones programadas


##Información general

Si tiene un escenario en el que desea enviar una notificación en algún momento del futuro, pero no tiene una forma sencilla de activar el código de back-end para enviar la notificación. Los Centros de notificaciones de nivel estándar admiten una característica que le permite programar notificaciones para hasta 7 días en el futuro.

Cuando envíe una notificación, simplemente use la clase [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) en los SDK de Centros de notificaciones, tal como se muestra en el ejemplo siguiente:

	Notification notification = new AppleNotification("{"aps":{"alert":"Happy birthday!"}}");
	var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Además, puede cancelar una notificación programada anteriormente con su notificationId:

	await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

No hay límite de notificaciones programadas que puede enviar.

<!---HONumber=AcomDC_0622_2016-->