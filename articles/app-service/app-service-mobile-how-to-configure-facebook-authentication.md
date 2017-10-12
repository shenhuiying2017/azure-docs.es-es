---
title: "Configuración de la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones"
description: "Obtenga información acerca de cómo configurar la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: c1b4c91d384c56c4f55bf8d31ced250f51c0d837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Facebook como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com].

## <a name="register"></a>Registro de la aplicación con Facebook
1. Inicie sesión en el [Portal de Azure]y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Facebook.
2. En otra ventana del explorador, navegue hasta el sitio web de [Desarrolladores de Facebook] e inicie sesión con las credenciales de su cuenta de Facebook.
3. (Opcional) Si aún no se ha registrado, haga clic en **Apps** (Aplicaciones) > **Register as a Developer** (Registrarse como desarrollador), acepte la directiva y siga los pasos del registro.
4. Haga clic en **My Apps** (Mis aplicaciones) > **Add a New App** (Agregar una nueva aplicación) > **Website** (Sitio web) > **Skip and Create App ID** (Omitir y crear id. de aplicación). 
5. En **Display Name** (Nombre para mostrar), escriba un nombre único para la aplicación, especifique el **correo electrónico de contacto**, elija una **categoría** para la aplicación, haga clic en **Create App ID** (Crear id. de aplicación) y complete la comprobación de seguridad. Esto le llevará al panel del desarrollador de la nueva aplicación de Facebook.
6. En "Inicio de sesión de Facebook", haga clic en **Comenzar**. Agregue el **URI de redirección** de la aplicación a **Valid OAuth redirect URIs** (URI de redirección de OAuth válidos) y haga clic en **Save Changes** (Guardar cambios). 
   
   > [!NOTE]
   > El URI de redirección es la dirección URL de la aplicación anexada con la ruta de acceso, */.auth/login/facebook/callback*. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Asegúrese de que está utilizando el esquema HTTPS.
   > 
   > 
7. En el panel de navegación izquierdo, haga clic en **Configuración**. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**. Más adelante los usará para configurar la aplicación en Azure.
   
   > [!IMPORTANT]
   > El secreto de aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.
   > 
   > 
8. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **App Review** (Revisión de aplicaciones) y habilite **Make <your-app-name> public** (Hacer pública <nombre-de-su-aplicación>) para habilitar el acceso público general mediante la autenticación de Facebook.

## <a name="secrets"></a>Agregar información de Facebook a la aplicación
1. Vuelva al [Portal de Azure]y vaya a la aplicación. Haga clic en **Configuración** > **Autenticación/autorización** y asegúrese de que **Autenticación de App Service** está **Activada**.
2. Haga clic en **Facebook**, pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente, opcionalmente habilite los ámbitos que requiera la aplicación y haga clic en **Aceptar**.
   
    ![][0]
   
    De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
3. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Facebook, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Facebook**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Facebook para la autenticación.
4. Cuando termine de configurar la autenticación, haga clic en **Guardar**.

De este modo ya estará listo para usar Facebook para realizar la autenticación en la aplicación.

## <a name="related-content"></a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Azure]: https://portal.azure.com/
