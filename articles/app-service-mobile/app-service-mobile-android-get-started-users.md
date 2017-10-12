---
title: "Adición de autenticación en Android con Mobile Apps | Microsoft Docs"
description: "Obtenga información sobre cómo usar la característica Mobile Apps de Azure App Service para autenticar usuarios de su aplicación Android a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 81331142aa6110d4e29e6fb30a90ce6e3a853439
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-android-app"></a>Agregar autenticación a su aplicación de Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumen
En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Android con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Mobile Apps] , que debe completar primero.

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adición de la aplicación a las direcciones URL de redirección externa permitidas

La autenticación segura requiere que se defina un nuevo esquema de dirección URL para la aplicación. Esto permite que el sistema de autenticación se redirija a la aplicación una vez completado el proceso de autenticación. En este tutorial, se usará el esquema de dirección URL _appname_. Sin embargo, puede utilizar cualquier otro esquema de dirección URL que elija. Debe ser único para la aplicación móvil. Para habilitar la redirección en el lado de servidor:

1. En [Azure Portal], seleccione App Service.

2. Haga clic en la opción de menú **Autenticación/autorización**.

3. En **URL de redirección externas permitidas**, introduzca `appname://easyauth.callback`.  El valor de _appname_ de esta cadena es el esquema de dirección URL para la aplicación móvil.  Debe seguir la especificación normal de las direcciones URL para un protocolo (usar únicamente letras y números, y comenzar por una letra).  Debe tomar nota de la cadena que elija ya que necesitará ajustar el código de la aplicación móvil con el esquema de direcciones URL en varios sitios.

4. Haga clic en **Aceptar**.

5. Haga clic en **Save**.

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* En Android Studio, abra el proyecto que ha completado con el tutorial [Introducción a Mobile Apps]. En el menú **Run** (Ejecutar), haga clic en **Run app** (Ejecutar aplicación). A continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

     Esta excepción produce porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualice la aplicación para autenticar usuarios antes de solicitar recursos del back-end de Mobile Apps. 

## <a name="add-authentication-to-the-app"></a>Incorporación de autenticación a la aplicación
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente
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
