---
title: 'Registro de aplicaciones: Azure Active Directory B2C'
description: "Registro de la aplicación con Azure Active Directory B2C"
services: active-directory-b2c
author: PatAltimore
manager: mtillman
editor: parakhj
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.openlocfilehash: b1d145466382c8fc2ea6c5e4e295940b0f000b97
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registro de la aplicación

Este inicio rápido le ayuda a registrar una aplicación en un inquilino de Microsoft Azure Active Directory (Azure AD) B2C en tan solo unos minutos. Cuando haya terminado, la aplicación se habrá registrado para usarse en el inquilino de Azure AD B2C.

## <a name="prerequisites"></a>Requisitos previos

Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Para obtener su propio inquilino, siga los pasos descritos en [Creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md).

Las aplicaciones creadas en Azure Portal se deben administrar desde la misma ubicación. Si edita las aplicaciones de Azure AD B2C mediante PowerShell u otro portal, dejarán de ser compatibles y no funcionarán con Azure AD B2C. Consulte los detalles en la sección [Aplicaciones con errores](#faulted-apps). 

En este artículo se usan ejemplos que le ayudarán a empezar a trabajar con los nuestros. Puede aprender más acerca de estos ejemplos en los artículos siguientes.

## <a name="navigate-to-b2c-settings"></a>Ir a la configuración de B2C

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Elija los pasos siguientes en función de su tipo de aplicación

* [Registro de una aplicación web](#register-a-web-app)
* [Registro de una API web](#register-a-web-api)
* [Registro de una aplicación móvil o nativa](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registro de una aplicación web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Creación de un secreto de cliente de aplicación web

Si la aplicación web llama a una API web protegida por Azure AD B2C, siga estos pasos:
   1. Cree un secreto de aplicación, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**. Anote el valor de la **Clave de la aplicación**. Use el valor como secreto de aplicación en el código de la aplicación.
   2. Haga clic en **Acceso de API**, en **Agregar** y seleccione la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
> 

[Saltar a los **pasos siguientes**](#next-steps)

### <a name="register-a-web-api"></a>Registro de una API web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Haga clic en **Ámbitos publicados** para agregar más ámbitos según sea necesario. De forma predeterminada, se definirá el ámbito de "user_impersonation". Este ámbito proporciona a otras aplicaciones la capacidad de tener acceso a esta API en nombre del usuario que ha iniciado la sesión. Si lo desea, se puede eliminar el ámbito user_impersonation.

[Saltar a los **pasos siguientes**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registro de una aplicación móvil o nativa

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Saltar a los **pasos siguientes**](#next-steps)

## <a name="limitations"></a>Limitaciones

### <a name="choosing-a-web-app-or-api-reply-url"></a>Selección de una dirección URL de respuesta de la aplicación web o API

Actualmente, las aplicaciones registradas en Azure AD B2C están restringidas a un conjunto limitado de valores de direcciones URL de respuesta. La dirección URL de respuesta de aplicaciones y servicios web debe comenzar por el esquema `https`, y todos los valores de dicha dirección deben compartir un único dominio DNS. Por ejemplo, no puede registrar una aplicación web con una de estas direcciones URL de respuesta:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

El sistema de registro compara el nombre DNS completo de la dirección URL de respuesta existente con el nombre DNS de la dirección URL de respuesta que va a agregar. La solicitud para agregar el nombre DNS presentará un error si se cumple alguna de las condiciones siguientes:

* Si el nombre DNS completo de la nueva dirección URL de respuesta no coincide con el nombre DNS de la dirección URL de respuesta existente.
* Si el nombre DNS completo de la nueva dirección URL de respuesta no es un subdominio de la dirección URL de respuesta existente.

Por ejemplo, si la aplicación tiene esta dirección URL de respuesta:

`https://login.contoso.com`

Puede agregarla del modo siguiente:

`https://login.contoso.com/new`

En este caso, el nombre DNS es una coincidencia exacta. O bien, puede hacer lo siguiente:

`https://new.login.contoso.com`

En este caso, hace referencia a un subdominio DNS de login.contoso.com. Si desea que una aplicación tenga login-east.contoso.com y login-west.contoso.com como direcciones URL de respuesta, debe agregar dichas direcciones en este orden:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Puede agregar las dos últimas porque son subdominios de la primera URL de respuesta, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Selección de un identificador URI de redireccionamiento de una aplicación nativa

Hay dos consideraciones importantes al elegir un URI de redirección para aplicaciones móviles o nativas:

* **Único**: el esquema del identificador URI de redirección debe ser único para todas las aplicaciones. En el ejemplo (com.onmicrosoft.contoso.appname://redirect/path), el esquema es com.onmicrosoft.contoso.appname. Se recomienda seguir este patrón. Si dos aplicaciones comparten el mismo esquema, el usuario verá un cuadro de diálogo de "elección de aplicación". Si el usuario realiza una elección incorrecta, no será posible iniciar sesión.
* **Completo**: el URI de redirección debe tener un esquema y una ruta de acceso. La ruta de acceso debe contener al menos una barra diagonal después del dominio (por ejemplo, //contoso/ será válido, pero si se escribe //contoso, se producirá un error).

Asegúrese de que no haya ningún carácter especial, como el carácter de subrayado, en el identificador URI de redireccionamiento.

### <a name="faulted-apps"></a>Aplicaciones con errores

Las aplicaciones B2C NO se deben editar:

* En otros portales de administración de aplicaciones, como el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/).
* Con la API Graph o PowerShell

Si edita la aplicación de Azure AD B2C como se ha descrito e intenta volver a editarla en las características de Azure AD B2C en Azure Portal, pasará a ser una aplicación con errores y no se podrá volver usar con Azure AD B2C. Tendrá que eliminarla y volver a crearla.

Para eliminar la aplicación, vaya al [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/) y elimínela desde allí. Para que la aplicación esté visible, debe ser el propietario de la misma (y no solo un administrador del inquilino).

## <a name="next-steps"></a>Pasos siguientes

Una vez que la aplicación está registrada en Azure AD B2C, puede completar uno de [los tutoriales de inicio rápido](active-directory-b2c-overview.md#get-started) para empezar a usarla.

> [!div class="nextstepaction"]
> [Crear una aplicación web de ASP.NET con registro, inicio de sesión y restablecimiento de contraseña](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
