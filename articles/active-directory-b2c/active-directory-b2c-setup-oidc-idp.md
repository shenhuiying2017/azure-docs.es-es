---
title: Agregar proveedores de identidades de OpenID Connect en directivas integradas - Azure AD B2C | Microsoft Docs
description: Introducción acerca de cómo agregar proveedores de OpenID Connect en directivas integradas en Azure AD B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.assetid: 357d193a-e33b-469c-8a93-0a8f45a60a9f
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.openlocfilehash: 46223f1ec6b82828983861dae26ec8d777b77b86
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308991"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: agregar un proveedor de identidades personalizado de OpenID Connect en políticas integradas

>[!NOTE]
> Esta característica está en versión preliminar pública. No use esta característica en entornos de producción.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) es un protocolo de autenticación basado en OAuth 2.0 que se puede usar para que los usuarios inicien sesión de forma segura. La mayoría de los proveedores de identidades que usan este protocolo, como [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), son compatibles con Azure AD B2C. En este artículo se explica cómo puede agregar proveedores de identidades personalizados de OpenID Connect en sus políticas integradas.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Configurar un proveedor de identidades personalizado de OpenID Connect

Para agregar un proveedor de identidades personalizado de OpenID Connect:

1. Siga estos pasos para [ir a la configuración de Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) en Azure Portal.
1. Haga clic en **Proveedores de identidades**.
1. Haga clic en **Agregar**.
1. Para seleccionar el **tipo de proveedor de identidades**, seleccione **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Configurar un proveedor de identidades de OpenID Connect

#### <a name="metadata-url"></a>URL de metadatos

Como requisito, todo proveedor de identidades de OpenID Connect describe un documento de metadatos que contiene la mayor parte de la información necesaria para iniciar sesión. Esta información incluye las direcciones URL que se usan y la ubicación de las claves de firma pública del servicio. El documento de metadatos de OpenID Connect siempre se encuentra en un punto de conexión que termina en `.well-known\openid-configuration`.

En cuanto al proveedor de identidades de OpenID Connect que quiere agregar, escriba su URL de metadatos.

#### <a name="client-id-and-secret"></a>Id. de cliente y secreto

Para permitir que los usuarios inicien sesión, el proveedor de identidades solicitará a los desarrolladores que registren una aplicación en su servicio. Esta aplicación tendrá un identificador (denominado **id. de cliente**) y un **secreto de cliente**. Copie estos valores desde el proveedor de identidades y escríbalos en los campos correspondientes.

> [!NOTE]
> El secreto del cliente es opcional. Sin embargo, debe especificar un secreto de cliente si quiere usar el [flujo de código de autorización](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), ya que usa el secreto para intercambiar el código del token.

#### <a name="scope"></a>Scope

Los ámbitos definen la información y los permisos que quiere recopilar del proveedor de identidades personalizado. Las solicitudes de OpenID Connect deben contener el valor del ámbito `openid` con el fin de recibir el token de id. del proveedor de identidades. Sin ese token de id., los usuarios no podrán iniciar sesión en Azure AD B2C mediante el proveedor de identidades personalizado.

Asimismo, puede anexar otros ámbitos (separados con espacios). Consulte la documentación del proveedor de identidades personalizado para ver otros ámbitos disponibles.

#### <a name="response-type"></a>Tipo de respuesta

El tipo de respuesta describe qué tipo de información se enviará en la llamada inicial al valor `authorization_endpoint` del proveedor de identidades personalizado. 

* `code`: según el [flujo de código de autorización](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), se devolverá un código a Azure AD B2C. A continuación, Azure AD B2C llamará a `token_endpoint` para intercambiar el código del token.
* `token`: se devolverá un token de acceso a Azure AD B2C desde el proveedor de identidades personalizado.
* `id_token`: se devolverá un token de id. a Azure AD B2C desde el proveedor de identidades personalizado.


#### <a name="response-mode"></a>Modo de respuesta

El modo de respuesta define el método que se debe usar para devolver los datos a Azure AD B2C desde el proveedor de identidades personalizado.

* `form_post`: este modo de respuesta se recomienda para mayor seguridad. La respuesta se transmite a través del método HTTP `POST`, con el código o token codificado en el cuerpo mediante el formato `application/x-www-form-urlencoded`.
* `query`: el código o token se devolverá como un parámetro de consulta.


#### <a name="domain-hint"></a>Sugerencia de dominio

La sugerencia de dominio se puede usar para ir directamente a la página de inicio de sesión del proveedor de identidades especificado, en lugar de hacer que el usuario lo seleccione en la lista de proveedores de identidades disponibles. Para permitir este tipo de comportamiento, escriba un valor en la sugerencia de dominio.

Para ir al proveedor de identidades personalizado, agregue el parámetro `domain_hint=<domain hint value>` al final de la solicitud cuando llame a Azure AD B2C para iniciar sesión.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>Asignar las notificaciones del proveedor de identidades de OpenID Connect

Una vez que el proveedor de identidades personalizado devuelve un token de id. a Azure AD B2C, Azure AD B2C debe poder asignar las notificaciones del token recibido a aquellas que Azure AD B2C ya reconoce y usa. 

Teniendo en cuenta cada una de las asignaciones que se detallan a continuación, consulte la documentación del proveedor de identidades personalizado para comprender las notificaciones que se devuelven en los tokens del proveedor de identidades.

* `User ID`: especifique la notificación que proporciona el identificador único del usuario con sesión iniciada.
* `Display Name`: especifique la notificación que proporciona el nombre para mostrar o el nombre completo del usuario.
* `Given Name`: especifique la notificación que proporciona el nombre del usuario.
* `Surname`: especifique la notificación que proporcionan los apellidos del usuario.
* `Email`: especifique la notificación que proporciona la dirección de correo electrónico del usuario.

## <a name="next-steps"></a>Pasos siguientes

Agregue el proveedor de identidades personalizado de OpenID Connect a la [directiva integrada](active-directory-b2c-reference-policies.md).
