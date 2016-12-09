---
title: "Incorporación de notificaciones push a una aplicación Xamarin Android | Microsoft Docs"
description: "Más información sobre cómo usar Servicios de aplicaciones de Azure y Centros de notificaciones de Azure para enviar notificaciones push a la aplicación de Xamarin Android"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c9f8c46fa5fba1e69382a383ff94700de07db4ab


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Agregar notificaciones push a la aplicación de Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregará notificaciones de inserción al proyecto de [inicio rápido de Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) para que cada vez que se envíe una notificación de inserción al dispositivo, se inserte un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesitará el paquete de extensión de la notificación de inserción. Vea [Trabajar con el SDK de servidor de back-end de .NET para Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obtener más información.

## <a name="prerequisites"></a>Requisitos previos
Este tutorial requiere lo siguiente:

* Una cuenta de Google activa. Puede registrarse para obtener una cuenta de Google en [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Componente del cliente de Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/).

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>Configurar un Centro de notificaciones
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraenable-firebase-cloud-messaging"></a><a id="register"></a>Habilitar la mensajería en la nube Firebase
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configuración de Azure para enviar notificaciones de inserción
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="a-idupdate-serveraupdate-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Actualizar el proyecto de servidor para que envíe notificaciones push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-idconfigure-appaconfigure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Configurar el proyecto cliente para las notificaciones push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="a-idadd-pushaadd-push-notifications-code-to-your-app"></a><a id="add-push"></a>Incorporación de código de notificaciones de inserción a la aplicación
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>Prueba de las notificaciones push en su aplicación
Puede probar la aplicación con un dispositivo virtual en el emulador. Hay pasos de configuración adicionales necesarios cuando se ejecuta en un emulador.

1. Asegúrese de que va a implementar o depurar en un dispositivo virtual que tenga las API de Google como destino, como se muestra a continuación en el administrador de dispositivo virtual Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Agregue una cuenta de Google al dispositivo Android haciendo clic en **Aplicaciones** > **Configuración** > **Agregar cuenta** y luego siga las indicaciones.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Ejecute la aplicación todolist como antes e inserte un nuevo elemento todo. Esta vez, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificaciones para ver el texto completo de la notificación.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Inicio rápido de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Componente del Cliente de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/



<!--HONumber=Nov16_HO3-->


