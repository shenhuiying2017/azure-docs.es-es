---
title: "Azure Active Directory B2C: adición de Twitter como un proveedor de identidades de OAuth1 mediante directivas personalizadas"
description: Uso de Twitter como proveedor de identidades mediante el protocolo OAuth1
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
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 629e0bbaa7c62ef5d381085588c6a99c203c41cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: adición de Twitter como un proveedor de identidades de OAuth1 mediante directivas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para los usuarios de una cuenta de Twitter mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos del artículo [Azure Active Directory B2C: introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-twitter-account-application"></a>Paso 1: Creación de una aplicación de la cuenta de Twitter
Para usar Twitter como proveedor de identidades en Azure Active Directory B2C (Azure AD B2C), debe crear una aplicación de Twitter y suministrarle los parámetros correctos. Para registrar una aplicación de Twitter, vaya a la [página de registro de Twitter](https://twitter.com/signup).

1. Vaya al sitio web para [desarrolladores de Twitter](https://apps.twitter.com/), inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Crear nueva aplicación**.

    ![Cuenta de Twitter: crear nueva aplicación](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. En la ventana **Create an application** (Crea una aplicación), haga lo siguiente:
 
    a. Escriba el **nombre** y una **descripción** de la nueva aplicación. 

    b. En el cuadro **Website** (Sitio web), pegue **https://login.microsoftonline.com**. 

    c. En el cuadro **Callback URL** (URL de devolución de llamada), pegue **https://login.microsoftonline.com/te/ {tenant}.onmicrosoft.com/oauth2/authresp**. Reemplace {*tenant*} por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Asegúrese de que está utilizando el esquema HTTPS. 

    d. En la parte inferior de la página, lea y acepte los términos y, luego, haga clic en **Create your Twitter application** (Crear su aplicación de Twitter).

    ![Cuenta de Twitter: agregar una nueva aplicación](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. En la ventana **B2C demo** (Demostración de B2C), seleccione **Settings** (Configuración), active la casilla **Allow this application to be used to sign in with Twitter** (Permitir usar esta aplicación para iniciar sesión en Twitter) y, después, haga clic en **Update Settings** (Actualizar configuración).

4. Seleccione **Keys and Access Tokens** (Claves y tokens de acceso) y anote los valores de **Consumer Key (API Key)** [Clave de consumidor (clave de API)] y **Consumer Secret (API Secret)** [Secreto de consumidor (secreto de API)].

    ![Cuenta de Twitter: establecer propiedades de la aplicación](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Paso 2: Adición de la clave de aplicación de la cuenta de Twitter a Azure AD B2C
La federación con cuentas de Twitter requiere un secreto de consumidor para que la cuenta de Twitter confíe en Azure AD B2C en nombre de la aplicación. Para almacenar el secreto de consumidor de la aplicación de Twitter en el inquilino de Azure AD B2C, siga estos pasos: 

1. En su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.

2. Para ver las claves disponibles en el inquilino, seleccione **Policy Keys** (Claves de directiva).

3. Seleccione **Agregar**.

4. En el cuadro **Opciones**, seleccione **Manual**.

5. En el cuadro **Nombre**, seleccione **TwitterSecret**.  
    Es posible que se agregue automáticamente el prefijo *B2C_1A_*.

6. En el cuadro **Secreto**, escriba el secreto de la aplicación de Microsoft desde el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com).

7. En **Uso de claves**, use **Cifrado**.

8. Seleccione **Crear**.

9. Confirme que ha creado la clave `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Paso 3: Adición de un proveedor de notificaciones en la directiva de extensión

Si desea que los usuarios inicien sesión mediante la cuenta de Twitter, debe definir Twitter como proveedor de notificaciones. En otras palabras, debe especificar los puntos de conexión con los que Azure AD B2C se comunica. Los puntos de conexión proporcionan un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Para definir Twitter como proveedor de notificaciones, agregue el nodo `<ClaimsProvider>` al archivo de la directiva de extensión:

1. Abra el archivo de directiva de extensión *TrustFrameworkExtensions.xml* desde el directorio de trabajo. 

2. Busque la sección `<ClaimsProviders>`.

3. En el nodo `<ClaimsProviders>`, agregue el siguiente fragmento de código XML:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Reemplace el valor de *client_id* por la clave de consumidor de la aplicación de la cuenta de Twitter.

5. Guarde el archivo .

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Paso 4: Registro del proveedor de notificaciones de la cuenta de Twitter para el recorrido del usuario de registro o inicio de sesión
Ha configurado el proveedor de identidades. Sin embargo, aún no está disponible en ninguna de las ventanas de registro o inicio de sesión. Ahora debe agregar el proveedor de identidades de la cuenta de Twitter al recorrido del usuario `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Paso 4.1: Copia del recorrido del usuario
Para que el recorrido del usuario esté disponible, cree un duplicado de una plantilla de recorrido del usuario existente y agregue el proveedor de identidades de Twitter:

>[!NOTE]
>Si ha copiado el elemento `<UserJourneys>` del archivo base de su directiva en el archivo de extensión *TrustFrameworkExtensions.xml*, puede pasar a la siguiente sección.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).

2. Busque el elemento `<UserJourneys>`, seleccione todo el contenido del nodo `<UserJourney>` y, después, seleccione **Cortar** para mover el texto seleccionado al Portapapeles.

3. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.

4. Pegue todo el contenido del nodo `<UserJourney>`, que movió al Portapapeles en el paso 2, en el elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Paso 4.2: Visualización del "botón"
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden. El nodo `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades en una página de registro o de inicio de sesión. Si agrega un nodo `<ClaimsProviderSelection>` para una cuenta de Twitter, cuando un usuario llega a la página se muestra un botón nuevo. Para agregar este elemento, siga estos pasos:

1. Busque el nodo `<UserJourney>` que contenga `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.

2. Busque el nodo `<OrchestrationStep>` que contiene `Order="1"`.

3. En el elemento `<ClaimsProviderSelections>`, agregue el siguiente fragmento de código XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Paso 4.3: Vinculación del botón a una acción
Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de Twitter para recibir un token. Vincule el botón a una acción mediante la vinculación del perfil técnico del proveedor de notificaciones de la cuenta de Twitter:

1. Busque el nodo `<OrchestrationStep>` que contenga `Order="2"` en el nodo `<UserJourney>`.
2. En el elemento `<ClaimsExchanges>`, agregue el siguiente fragmento de código XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Asegúrese de que `Id` tiene el mismo valor que el de `TargetClaimsExchangeId` en la sección anterior.
    >* Asegúrese de que el identificador `TechnicalProfileReferenceId` se establece en el perfil técnico que creó anteriormente (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Paso 5: Carga de la directiva en el inquilino
1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y seleccione **Azure AD B2C**.

2. Seleccione **Marco de experiencia de identidad**.

3. Seleccione **Todas las directivas**.

4. Seleccione **Cargar directiva**.

5. Active la casilla **Sobrescribir la directiva si existe**.

6. Cargue los archivos *TrustFrameworkBase.xml* y *TrustFrameworkExtensions.xml* y asegúrese de que pasan la validación.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Paso 6: Prueba de la directiva personalizada con Ejecutar ahora

1. Seleccione **Configuración de Azure AD B2C** y, después, **Marco de experiencia de identidad**.

    >[!NOTE]
    >La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2. Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado, y seleccione **Ejecutar ahora**.  
    Debería poder iniciar sesión con la cuenta de Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Paso 7: (Opcional) Registro del proveedor de notificaciones de la cuenta de Twitter en el recorrido del usuario de edición de perfil
También puede agregar el proveedor de identidades de la cuenta de Twitter al recorrido del usuario `ProfileEdit`. Para que el recorrido del usuario esté disponible, repita el "paso 4". Esta vez, seleccione el nodo `<UserJourney>` que contenga`Id="ProfileEdit"`. Guarde, cargue y pruebe la directiva.


## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Descarga de los archivos de directiva completos
Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app) para que los tenga como referencia.
