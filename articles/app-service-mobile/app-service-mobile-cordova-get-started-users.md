---
title: "Adición de autenticación en Apache Cordova con Mobile Apps | Microsoft Docs"
description: "Obtenga información sobre cómo usar Aplicaciones móviles en el Servicio de aplicaciones de Azure para autenticar usuarios de su aplicación de Apache Cordova a través de una variedad de proveedores de identidades, incluidos Google, Facebook, Twitter y Microsoft."
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: b7362b7f26859de541f792e714502851d74c98e5
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Agregar autenticación a su aplicación de Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumen
En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Apache Cordova con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Mobile Apps] , que debe completar primero.

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Visualización de un vídeo donde se muestren pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Ahora, puede comprobar que se deshabilitó el acceso anónimo a su back-end. En Visual Studio:

* Abra el proyecto que ha creado al completar el tutorial [Introducción a Mobile Apps].
* Ejecute la aplicación en el **emulador de Google Android**.
* Compruebe que se muestra un error de conexión inesperado después de iniciarse la aplicación.

A continuación, actualice la aplicación para autenticar usuarios antes de solicitar recursos del back-end de Mobile Apps.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación
1. Abra el proyecto en **Visual Studio** y, después, abra el archivo `www/index.html` para editarlo.
2. Busque la etiqueta META `Content-Security-Policy` en la sección de encabezado.  Agregue el host de OAuth a la lista de orígenes permitidos.

   | Proveedor | Nombre del proveedor del SDK | Host de OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    A continuación se muestra un ejemplo de Content-Security-Policy (implementado para Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Reemplace `https://login.microsoftonline.com` por el host de OAuth de la tabla anterior.  Para obtener más información sobre la etiqueta de metadatos de lContent-Security-Policy, consulte la [documentación de Content-Security-Policy].

    Algunos proveedores de autenticación no requieren cambios en Content-Security-Policy cuando se usa en dispositivos móviles adecuados.  Por ejemplo, no se requiere ningún cambio en Content-Security-Policy cuando se usa la autenticación de Google en un dispositivo Android.

3. Abra el archivo `www/js/index.js` para editarlo, busque el método `onDeviceReady()` y, en el código de creación del cliente, agregue el siguiente código:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Este código reemplaza el código existente que crea la referencia de tabla y actualiza la interfaz de usuario.

    El método login() inicia la autenticación con el proveedor. El método login() es una función asincrónica que devuelve una promesa de JavaScript.  El resto de la inicialización se coloca dentro de la respuesta de la promesa para que no se ejecute hasta que se complete el método login().

4. En el código que acaba de agregar, reemplace `SDK_Provider_Name` por el nombre de su proveedor de inicio de sesión. Por ejemplo, para Azure Active Directory, use `client.login('aad')`.
5. Ejecute el proyecto.  Cuando el proyecto acabe de inicializarse, la aplicación mostrará la página de inicio de sesión de OAuth del proveedor de autenticación seleccionado.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información [sobre la autenticación] con el Servicio de aplicaciones de Azure.
* Prosiga el tutorial agregando [notificaciones push] a la aplicación de Apache Cordova.

Obtenga información sobre cómo usar los SDK.

* [SDK de Apache Cordova]
* [SDK de servidor ASP.NET]
* [SDK de servidor Node.js]

<!-- URLs. -->
[Introducción a Mobile Apps]: app-service-mobile-cordova-get-started.md
[documentación de Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[notificaciones push]: app-service-mobile-cordova-get-started-push.md
[sobre la autenticación]: app-service-mobile-auth.md
[SDK de Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK de servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK de servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
