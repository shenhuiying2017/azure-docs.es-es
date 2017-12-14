---
title: "Azure Active Directory B2C: configuración de Facebook | Microsoft Docs"
description: "Proporcione a los consumidores la posibilidad de registro e inicio de sesión con cuentas de Facebook en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: mtillman
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.openlocfilehash: 7f551a7ba9b7ddbb373e6b6418ab43fe7ce9be36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los consumidores con cuentas de Facebook
## <a name="create-a-facebook-application"></a>Creación de una aplicación de Facebook
Para usar Facebook como proveedor de identidades en Azure Active Directory (Azure AD) B2C, primero debe crear una aplicación de Facebook y suministrarle los parámetros correctos. Necesita una cuenta de Facebook para ello. Si no tiene una, puede obtenerla en [https://www.facebook.com/](https://www.facebook.com/).

1. Vaya al sitio web [Facebook for developers](https://developers.facebook.com/) e inicie sesión con las credenciales de su cuenta de Facebook.
2. Si aún no lo ha hecho, debe registrarse como desarrollador de Facebook. Para ello, haga clic en **Register** (Registrarte) (en la esquina superior derecha de la página), acepte las directivas de Facebook y complete los pasos de registro.
3. Haga clic en **Mis aplicaciones** y luego en **Agregar una nueva aplicación**. 
4. En el formulario, proporcione un valor para **Nombre para mostrar** y un valor válido de **Correo electrónico de contacto**.
5. Haga clic en **Create App ID** (Crear id. de aplicación). Es posible que deba aceptar las políticas de la plataforma Facebook y realizar una comprobación de seguridad en línea.
6. En la columna izquierda, haga clic en **Configuración** y, a continuación, seleccione el valor **Básico** en el caso de que no aparezca seleccionado ya.
7. Seleccione un valor de **Categoría**. 
8. Haga clic en **+Add Platform** (+Agregar plataforma) y seleccione **Sitio web**.
   
    ![Facebook - Configuración](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Configuración - Sitio web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Escriba `https://login.microsoftonline.com/` en el campo **Dirección URL del sitio** y, a continuación, haga clic en **Guardar cambios** en la parte inferior de la página.
   
    ![Facebook - Dirección URL del sitio](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Copie el valor de **App ID** (Id. de aplicación). Haga clic en **Show** (Mostrar) y copie el valor de **App Secret** (Secreto de la aplicación). Necesitará ambos para configurar Facebook como proveedor de identidades de su inquilino. El **secreto de la aplicación** es una credencial de seguridad importante.
   
    ![Facebook - Id. de aplicación y el secreto de la aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Haga clic en **+ Add Product** (+ Agregar producto) en la barra de navegación izquierda y, a continuación, en el botón **Set Up** (Instalación) de **Facebook Login** (Inicio de sesión de Facebook).
   
    ![Facebook - Inicio de sesión de Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Haga clic en **Settings** (Configuración) en la barra de navegación derecha **Facebook Login** (Inicio de sesión de Facebook)

    ![Facebook - Configuración de inicio de sesión de Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Valid OAuth redirect URIs** (URI de redirección de OAuth válido) de la sección **Client OAuth Settings** (Configuración de OAuth de cliente). Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Save Changes** (Guardar cambios) en la parte inferior de la página.
    
    ![Facebook - URI de redireccionamiento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Para que la aplicación Facebook se puede usar con Azure AD B2C, es necesario ponerla a disposición del público. Para ello, haga clic en **App Review** (Revisión de la aplicación) en la barra navegación izquierda, mueva el conmutador de la parte superior de la página a **YES** (SÍ) y haga clic en **Confirm** (Confirmar).
    
    ![Facebook - Público de la aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configuración de Facebook como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) de Azure Portal.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, escriba "Facebook".
5. Haga clic en **Identity provider type** (Tipo de proveedor de identidades), seleccione **Facebook** y haga clic en **OK** (Aceptar).
6. Haga clic en **Set up this identity provider** (Configurar este proveedor de identidades), y escriba el id. y el secreto de la aplicación (de la aplicación de Facebook que creó anteriormente) en los campos **Client ID** y **Client secret** respectivamente.
7. Haga clic en **OK** (Aceptar) y, luego, en **Create** (Crear) para guardar la configuración de Facebook.

> [!NOTE]
> Si se agrega un **Proveedor de identidades** a su inquilino, sus directivas existentes no se modificarán. Recuerde actualizar sus directivas incluyendo el proveedor de identidades que acaba de crear.
>