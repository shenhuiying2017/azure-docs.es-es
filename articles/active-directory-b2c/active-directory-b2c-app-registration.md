---
title: 'Azure Active Directory B2C: Registro de aplicaciones | Microsoft Docs'
description: "Registro de la aplicación con Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registro de la aplicación

> [!IMPORTANT]
> Las aplicaciones creadas en la hoja de Azure AD B2C de Azure Portal se deben administrar desde la misma ubicación. Si edita las aplicaciones B2C mediante PowerShell u otro portal, dejarán de ser compatibles y no funcionarán con Azure AD B2C. Vea más información [a continuación](#faulted-apps).
>

## <a name="prerequisite"></a>Requisito previo

Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Para obtener su propio inquilino, siga los pasos descritos en [Creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md). Si ha seguido todos los pasos de este artículo, debe tener la hoja de características B2C anclada en el panel de inicio.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegación a la hoja de características B2C

Si tiene la hoja de características B2C anclada al Panel de inicio, la verá en cuanto inicie sesión en el [Portal de Azure](https://portal.azure.com/) como administrador global del inquilino B2C.

Otra manera de acceder a la hoja es hacer clic en **Más servicios** y luego buscar **Azure AD B2C** en el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com/).

> [!IMPORTANT]
> Necesita ser administrador global del inquilino B2C para poder acceder a la hoja Características B2C. Un administrador global de cualquier otro inquilino o un usuario de cualquier inquilino no puede acceder a dicha hoja.  Puede cambiar a su inquilino B2C mediante el selector de inquilinos de la esquina superior derecha de Azure Portal.
>
>

## <a name="register-a-web-application"></a>Registro de una aplicación web

1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
1. Haga clic en **+Agregar** en la parte superior de la hoja.
1. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
1. Cambie el conmutador **Include web app / web API** (Incluir aplic. web/API web) a **Yes** (Sí).
1. Escriba un valor [adecuado](#limitations) para las **Direcciones URL de respuesta**, que son los puntos de conexión a los que Azure AD B2C devolverá los tokens que la aplicación solicite. Por ejemplo, escriba: `https://localhost:44316/`.
1. Haga clic en **Crear** para registrar la aplicación.
1. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.
1. Si la aplicación web va a llamar también a una API web protegida mediante Azure AD B2C, deberá:
    1. Crear un **secreto de aplicación**, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**.
    1. Hacer clic en **Acceso de API**, en **Agregar** y seleccionar la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
>

## <a name="register-a-web-api"></a>Registro de una API web

1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
1. Haga clic en **+Agregar** en la parte superior de la hoja.
1. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "API Contoso B2C".
1. Cambie el conmutador **Include web app / web API** (Incluir aplic. web/API web) a **Yes** (Sí).
1. Escriba un valor [adecuado](#choosing-a-web-app/api-reply-url) para las **Direcciones URL de respuesta**, que son los puntos de conexión a los que Azure AD B2C devolverá los tokens que la aplicación solicite. Por ejemplo, escriba: `https://localhost:44316/`.
1. Escriba un **URI de identificador de aplicación**. Este es el identificador utilizado para la API web. Por ejemplo, escriba "notas". Generará el identificador URI completo debajo.
1. Haga clic en **Crear** para registrar la aplicación.
1. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.
1. Haga clic en **Ámbitos publicados**. Esto es donde se definen los permisos (ámbitos) que se pueden conceder a otras aplicaciones.
1. Agregue más ámbitos según sea necesario. De forma predeterminada, se definirá el ámbito de "user_impersonation". Esto proporciona a otras aplicaciones la capacidad de tener acceso a esta API en nombre del usuario que ha iniciado la sesión. Esto se puede quitar si lo desea.
1. Haga clic en **Guardar**.

## <a name="register-a-mobilenative-application"></a>Registrar una aplicación móvil o nativa

1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
1. Haga clic en **+Agregar** en la parte superior de la hoja.
1. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
1. Cambie **Include native client** (Incluir cliente nativo) a **Yes** (Sí).
1. Escriba un **URI de redirección** con un esquema personalizado. Por ejemplo, com.onmicrosoft.contoso.appname://redirect/path. Asegúrese de elegir un [buen identificador URI de redireccionamiento](#choosing-a-native-application-redirect-uri) y no incluya caracteres especiales, como caracteres de subrayado.
1. Haga clic en **Guardar** para registrar la aplicación.
1. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.
1. Si la aplicación nativa también va a llamar a una API web protegida por Azure AD B2C, deberá:
    1. Crear un **secreto de aplicación**, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**.
    1. Hacer clic en **Acceso de API**, en **Agregar** y seleccionar la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
>

## <a name="limitations"></a>Limitaciones

### <a name="choosing-a-web-appapi-reply-url"></a>Elegir una dirección URL de respuesta de la aplicación web o API

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

### <a name="choosing-a-native-application-redirect-uri"></a>Elegir un identificador URI de redireccionamiento de una aplicación nativa

Hay dos consideraciones importantes al elegir un URI de redirección para aplicaciones móviles o nativas:

* **Único**: el esquema del identificador URI de redirección debe ser único para todas las aplicaciones. En nuestro ejemplo (com.onmicrosoft.contoso.appname://redirect/path), se usa com.onmicrosoft.contoso.appname como esquema. Se recomienda seguir este patrón. Si dos aplicaciones comparten el mismo esquema, el usuario verá un cuadro de diálogo de "elección de aplicación". Si el usuario realiza una elección incorrecta, no será posible iniciar sesión.
* **Completo**: el URI de redirección debe tener un esquema y una ruta de acceso. La ruta de acceso debe contener al menos una barra diagonal después del dominio (por ejemplo, //contoso/ será válido, pero si se escribe //contoso, se producirá un error).

Asegúrese de que no haya ningún carácter especial, como el carácter de subrayado, en el identificador URI de redireccionamiento.

### <a name="faulted-apps"></a>Aplicaciones con errores

Las aplicaciones B2C NO se deben editar:

* En otros portales de administración de aplicaciones, como el [Portal de Azure clásico](https://manage.windowsazure.com/) o el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/).
* Con la API Graph o PowerShell

Si edita la aplicación B2C tal y como se ha descrito anteriormente e intenta editarla de nuevo en la hoja de características de Azure AD B2C en Azure Portal, se convertirá en una aplicación con errores y ya no se podrá usar con Azure AD B2C. Tendrá que eliminar la aplicación y volver a crearla.

Para eliminar la aplicación, vaya al [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/) y elimínela desde allí. Para que la aplicación esté visible, debe ser el propietario de la misma (y no solo un administrador del inquilino).

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una aplicación registrada en Azure AD B2C, puede completar uno de [nuestros tutoriales de inicio rápido](active-directory-b2c-overview.md#get-started) para ponerse en marcha rápidamente.

