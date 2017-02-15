---
title: "Adición de autenticación en Android con Mobile Apps | Microsoft Docs"
description: "Obtenga información sobre cómo usar la característica Mobile Apps de Azure App Service para autenticar usuarios de su aplicación Android a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 817626dd3fc87db61280075b80cedf8b9ed77684
ms.openlocfilehash: e638495c10742388804e75f3277c50cf1e20c6a6


---
# <a name="add-authentication-to-your-android-app"></a>Agregar autenticación a su aplicación de Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumen
En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Android con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Mobile Apps] , que debe completar primero.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Registro de la aplicación para la autenticación y configuración de Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* En Android Studio, abra el proyecto que ha completado con el tutorial [Introducción a Mobile Apps]. En el menú **Run** (Ejecutar), haga clic en **Run app** (Ejecutar aplicación). A continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

     Esta excepción produce porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualice la aplicación para autenticar usuarios antes de solicitar recursos del back-end de Mobile Apps. 

## <a name="add-authentication-to-the-app"></a>Incorporación de autenticación a la aplicación
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Incorporación de notificaciones push a la aplicación de Android](app-service-mobile-android-get-started-push.md)
  Aprenda a configurar su back-end de Mobile Apps para usar Azure Notification Hubs para enviar notificaciones de inserción.
* [Habilitación de la sincronización sin conexión para la aplicación móvil de Android](app-service-mobile-android-get-started-offline-data.md)
  Aprenda a agregar compatibilidad sin conexión a una aplicación con un back-end de Mobile Apps. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;aun cuando no haya conexión de red.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introducción a Mobile Apps]: app-service-mobile-android-get-started.md



<!--HONumber=Dec16_HO2-->


