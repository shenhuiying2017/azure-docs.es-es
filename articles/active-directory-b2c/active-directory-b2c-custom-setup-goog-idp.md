---
title: "Azure Active Directory B2C: adición de Google + como un proveedor de identidades de OAuth2 mediante directivas personalizadas"
description: Ejemplo donde se usa Google+ como proveedor de identidades mediante el protocolo OAuth2
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: e0aaf710d230f7667fff32b50ddb64104509d740
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adición de Google + como un proveedor de identidades de OAuth2 mediante directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En esta guía se muestra cómo habilitar el inicio de sesión para usuarios de desde la cuenta de Google+ mediante [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos

Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:

1.  Crear una aplicación de la cuenta de Google+
2.  Agregar la clave de aplicación de la cuenta de Google+ a Azure AD B2C
3.  Agregar un proveedor de notificaciones a una directiva
4.  Registrar el proveedor de notificaciones de la cuenta de Google + en un recorrido del usuario
5.  Cargar la directiva en un inquilino de Azure AD B2C y probarla

## <a name="create-a-google-account-application"></a>Creación de una aplicación de la cuenta de Google+
Para usar Google+ como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de Google+ y suministrarle los parámetros correctos. Puede registrar una aplicación de Google+ aquí: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1.  Vaya a [Google Developers Console](https://console.developers.google.com/) e inicie sesión con las credenciales de su cuenta de Google+.
2.  Haga clic en **Create project** (Crear proyecto), escriba un nombre de proyecto en **Project name** y haga clic en **Create** (Crear).

3.  Haga clic en el menú **Projects** (Proyectos).

    ![Cuenta de Google+: Selección del proyecto](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Haga clic en el botón **+**.

    ![Cuenta de Google+: Creación de un nuevo proyecto](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Escriba un **nombre de proyecto** y luego haga clic en **Create** (Crear).

    ![Cuenta de Google+: Nuevo proyecto](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Espere a que el proyecto esté listo y haga clic en el menú **Projects** (Proyectos).

    ![Cuenta de Google+: Esperar a que el nuevo proyecto esté listo para usar](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Haga clic en el nombre del proyecto.

    ![Cuenta de Google+: Selección del nuevo proyecto](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  En el panel de navegación izquierdo, haga clic en **API Manager** (Administrador de API) y, luego, en **Credentials** (Credenciales).
9.  Haga clic en la **pantalla de autorización de OAuth** en la parte superior.

    ![Cuenta de Google+: Configuración de la pantalla de consentimiento de OAuth](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Seleccione o especifique una dirección de correo electrónico válida en **Email address**, especifique un nombre de producto en **Product name** y haga clic en **Save** (Guardar).

    ![Google+: Credenciales de la aplicación](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Haga clic en **New credentials** (Nuevas credenciales) y, luego, elija **OAuth client ID** (Id. de cliente de OAuth).

    ![Google+: Creación de nuevas credenciales de aplicación](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  En **Application type** (Tipo de aplicación), seleccione **Web application** (Aplicación web).

    ![Google+: Selección del tipo de aplicación](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Especifique un **nombre** para la aplicación, escriba `https://login.microsoftonline.com` en el campo **Authorized JavaScript origins** (Orígenes de JavaScript autorizados) y `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Authorized redirect URIs** (URI de redirección autorizados). Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). El valor de **{tenant}** distingue mayúsculas de minúsculas. Haga clic en **Crear**.

    ![Google+: Provisión de los orígenes y los URI de redirección autorizados de JavaScript](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Copie los valores de **Client Id** (Id. de cliente) y **Client secret** (Secreto de cliente). Debe configurar Google+ como proveedor de identidades en su inquilino. **Secreto del cliente** es una credencial de seguridad importante.

    ![Google+: Copia de los valores de id. de cliente y secreto de cliente](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Adición de la clave de aplicación de la cuenta de Google+ a Azure AD B2C
La federación con cuentas de Google+ requiere un secreto de cliente para que la cuenta de Google+ confíe en Azure AD B2C en nombre de la aplicación. Debe almacenar el secreto de aplicación de Google+ en el inquilino de Azure AD B2C:  

1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Manual**.
5.  En **Nombre**, use `GoogleSecret`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En el cuadro **Secreto**, escriba el secreto de aplicación de Microsoft desde https://apps.dev.microsoft.com.
7.  En **Uso de claves**, use **Firma**.
8.  Haga clic en **Crear**
9.  Confirme que ha creado la clave `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adición de un proveedor de notificaciones en la directiva de extensión

Si desea que los usuarios inicien sesión mediante la cuenta de Google+, debe definir dicha cuenta como proveedor de notificaciones. En otras palabras, debe especificar un punto de conexión con el que Azure AD B2C se comunica. El punto de conexión proporciona un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Defina la cuenta de Google+ como proveedor de notificaciones; para ello, agregue el nodo `<ClaimsProvider>` en el archivo de la directiva de extensión:

1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. Si necesita un editor XML, [pruebe Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
2.  Busque la sección `<ClaimsProviders>`.
3.  Agregue el siguiente fragmento de código XML bajo el elemento `ClaimsProviders` y sustituya el valor `client_id` por su id. de cliente de aplicación de la cuenta de Google+ antes de guardar el archivo.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registro del proveedor de notificaciones de Google+ en el recorrido del usuario de registro o inicio de sesión

Se ha configurado el proveedor de identidades.  Sin embargo, no está disponible en ninguna de las pantallas de registro o inicio de sesión. Agregue el proveedor de identidades de la cuenta de Google+ al recorrido del usuario `SignUpOrSignIn` del usuario. Para que esté disponible, se crea un duplicado de un recorrido del usuario de plantilla ya existente.  A continuación, se agrega el proveedor de identidades de la cuenta de Google +:

>[!NOTE]
>
>Si ha copiado el elemento `<UserJourneys>` del archivo base de su directiva en el archivo de extensión (TrustFrameworkExtensions.xml), puede omitir esta sección.

1.  Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2.  Busque el elemento `<UserJourneys>` y copie el contenido entero del nodo `<UserJourneys>`.
3.  Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
4.  Pegue el contenido entero del nodo `<UserJournesy>` que copió como secundario del elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualización del botón
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden.  El elemento `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades de una página de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para la cuenta de Google+, se muestra un nuevo botón cuando un usuario llega a la página. Para agregar este elemento:

1.  Busque el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.
2.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
3.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción
Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de Google+ para recibir un token.

1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Asegúrese de que el valor de `Id` sea el mismo que el de `TargetClaimsExchangeId` en la sección anterior.
> * Asegúrese de que el id. de `TechnicalProfileReferenceId` se establece en el perfil técnico que creó anteriormente (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Abra la hoja **Todas las directivas**.
4.  Seleccione **Cargar directiva**.
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="test-the-custom-policy-by-using-run-now"></a>Probar la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    >[!NOTE]
    >
    >    **Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. 
    >    Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.


2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de Google+.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registro del proveedor de notificaciones de la cuenta en Google+ en el recorrido del usuario de edición de perfil
Puede que también desee agregar el proveedor de identidades de la cuenta de Google + al recorrido del usuario `ProfileEdit` de su usuario. Para que esté disponible, se repiten los dos últimos pasos:

### <a name="display-the-button"></a>Visualización del botón
1.  Abra el archivo de extensión de su directiva (por ejemplo, TrustFrameworkExtensions.xml).
2.  Busque el nodo `<UserJourney>` que incluye `Id="ProfileEdit"` en el recorrido del usuario que ha copiado.
3.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
4.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción
1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Prueba de la directiva de edición de perfil personalizada mediante Ejecutar ahora

1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
2.  Abra **B2C_1A_ProfileEdit**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de Google+.

## <a name="download-the-complete-policy-files"></a>Descarga de los archivos de directiva completos
Opcional: se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
