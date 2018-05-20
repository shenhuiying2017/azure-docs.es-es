---
title: Configuración de la autenticación mediante Google para la aplicación de Servicios de aplicaciones
description: Obtenga información acerca de cómo configurar la autenticación mediante Google para la aplicación de Servicios de aplicaciones.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 1a174913446c0a1d5e3e3b01123db8b40bfd172c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Configuración de la aplicación de App Service para usar el inicio de sesión de Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Azure App Service para usar Google como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Registro de la aplicación con Google
1. Inicie sesión en el [Portal de Azure]y vaya a la aplicación. Copie la **URL**, que se utilizará más adelante para configurar la aplicación de Google.
2. Navegue al sitio web de [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), inicie sesión con las credenciales de su cuenta de Google, haga clic en **Create Project** (Crear proyecto), especifique un **nombre de proyecto** y haga clic en **Create** (Crear).
3. Una vez creado el proyecto, selecciónelo. En el panel del proyecto, haga clic en **Go to APIs overview** (Ir a la información general de las API).
4. Seleccione **Enable APIs and services** (Habilitar API y servicios). Busque la opción **Google+ API** (API de Google+) y selecciónela. A continuación, haga clic en **Enable** (Habilitar).
6. En el panel de navegación izquierdo, haga clic en **Credentials** (Credenciales)  > **OAuth consent screen** (Pantalla de consentimiento de OAuth), seleccione su **dirección de correo electrónico**, escriba un **nombre de producto** y haga clic en **Save** (Guardar).
7. En la pestaña **Credentials** (Credenciales), haga clic en **Create credentials** (Crear credenciales) > **OAuth client ID** (Id. de cliente de OAuth). Haga clic en **Configure consent screen** (Configurar pantalla de consentimiento) y proporcione un **Product name** (Nombre de producto). A continuación, haga clic en **Guardar**
8. En la pantalla "Create client ID" (Crear id. de cliente), seleccione **Aplicación web**.
9. Pegue la **URL** de App Service que copió anteriormente en **Authorized JavaScript Origins** (Orígenes de JavaScript autorizados) y luego pegue el identificador URI de redirección en **Authorized Redirect URI** (URI de redirección autorizado). El URI de redirección es la dirección URL de la aplicación anexada a la ruta de acceso, */.auth/login/google/callback*. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Asegúrese de que está utilizando el esquema HTTPS. A continuación, haga clic en **Crear**.
10. En la siguiente pantalla, tome nota de los valores de id. de cliente y el secreto del cliente.

    > [!IMPORTANT]
    > El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.


## <a name="secrets"></a>Adición de información de Google a la aplicación
1. Vuelva al [Portal de Azure]y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación/autorización**.
2. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.
3. Haga clic en **Google**. Pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo previamente y habilite opcionalmente los ámbitos que requiere la aplicación. A continuación, haga clic en **Aceptar**.
   
   ![][1]
   
   De forma predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
4. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Google, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Google**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Google para la autenticación.
5. Haga clic en **Save**(Guardar).

De este modo ya estará listo para usar Google para realizar la autenticación en la aplicación.

## <a name="related-content"></a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Azure]: https://portal.azure.com/

