---
title: 'Azure Active Directory B2C: solicitud de tokens de acceso | Microsoft Docs'
description: "En este artículo se mostrará cómo configurar una aplicación cliente y adquirir un token de acceso."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 5d15ad7a4e75410390891b5e11f72c6a649ebf53
ms.lasthandoff: 03/23/2017


---


# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: solicitud de tokens de acceso


Un token de acceso (que se indica como **access\_token**) es una forma de token de seguridad que un cliente puede usar para tener acceso a los recursos protegidos por un [servidor de autorización](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), como una API web. Los tokens de acceso se representan como [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) y contienen información sobre el servidor de recursos previsto y los permisos concedidos al servidor. Cuando se llama al servidor de recursos, el token de acceso debe incluirse en la solicitud HTTP.

En este artículo se describe cómo configurar una aplicación cliente y hacer que realice una solicitud para adquirir un **access\_token** desde los puntos de conexión `authorize` y `token`.

## <a name="prerequisite"></a>Requisito previo

Antes de solicitar un token de acceso, debe registrar una API web y publicar los permisos que se pueden conceder a la aplicación cliente. Para comenzar, siga los pasos que se encuentran en la sección [Registro de una API web](active-directory-b2c-app-registration.md).

## <a name="granting-permissions-to-a-web-api"></a>Concesión de permisos a una API web

Para que una aplicación cliente obtenga permisos específicos para una API, se deben conceder esos permisos a la aplicación cliente a través de Azure Portal. Para conceder permisos a una aplicación cliente:

1. Vaya al menú **Aplicaciones** en la hoja de características de B2C.
2. Haga clic en la aplicación cliente ([Registro de una aplicación](active-directory-b2c-app-registration.md) si no tiene una).
3. Seleccione el **acceso API**.
4. Haga clic en **Agregar**.
5. Seleccione la API web y los ámbitos (permisos) que desea conceder.
6. Haga clic en **Aceptar**.

> [!NOTE]
> Azure AD B2C no pide el consentimiento de los usuarios de la aplicación cliente. En lugar de eso, el administrador proporciona todo el consentimiento según los permisos configurados entre las aplicaciones descritas anteriormente. Si se revoca un permiso concedido para una aplicación, todos los usuarios que anteriormente podían adquirir ese permiso ya no podrán hacerlo.

## <a name="requesting-a-token"></a>Solicitud de un token

Para obtener un token de acceso para una aplicación de recursos, la aplicación cliente debe especificar los permisos que desea en el parámetro **scope** de la solicitud. Por ejemplo, para adquirir el permiso de "lectura" para la aplicación de recursos que tiene el URI de id. de aplicación de `https://contoso.onmicrosoft.com/notes`, el ámbito sería `https://contoso.onmicrosoft.com/notes/read`. A continuación se muestra un ejemplo de solicitud de código de autorización al punto de conexión `authorize`.

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Para adquirir varios permisos en la misma solicitud, puede agregar varias entradas en el parámetro **scope** único, separadas por espacios. Por ejemplo:

Dirección URL descodificada:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Dirección URL codificada:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

También puede solicitar más ámbitos o permisos para un recurso que lo que se concede para la aplicación cliente. Si este es el caso, la llamada se realizará correctamente si se concede al menos un permiso. La notificación "scp" del **access\_token** resultante se rellena solo con los permisos que se concedieron correctamente.

> [!NOTE] 
> No admitimos solicitar permisos para dos recursos web distintos en la misma solicitud. Este tipo de solicitud generará un error.

### <a name="special-cases"></a>Casos especiales

El estándar de OpenID Connect especifica varios valores de "ámbito" especiales. Los ámbitos especiales siguientes representan el permiso para "tener acceso al perfil del usuario":

* **openid**: solicita un token de id.
* **offline\_access**: solicita un token de actualización (con flujos de código de autenticación).

Si el parámetro "response\_type" de una solicitud `authorize` incluye "token", el parámetro "scope" debe incluir al menos un permiso de recurso (distinto de "openid" y "offline\_access") que se concederá. De lo contrario, la solicitud `authorize` terminará con un error.

## <a name="the-returned-token"></a>El token devuelto

En un **access\_token** que se creó correctamente (desde el punto de conexión `authorize` o `token`), existirán las siguientes notificaciones:

| Nombre | Notificación | Descripción |
| --- | --- | --- |
|Público |`aud` |El \*id. de la aplicación\* del recurso único al que se le concedió acceso al token. |
|Scope |`scp` |Los permisos concedidos al recurso. Si se trata de varios permisos concedidos, se separarán con un espacio. |
|Entidad autorizada |`azp` |El \*id. de la aplicación\* de la aplicación cliente que inició la solicitud. |

Cuando la API recibe el **access\_token**, debe [validar el token](active-directory-b2c-reference-tokens.md) para demostrar que es auténtico y que tiene las notificaciones correctas.

Siempre estamos abiertos a todo tipo de comentarios y sugerencias. Si tiene dificultades para completar este tema o tiene recomendaciones para mejorar este contenido, agradecemos que escriba sus comentarios en la parte inferior de la página. Si tiene solicitudes de características, agréguelas a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
