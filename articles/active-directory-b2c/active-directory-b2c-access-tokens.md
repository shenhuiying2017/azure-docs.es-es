---
title: 'Solicitud de tokens de acceso: Azure AD B2C | Microsoft Docs'
description: "En este artículo se mostrará cómo configurar una aplicación cliente y adquirir un token de acceso."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: mtillman
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: parakhj
ms.openlocfilehash: 9d2df39118741d4254f7b7fe4c419a00ceb4ba8e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: solicitud de tokens de acceso

Un token de acceso, que se designa como **access\_token** en las respuestas de Azure AD B2C, es una forma de token de seguridad que un cliente puede usar para tener acceso a los recursos protegidos por un [servidor de autorización](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), como una API web. Los tokens de acceso se representan como [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) y contienen información sobre el servidor de recursos previsto y los permisos concedidos al servidor. Cuando se llama al servidor de recursos, el token de acceso debe incluirse en la solicitud HTTP.

En este artículo se trata cómo configurar una aplicación cliente y una API web para obtener un **access\_token**.

> [!NOTE]
> **Las cadenas de la API web (en nombre de) no son compatibles con Azure AD B2C.**
>
> Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es común en los clientes nativos que tienen un back-end de API web que, a su vez, llama a un servicio de Microsoft Online, como la API de Azure AD Graph.
>
> Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador JWT de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no está implementado actualmente en Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registro de una API web y publicación de permisos

Antes de solicitar un token de acceso, debe registrar una API web y publicar los permisos (ámbitos) que se pueden conceder a la aplicación cliente.

### <a name="register-a-web-api"></a>Registro de una API web

1. En el menú de características de Azure AD B2C, haga clic en **Aplicaciones**.
1. Haga clic en **+Agregar** en la parte superior del menú.
1. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "API Contoso".
1. Cambie el conmutador **Include web app / web API** (Incluir aplic. web/API web) a **Yes** (Sí).
1. Escriba un valor arbitrario para las **Direcciones URL de respuesta**. Por ejemplo, escriba: `https://localhost:44316/`. El valor no importa, ya que una API no debe recibir el token directamente de Azure AD B2C.
1. Escriba un **URI de identificador de aplicación**. Este es el identificador utilizado para la API web. Por ejemplo, escriba "notas" en el cuadro. El identificador **URI de id. de aplicación** sería entonces `https://{tenantName}.onmicrosoft.com/notes`.
1. Haga clic en **Crear** para registrar la aplicación.
1. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.

### <a name="publishing-permissions"></a>Publicación de permisos

Los ámbitos, que son análogos a los permisos, son necesarios si la aplicación llama a una API. Algunos ejemplos de ámbitos son "lectura" o "escritura". Imagine que quiere su aplicación web o nativa para "leer" desde una API. Su aplicación llamaría a Azure AD B2C y solicitaría un token de acceso que proporcionara acceso al ámbito "lectura". Para que Azure AD B2C emita semejante token de acceso, debe concederse permiso a la aplicación para "leer" desde la API específica. Para ello, lo primero que debe hacer su API es publicar el ámbito "lectura".

1. En el menú **Aplicaciones** de Azure AD B2C, abra la aplicación de API web ("API Contoso").
1. Haga clic en **Ámbitos publicados**. Esto es donde se definen los permisos (ámbitos) que se pueden conceder a otras aplicaciones.
1. Agregue **Valores de ámbito** según sea necesario (por ejemplo, "lectura"). De forma predeterminada, se definirá el ámbito de "user_impersonation". Puede omitir esto si lo desea. Escriba una descripción del ámbito en la columna **Nombre de ámbito**.
1. Haga clic en **Guardar**.

> [!IMPORTANT]
> El **Nombre de ámbito** es la descripción del **Valor de ámbito**. Al usar el ámbito, asegúrese de usar el **Valor de ámbito**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Concesión de permisos de aplicación nativa o web a una API web

