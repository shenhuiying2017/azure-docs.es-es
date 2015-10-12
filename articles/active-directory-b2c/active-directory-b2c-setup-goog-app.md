<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Configuración de Google+ | Microsoft Azure"
	description="Proporcionar registro e inicio de sesión a los consumidores con cuentas de Google+ en las aplicaciones protegidas por Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Proporcionar a los consumidores registro e inicio de sesión con cuentas de Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creación de una aplicación de Google+

Para usar Google+ como proveedor de identidades en Azure Active Directory (AD) B2C, primero debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Para ello, necesita una cuenta de Google+; si no tiene una, puede obtenerla en [https://www.facebook.com/](https://accounts.google.com/SignUp).

1. Vaya a [Google Developers Console](https://console.developers.google.com/) e inicie sesión con las credenciales de su cuenta de Google+.
2. Haga clic en **Create Project** (Crear proyecto), escriba un **nombre de proyecto**, acepte los términos del servicio y, luego, haga clic en **Create** (Crear).

    ![G+ - primeros pasos](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - nuevo proyecto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. En el panel de navegación izquierdo, haga clic en **APIs & Auth** (API y autenticación) y, luego, en **Credentials** (Credenciales).
4. Haga clic en la **pantalla de consentimiento de OAuth** en la parte superior.

    ![G+ - credenciales](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Seleccione o especifique una **dirección de correo electrónico** válida, proporcione un **nombre de producto** y haga clic en **Save** (Guardar).

    ![G+ - pantalla de consentimiento de OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. En **Application type** (Tipo de aplicación) seleccione **Web application** (Aplicación web).
7. Proporcione un **nombre** para la aplicación, escriba [https://login.microsoftonline.com](https://login.microsoftonline.com) en el campo **Authorized redirect URIs** (URI de redirección autorizados) y [https://login.microsoftonline.com/te/{tenant}/oauth2/authresp](https://login.microsoftonline.com/te/{tenant}/oauth2/authresp) en el campo **Authorized redirect URIs** (URI de redirección autorizados) donde **{tenant}** se sustituirá por el nombre del inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Crear**.

    > [AZURE.NOTE]El valor de **{tenant}** distingue mayúsculas de minúsculas.

    ![G+ - crear id. de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. Copie los valores **Client ID** (Id. de cliente) y **Client secret** (Secreto del cliente) para usarlos más adelante. Necesitará ambos para configurar Google+ como proveedor de identidades de su inquilino.

    > [AZURE.NOTE]El **secreto de cliente** es una credencial de seguridad importante.

    ![G+ - secreto de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configuración de Google+ como proveedor de identidades del inquilino

1. [Vaya a la hoja de características B2C del Portal de vista previa de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. En la hoja de características B2C, haga clic en **Proveedores de identidades sociales**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "G+".
5. Haga clic en **Tipo de proveedor de identidades**, seleccione **Google** y haga clic en **Aceptar**.
6. Haga clic en **Configurar este proveedor de identidades** y escriba el **id. de cliente** y el **secreto de cliente** de la aplicación de Google+ que creó anteriormente.
7. Haga clic en **Aceptar** y, luego, en **Crear** para guardar la configuración de Google+.

<!---HONumber=Oct15_HO1-->