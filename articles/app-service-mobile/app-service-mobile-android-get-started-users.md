---
title: "Adición de autenticación en Android con Mobile Apps | Microsoft Docs"
description: "Obtenga información sobre cómo usar Aplicaciones móviles en el Servicio de aplicaciones de Azure para autenticar usuarios de su aplicación Android a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3caf6edb1e3a33a451ec8773b1e14fe0c95734a1


---
# <a name="add-authentication-to-your-android-app"></a>Agregar autenticación a su aplicación de Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumen
En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Android con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Aplicaciones móviles] , que debe completar primero.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* En Android Studio, abra el proyecto que ha completado con el tutorial [Introducción a Aplicaciones móviles]. En el menú **Run** (Ejecutar), haga clic en **Run app** (Ejecutar aplicación). A continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.
  
     Esta excepción produce porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualice la aplicación para autenticar usuarios antes de solicitar recursos del back-end de aplicación móvil.

## <a name="add-authentication-to-the-app"></a>Incorporación de autenticación a la aplicación
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Agregar notificaciones de inserción a la aplicación Android](app-service-mobile-android-get-started-push.md) Aprenda a configurar su back-end de aplicación móvil para usar Azure Notification Hubs para enviar notificaciones de inserción.
* [Habilitar la sincronización sin conexión para su aplicación Android](app-service-mobile-android-get-started-offline-data.md) Obtenga información sobre cómo agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil &mdash;ver, agregar o modificar datos&mdash; aun cuando no haya conexión de red.

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla para los usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenar tokens de autenticación en el cliente]: #cache-tokens
[Actualizar tokens expirados]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introducción a Aplicaciones móviles]: app-service-mobile-android-get-started.md



<!--HONumber=Nov16_HO3-->


