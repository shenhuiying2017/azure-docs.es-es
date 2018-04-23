---
title: Personalización de la autenticación y autorización en Azure App Service | Microsoft Docs
description: Muestra cómo personalizar la autenticación y autorización en App Service y obtener las notificaciones de usuario y los diferentes tokens.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: ed87eab6250eea753bc3b0abd97182d1b869420c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Personalización de la autenticación y autorización en Azure App Service

En este artículo se muestra cómo personalizar la [autenticación y autorización en App Service](app-service-authentication-overview.md) y cómo administrar la identidad desde la aplicación. 

Para comenzar inmediatamente, consulte uno de los siguientes tutoriales:

* [Tutorial: Autenticación y autorización de usuarios de extremo a extremo en Azure App Service](app-service-web-tutorial-auth-aad.md)
* [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Configuración de la aplicación para usar el inicio de sesión de Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Configuración de la aplicación para usar el inicio de sesión de Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Configuración de la aplicación para usar el inicio de sesión de Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Configuración de varias opciones de inicio de sesión

La configuración del portal no ofrece una manera preparada para presentar varias opciones de inicio de sesión a los usuarios (por ejemplo, Facebook y Twitter). Sin embargo, no es difícil agregar la funcionalidad a la aplicación web. A continuación se describen los pasos necesarios:

Primero, en la página **Autenticación / Autorización** de Azure Portal, configure cada uno de los proveedores de identidades que desea habilitar.

En **Action to take when request is not authenticated** (Acción necesaria cuando la solicitud no está autenticada), seleccione **Allow Anonymous requests (no action)** (Permitir solicitudes anónimas (sin acción)).

En la página de inicio de sesión, en la barra de navegación o en cualquier otra ubicación de la aplicación web, agregue un vínculo de inicio de sesión a cada uno de los proveedores que se ha habilitado (`/.auth/login/<provider>`). Por ejemplo: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Cuando el usuario haga clic en uno de los vínculos, la página de inicio de sesión correspondiente se abrirá para que el usuario inicie sesión.

## <a name="access-user-claims"></a>Acceso a las notificaciones de usuario

App Service pasa las notificaciones de usuario a la aplicación mediante encabezados especiales. Las solicitudes externas no están autorizadas para establecer estos encabezados, por lo que solo están presentes si App Service los establece. A continuación puede ver algunos encabezados de ejemplo:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

El código escrito en cualquier lenguaje o plataforma puede obtener la información que necesita de estos encabezados. Para las aplicaciones de ASP.NET 4.6, **ClaimsPrincipal** se establece automáticamente con los valores adecuados.

La aplicación también puede obtener detalles adicionales sobre el usuario autenticado mediante una llamada a `/.auth/me`. Los SDK del servidor de Mobile Apps proporcionan métodos auxiliares para trabajar con estos datos. Para más información, consulte [Uso del SDK de Node.js de Azure Mobile Apps](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) y [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperación de los tokens en el código de aplicación

Desde el código de servidor, los tokens específicos del proveedor se inyectan en el encabezado de solicitud, por lo que puede acceder a ellos fácilmente. En la siguiente tabla se muestran los posibles nombres de encabezado de token:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token de Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Cuenta Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Desde el código de cliente (por ejemplo, una aplicación móvil o JavaScript en el explorador), envíe la solicitud `GET` HTTP a `/.auth/me`. El JSON devuelto tiene los tokens específicos del proveedor.

> [!NOTE]
> Los tokens de acceso son para acceder a recursos de proveedor, por lo que solo están presentes si se configura el proveedor con un secreto de cliente. Para ver cómo obtener tokens de actualización, vea [Tokens de acceso de actualización](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Tokens de acceso de actualización

Cuando el token de acceso del proveedor expira, debe volver a autenticar el usuario. Puede evitar la expiración del token mediante la realización de una llamada `GET` al punto de conexión `/.auth/refresh` de la aplicación. Cuando se llama, App Service actualiza automáticamente los tokens de acceso en el almacén de tokens para el usuario autenticado. Las solicitudes posteriores para los tokens por código de aplicación obtienen los tokens actualizados. Sin embargo, para que la actualización de token funcione, el almacén de tokens debe contener [tokens de actualización](https://auth0.com/learn/refresh-tokens/) para el proveedor. Cada proveedor documenta la manera de obtener tokens de actualización, pero en la lista siguiente se muestra un breve resumen:

- **Google**: anexe un parámetro de cadena de consulta `access_type=offline` en su llamada API `/.auth/login/google`. Si usa el SDK de Mobile Apps, puede agregar el parámetro a una de las sobrecargas `LogicAsync` (vea [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens) (Tokens de actualización de Google)).
- **Facebook**: no proporciona tokens de actualización. Los tokens de larga duración expiran en 60 días (vea [Facebook Expiration and Extension of Access Tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension) (Expiración y extensión de tokens de acceso de Facebook)).
- **Twitter**: los tokens de acceso no expiran (vea [Twitter OAuth FAQ](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq) (Preguntas más frecuentes sobre Twitter OAuth)).
- **Cuenta Microsoft**: cuando [defina la configuración de autenticación de Cuenta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), seleccione el ámbito `wl.offline_access`.
- **Azure Active Directory**: en [https://resources.azure.com](https://resources.azure.com), realice los pasos siguientes:
    1. En la parte superior de la página, seleccione **Lectura y escritura**.
    1. En el explorador de la izquierda, desplácese hasta **subscriptions** > **_\<nombre\_suscripción_** > **resourceGroups** > _**\<nombre\_grupo\_recursos>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<nombre\_aplicación>**_ > **config** > **authsettings**. 
    1. Haga clic en **Editar**.
    1. Modifique la siguiente propiedad. Reemplace _\<app\_id>_ por el identificador de aplicación de Azure Active Directory del servicio al que desea acceder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Haga clic en **Put**. 

Una vez configurado el proveedor, puede ver si los tokens de actualización se encuentran en el almacén de tokens mediante una llamada a `/.auth/me`. 

Para actualizar el token de acceso en cualquier momento, simplemente llame a `/.auth/refresh` en cualquier lenguaje. El fragmento de código siguiente utiliza jQuery para actualizar los tokens de acceso de un cliente de JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Si un usuario revoca los permisos concedidos a la aplicación, la llamada a `/.auth/me` puede producir un error con un respuesta `403 Forbidden`. Para diagnosticar errores, compruebe los registros de aplicación para más detalles.

## <a name="extend-session-expiration-grace-period"></a>Ampliación del período de gracia de expiración de sesión

Después de que expire una sesión autenticada, hay un período de gracia de 72 horas de forma predeterminada. Dentro de este período de gracia, puede actualizar la cookie de sesión o el token de sesión con App Service sin volver a autenticar el usuario. Simplemente puede llamar a `/.auth/refresh` cuando la cookie de sesión o el token de sesión deje de ser válido y no es necesario que realice un seguimiento usted mismo de la expiración del token. Una vez que transcurra el período de gracia de 72 horas, el usuario debe iniciar sesión de nuevo para obtener una cookie de sesión o un token de sesión válido.

Si 72 horas no es tiempo suficiente para usted, puede ampliar este período de expiración. Extender la expiración durante un período prolongado podría tener implicaciones de seguridad importantes (por ejemplo, cuando un token de autenticación se pierde o roba). Por tanto, debe dejarla en el valor predeterminado de 72 horas o establecer el período de extensión en el valor más pequeño.

Para extender el período de expiración predeterminado, ejecute el siguiente comando en [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> El período de gracia solo se aplica a la sesión autenticada de App Service, no a los tokens de los proveedores de identidades. No hay ningún período de gracia para los tokens del proveedor expirados. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitación del dominio de cuentas de inicio de sesión

Cuenta Microsoft y Azure Active Directory le permiten iniciar sesión desde varios dominios. Por ejemplo, Cuenta Microsoft permite cuentas de _outlook.com_, _live.com_ y _hotmail.com_. Azure Active Directory permite cualquier número de dominios personalizados para las cuentas de inicio de sesión. Este comportamiento puede no ser deseable para una aplicación interna, a la que no desea que cualquier persona con una cuenta de _outlook.com_ acceda. Para limitar el nombre de dominio de las cuentas de inicio de sesión, siga estos pasos.

En [https://resources.azure.com](https://resources.azure.com), desplácese hasta **subscriptions** > **_\<nombre\_suscripción_** > **resourceGroups** > _**\<nombre\_grupo\_recursos>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<nombre\_aplicación>**_ > **config** > **authsettings**. 

Haga clic en **Editar**, modifique la propiedad siguiente y luego haga clic en **Put**. No olvide reemplazar _\<domain\_name>_ por el dominio que desee.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](app-service-web-tutorial-auth-aad.md)
