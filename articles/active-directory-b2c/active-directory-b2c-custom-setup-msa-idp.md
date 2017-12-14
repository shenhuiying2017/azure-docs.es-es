---
title: "Azure Active Directory B2C: adición de una cuenta de Microsoft (MSA) como proveedor de identidades mediante directivas personalizadas"
description: Ejemplo en el que se usa Microsoft como proveedor de identidades mediante el protocolo OpenID Connect (OIDC).
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: cdc77d093358fa15bb1acbc9ba6b1867bae062f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adición de una cuenta de Microsoft (MSA) como proveedor de identidades mediante directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para usuarios de la cuenta de Microsoft (MSA) mediante [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:

1.  Crear una aplicación de la cuenta de Microsoft
2.  Agregar la clave de aplicación de la cuenta de Microsoft a Azure AD B2C
3.  Agregar un proveedor de notificaciones a una directiva
4.  Registrar el proveedor de notificaciones de la cuenta de Microsoft en un recorrido del usuario
5.  Cargar la directiva en un inquilino de Azure AD B2C y probarla

## <a name="create-a-microsoft-account-application"></a>Creación de una aplicación de cuenta Microsoft
Para usar una cuenta Microsoft como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una aplicación de cuenta Microsoft y suministrarle los parámetros correctos. Necesita una cuenta de Microsoft. Si no tiene una, visite [https://www.live.com/](https://www.live.com/).

1.  Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e inicie sesión con las credenciales de su cuenta Microsoft.
2.  Haga clic en **Agregar una aplicación**.

    ![Cuenta de Microsoft: Adición de una aplicación](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Proporcione un **nombre** para su aplicación, un **correo electrónico de contacto**, desactive **Permítanos que le ayudemos a empezar** y haga clic en **Crear**.

    ![Cuenta de Microsoft: Registro de la aplicación](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Copie el valor de **Id. de aplicación**. Lo necesitará para configurar una cuenta de Microsoft como proveedor de identidades de su inquilino.

    ![Cuenta de Microsoft: Copia del valor de id. de aplicación](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Haga clic en **Agregar plataforma**.

    ![Cuenta Microsoft: Agregar plataforma](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  En la lista de plataformas, elija **Web**.

    ![Cuenta de Microsoft: En la lista de plataformas, selección de Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **URI de redireccionamiento** . Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com).

    ![Cuenta de Microsoft: Establecimiento de las direcciones URL de redireccionamiento](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Haga clic en **Generar nueva contraseña** en la sección **Secretos de aplicación**. Copie la nueva contraseña que se muestra en la pantalla. Lo necesitará para configurar una cuenta de Microsoft como proveedor de identidades de su inquilino. Esta contraseña es una credencial de seguridad importante.

    ![Cuenta Microsoft: Generar nueva contraseña](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Cuenta de Microsoft: Copia de la nueva contraseña](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Active la casilla **Soporte técnico de SDK de Live** de la sección **Opciones avanzadas**. Haga clic en **Guardar**.

    ![Cuenta Microsoft: Soporte técnico de SDK de Live](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Adición de la clave de aplicación de la cuenta de Microsoft a Azure AD B2C
La federación con cuentas de Microsoft requiere un secreto de cliente para que la cuenta de Microsoft confíe en Azure AD B2C en nombre de la aplicación. Debe almacenar el secreto de aplicación de la cuenta de Microsoft en el inquilino de Azure AD B2C:   

1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Manual**.
5.  En **Nombre**, use `MSASecret`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En el cuadro **Secreto**, escriba el secreto de aplicación de Microsoft desde https://apps.dev.microsoft.com.
7.  En **Uso de claves**, use **Firma**.
8.  Haga clic en **Crear**
9.  Confirme que ha creado la clave `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adición de un proveedor de notificaciones en la directiva de extensión
Si desea que los usuarios inicien sesión mediante la cuenta de Microsoft, debe definir este servicio como proveedor de notificaciones. En otras palabras, debe especificar un punto de conexión con el que Azure AD B2C se comunica. El punto de conexión proporciona un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Defina la cuenta de Microsoft como proveedor de notificaciones; para ello, agregue el nodo `<ClaimsProvider>` en el archivo de la directiva de extensión:

1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. Si necesita un editor XML, [pruebe Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
2.  Busque la sección `<ClaimsProviders>`.
3.  Agregue el siguiente fragmento de código XML en el elemento `ClaimsProviders`:

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

4.  Reemplace el valor `client_id` por el id. de cliente de aplicación de la cuenta de Microsoft.

5.  Guarde el archivo .

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registro del proveedor de notificaciones de la cuenta de Microsoft en un recorrido del usuario de registro o inicio de sesión

El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión. Ahora, debe agregar el proveedor de identidades de la cuenta de Microsoft a su recorrido del usuario `SignUpOrSignIn`. Para que esté disponible, se crea un duplicado de un recorrido del usuario de plantilla ya existente.  Luego, se agrega el proveedor de identidades de la cuenta de Microsoft:

> [!NOTE]
>
>Si copió anteriormente el elemento `<UserJourneys>` del archivo base de la directiva al archivo de extensión `TrustFrameworkExtensions.xml`, puede omitir esta sección.

1.  Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2.  Busque el elemento `<UserJourneys>` y copie el contenido entero del nodo `<UserJourneys>`.
3.  Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
4.  Pegue el contenido entero del nodo `<UserJournesy>` que copió como secundario del elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualización del botón
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden.  El elemento `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades de una página de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para la cuenta de Microsoft, se muestra un nuevo botón cuando un usuario llega a la página. Para agregar este elemento:

1.  Busque el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.
2.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
3.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción
Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de Microsoft para recibir un token. Vincule el botón a una acción mediante la vinculación del perfil técnico del proveedor de notificaciones de la cuenta de Microsoft:

1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Asegúrese de que el valor de `Id` sea el mismo que el de `TargetClaimsExchangeId` en la sección anterior.
>   * Asegúrese de que el id. de `TechnicalProfileReferenceId` se establezca en el perfil técnico que creó anteriormente (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Abra la hoja **Todas las directivas**.
4.  Seleccione **Cargar directiva**.
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="test-the-custom-policy-by-using-run-now"></a>Probar la directiva personalizada con Ejecutar ahora

1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
> [!NOTE]
>
>**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.
2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registro del proveedor de notificaciones de la cuenta de Microsoft en el recorrido del usuario de edición de perfil
Puede que también quiera agregar el proveedor de identidades de la cuenta de Microsoft al recorrido del usuario `ProfileEdit` de su usuario. Para que esté disponible, se repiten los dos últimos pasos:

### <a name="display-the-button"></a>Visualización del botón
1.  Abra el archivo de extensión de su directiva (por ejemplo, TrustFrameworkExtensions.xml).
2.  Busque el nodo `<UserJourney>` que incluye `Id="ProfileEdit"` en el recorrido del usuario que ha copiado.
3.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
4.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción
1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Prueba de la directiva de edición de perfil personalizada mediante Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
2.  Abra **B2C_1A_ProfileEdit**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de Microsoft.

## <a name="download-the-complete-policy-files"></a>Descarga de los archivos de directiva completos
Opcional: se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