Una vez configurada una API para publicar ámbitos, es necesario que se concedan a la aplicación cliente esos ámbitos a través de Azure Portal.

1. Vaya al menú **Aplicaciones** en el menú de características de Azure AD B2C.
1. Registre una aplicación cliente ([aplicación web](active-directory-b2c-app-registration.md#register-a-web-app) o [cliente nativo](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) si no tiene una todavía. Si sigue esta guía como punto de partida, deberá registrar una aplicación cliente.
1. Haga clic en **Acceso de API**.
1. Haga clic en **Agregar**.
1. Seleccione la API web y los ámbitos (permisos) que desea conceder.
1. Haga clic en **Aceptar**.

> [!NOTE]
> Azure AD B2C no pide el consentimiento de los usuarios de la aplicación cliente. En lugar de eso, el administrador proporciona todo el consentimiento según los permisos configurados entre las aplicaciones descritas anteriormente. Si se revoca un permiso concedido para una aplicación, todos los usuarios que anteriormente podían adquirir ese permiso ya no podrán hacerlo.

## <a name="requesting-a-token"></a>Solicitud de un token

Al solicitar un token de acceso, la aplicación cliente debe especificar los permisos que desea en el parámetro **scope** de la solicitud. Por ejemplo, para determinar el **Valor de ámbito** "lectura" de la API que tiene el identificador **URI de id. de aplicación** de `https://contoso.onmicrosoft.com/notes`, el ámbito sería `https://contoso.onmicrosoft.com/notes/read`. A continuación se muestra un ejemplo de solicitud de código de autorización al punto de conexión `/authorize`.

> [!NOTE]
> Actualmente, los dominios personalizados no se admiten junto con los tokens de acceso. Debe usar su dominio tenantName.onmicrosoft.com en la URL de solicitud.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
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

También puede solicitar más ámbitos (permisos) para un recurso que lo que se concede para la aplicación cliente. Si este es el caso, la llamada se realizará correctamente si se concede al menos un permiso. La notificación "scp" del **access\_token** resultante se rellena solo con los permisos que se concedieron correctamente.

> [!NOTE] 
> No admitimos solicitar permisos para dos recursos web distintos en la misma solicitud. Este tipo de solicitud generará un error.

### <a name="special-cases"></a>Casos especiales

El estándar de OpenID Connect especifica varios valores de "ámbito" especiales. Los ámbitos especiales siguientes representan el permiso para "tener acceso al perfil del usuario":

* **openid**: solicita un token de id.
* **offline\_access**: solicita un token de actualización (con [flujos de código de autenticación](active-directory-b2c-reference-oauth-code.md)).

Si el parámetro `response_type` de una solicitud de `/authorize` incluye `token`, el parámetro `scope` debe incluir al menos un ámbito de recurso (distinto de `openid` y `offline_access`) para su concesión. De lo contrario, la solicitud `/authorize` terminará con un error.

## <a name="the-returned-token"></a>El token devuelto

En un **access\_token** que se creó correctamente (desde el punto de conexión `/authorize` o `/token`), existirán las siguientes notificaciones:

| Nombre | Notificación | Descripción |
| --- | --- | --- |
|Público |`aud` |El **id. de la aplicación** del recurso único al que se le concedió acceso al token. |
|Scope |`scp` |Los permisos concedidos al recurso. Si se trata de varios permisos concedidos, se separarán con un espacio. |
|Entidad autorizada |`azp` |El **id. de la aplicación** cliente que inició la solicitud. |

Cuando la API recibe el **access\_token**, debe [validar el token](active-directory-b2c-reference-tokens.md) para demostrar que es auténtico y que tiene las notificaciones correctas.

Siempre estamos abiertos a todo tipo de comentarios y sugerencias. Si tiene dificultades con este tema, publique en Stack Overflow mediante la etiqueta ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Si tiene solicitudes de características, agréguelas a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Pasos siguientes

* Creación de una API web mediante [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Creación de una API web mediante [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
