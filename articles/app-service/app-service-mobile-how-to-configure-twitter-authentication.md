---
title: "Configuración de la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones"
description: "Obtenga información acerca de cómo configurar la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: afde020b7817dc58ecea24eb4a09cf93d0986eb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Twitter como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico y un número de teléfono verificados. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"></a>Registro de la aplicación con Twitter
1. Inicie sesión en el [Portal de Azure]y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Twitter.
2. Vaya al sitio web para [desarrolladores de Twitter] , inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Crear nueva aplicación**.
3. Escriba el **nombre** y una **descripción** de la nueva aplicación. Pegue la **dirección URL** de la aplicación como valor de **Sitio web**. Después, en **Dirección URL de devolución de llamadas**, pegue la **dirección URL de devolución de llamadas** que copió anteriormente. Se trata de la puerta de enlace de Mobile App anexada a la ruta de acceso, */.auth/login/twitter/callback*. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Asegúrese de que está utilizando el esquema HTTPS.
4. En la parte inferior de la página, lea y acepte los términos. A continuación, haga clic en **Crear aplicación de Twitter**. De esta forma, la aplicación se registra y se muestran los detalles correspondientes.
5. Haga clic en la pestaña **Configuración**, seleccione **Allow this application to be used to sign in with Twitte** (Permitir que esta aplicación se use para iniciar sesión en Twitter) y, después, haga clic en **Update Settings** (Actualizar configuración).
6. Seleccione la pestaña **Claves y tokens de acceso** . Tome nota de los valores de **Consumer Key (API Key)** [Clave de consumidor (clave de API)] y **Consumer secret (API Secret)** [Secreto de consumidor (secreto de API)].
   
   > [!NOTE]
   > El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.
   > 
   > 

## <a name="secrets"></a>Adición de información de Twitter a su aplicación
1. Vuelva al [Portal de Azure]y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación/autorización**.
2. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.
3. Haga clic en **Twitter**. Pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente. y, a continuación, haga clic en **Aceptar**.
   
   ![][1]
   
   De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
4. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Twitter, establezca **Acción necesaria cuando la solicitud no está autenticada** en **Twitter**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Twitter para la autenticación.
5. Haga clic en **Save**.

De este modo ya estará listo para usar Twitter para realizar la autenticación en la aplicación.

## <a name="related-content"></a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
