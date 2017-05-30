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
ms.date: 3/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 414c684c78314a92bbbe12e6164e2b10fb682b0f
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registro de la aplicación

> [!IMPORTANT]
> Las aplicaciones creadas en la hoja de Azure AD B2C de Azure Portal se deben administrar desde la misma ubicación. Si edita las aplicaciones B2C mediante PowerShell u otro portal, dejarán no serán compatibles y es probable que no funcionen con Azure AD B2C.
> 
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
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
4. Cambie el conmutador **Include web app / web API** (Incluir aplic. web/API web) a **Yes** (Sí). Las **Direcciones URL de respuesta** son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. Por ejemplo, escriba: `https://localhost:44316/`.
5. Haga clic en **Crear** para registrar la aplicación.
6. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código. 
7. Si la aplicación web va a llamar también a una API web protegida mediante Azure AD B2C, deberá:
   1. Crear un **secreto de aplicación**, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**.
   2. Hacer clic en **Acceso de API**, en **Agregar** y seleccionar la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
> 
   

## <a name="register-a-web-api"></a>Registro de una API web
1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "API Contoso B2C".
4. Cambie el conmutador **Include web app / web API** (Incluir aplic. web/API web) a **Yes** (Sí). Las **Direcciones URL de respuesta** son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. Por ejemplo, escriba: `https://localhost:44316/`.
5. Escriba un **URI de identificador de aplicación**. Este es el identificador utilizado para la API web. Por ejemplo, escriba "notas". Generará el identificador URI completo debajo. 
6. Haga clic en **Crear** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.
8. Haga clic en **Ámbitos publicados**. Esto es donde se definen los permisos (ámbitos) que se pueden conceder a otras aplicaciones.
9. Agregue más ámbitos según sea necesario. De forma predeterminada, se definirá el ámbito de "user_impersonation". Esto proporciona a otras aplicaciones la capacidad de tener acceso a esta API en nombre del usuario que ha iniciado la sesión. Esto se puede quitar si lo desea. 
10. Haga clic en **Guardar**.

## <a name="register-a-mobilenative-application"></a>Registrar una aplicación móvil o nativa
1. En la hoja de características B2C del Portal de Azure, haga clic en **Aplicaciones**.
2. Haga clic en **+Agregar** en la parte superior de la hoja.
3. Escriba un **Nombre** para la aplicación que la describa a los consumidores. Por ejemplo, puede escribir "Aplicación Contoso B2C".
4. Cambie **Include native client** (Incluir cliente nativo) a **Yes** (Sí).
5. Escriba un **URI de redirección** con un esquema personalizado. Por ejemplo, com.onmicrosoft.contoso.appname://redirect/path. Asegúrese de elegir un [buen URI de redirección](#choosing-a-redirect-uri).
6. Haga clic en **Guardar** para registrar la aplicación.
7. Haga clic en la aplicación que acaba de crear y copie el **Id. de cliente de aplicación** único global que usará más adelante en el código.
8. Si la aplicación nativa también va a llamar a una API web protegida por Azure AD B2C, deberá:
   1. Crear un **secreto de aplicación**, para lo cual debe ir a la hoja **Claves** y hacer clic en el botón **Generar clave**.
   2. Hacer clic en **Acceso de API**, en **Agregar** y seleccionar la API web y los ámbitos (permisos).

> [!NOTE]
> Un **secreto de aplicación** es una credencial de seguridad importante y debe protegerse correctamente.
> 

### <a name="choosing-a-redirect-uri"></a>Elección de un identificador URI de redirección
Hay dos consideraciones importantes al elegir un URI de redirección para aplicaciones móviles o nativas: 
* **Único**: el esquema del identificador URI de redirección debe ser único para todas las aplicaciones. En nuestro ejemplo (com.onmicrosoft.contoso.appname://redirect/path), se usa com.onmicrosoft.contoso.appname como esquema. Se recomienda seguir este patrón. Si dos aplicaciones comparten el mismo esquema, el usuario verá un cuadro de diálogo de "elección de aplicación". Si el usuario realiza una elección incorrecta, no será posible iniciar sesión. 
* **Completo**: el URI de redirección debe tener un esquema y una ruta de acceso. La ruta de acceso debe contener al menos una barra diagonal después del dominio (por ejemplo, //contoso/ será válido, pero si se escribe //contoso, se producirá un error). 

## <a name="build-an-application"></a>Creación de una aplicación
Ahora que tiene una aplicación registrada en Azure AD B2C, puede completar uno de [nuestros tutoriales de inicio rápido](active-directory-b2c-overview.md#get-started) para ponerse en marcha rápidamente.


