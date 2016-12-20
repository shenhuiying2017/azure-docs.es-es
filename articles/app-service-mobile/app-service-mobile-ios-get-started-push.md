---
title: "Incorporación de notificaciones push a la aplicación iOS con las Aplicaciones móviles de Azure"
description: "Obtenga información acerca de cómo usar las Aplicaciones móviles de Azure para enviar notificaciones push a su aplicación iOS."
services: app-service\mobile
documentationcenter: ios
manager: yochayk
editor: 
author: ysxu
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a21ae4b13479f66ada053153834a2b3cc5687e5a


---
# <a name="add-push-notifications-to-your-ios-app"></a>Incorporación de notificaciones push a la aplicación iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregará notificaciones de inserción al proyecto de [inicio rápido de iOS] para que cada vez que se envíe una notificación de inserción al dispositivo, se inserte un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesitará el paquete de extensión de la notificación de inserción. Vea [Trabajar con el SDK de servidor de back-end de .NET para Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obtener más información.

El [simulador de iOS no admite notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Necesita un dispositivo de iOS físico y una [suscripción de Apple Developer Program](https://developer.apple.com/programs/ios/).

## <a name="a-nameconfigure-hubaconfigure-notification-hub"></a><a name="configure-hub"></a>Configurar el Centro de notificaciones
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraregister-app-for-push-notifications"></a><a id="register"></a>Registro de aplicaciones para notificaciones push
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configuración de Azure para enviar notificaciones push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="a-idupdate-serveraupdate-backend-to-send-push-notifications"></a><a id="update-server"></a>Actualizar el back-end para enviar notificaciones de inserción
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="a-idadd-pushaadd-push-notifications-to-app"></a><a id="add-push"></a>Agregar notificaciones push a la aplicación
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="a-idtestatest-push-notifications"></a><a id="test"></a>Probar notificaciones de inserción
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="a-idmoreamore"></a><a id="more"></a>Más
* Las plantillas proporcionan flexibilidad para enviar inserciones multiplataforma e inserciones localizadas. [Uso de la biblioteca de cliente de iOS para Aplicaciones móviles de Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) muestra cómo registrar plantillas.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[inicio rápido de iOS]: app-service-mobile-ios-get-started.md



<!--HONumber=Nov16_HO3-->


