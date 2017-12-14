---
title: "Azure Active Directory B2C: adición de LinkedIn como proveedor de identidades de OAuth2 mediante directivas personalizadas"
description: "Un artículo con instrucciones para configurar una aplicación de LinkedIn mediante las directivas personalizadas y el protocolo OAuth2"
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
ms.openlocfilehash: 77e2b9b283e4051370ffb905681135c27512834e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: adición de LinkedIn como proveedor de identidades mediante directivas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para los usuarios de una cuenta de LinkedIn mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos del artículo [Azure Active Directory B2C: introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-linkedin-account-application"></a>Paso 1: Creación de una aplicación de la cuenta de LinkedIn
Para usar LinkedIn como proveedor de identidades en Azure Active Directory B2C (Azure AD B2C), es preciso crear una aplicación de LinkedIn y suministrarle los parámetros correctos. Para registrar una aplicación de LinkedIn, vaya a la [página de registro de LinkedIn](https://LinkedIn.com/signup).

1. Vaya el sitio web de [administración de aplicaciones de LinkedIn](https://www.linkedin.com/secure/developer?newapp=), inicie sesión con las credenciales de su cuenta de LinkedIn y haga clic en **Crear aplicación**.

    ![Cuenta de LinkedIn: Crear aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. En la página **Create a New Application** (Crea una nueva aplicación), haga lo siguiente:

    a. Rellene los campos **Company Name** (Empresa), **Name** (Nombre) y **Description** (Descripción) de la nueva aplicación.

    b. Cargue el **logotipo de la aplicación**.

    c. Seleccione un **uso de la aplicación**.

    d. En el cuadro **Website URL** (URL del sitio web), pegue **https://login.microsoftonline.com**.

    e. Escriba la dirección de **correo electrónico de la empresa** y el número de **teléfono de la empresa**.

    f. En la parte inferior de la página, lea y acepte los términos de uso, y haga clic en **Submit** (Enviar).

    ![Cuenta de LinkedIn: Configurar propiedades de la aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Seleccione **Authentication** (Autenticación) y, después, anote los valores de **Client ID** (ID del cliente) y **Client Secret** (Secreto del cliente).

4. En el cuadro **Authorized Redirect URLs** (URL de redirección autorizadas), pegue **https://login.microsoftonline.com/te/ {tenant}.onmicrosoft.com/oauth2/authresp**. Reemplace {*tenant*} por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Asegúrese de que está utilizando el esquema HTTPS. 

    ![Cuenta de LinkedIn: Establecer la redirección de URL autorizadas](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

5. Seleccione **Agregar**.

6. Seleccione **Settings** (Configuración), cambie el valor de **Application status** (Estado de la aplicación) a **Live** (Activada) y, después, seleccione **Update** (Actualizar).

    ![Cuenta de LinkedIn: Establecer el estado de la aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Paso 2: Adición de la clave de aplicación de la cuenta de LinkedIn a Azure AD B2C
La federación con cuentas de LinkedIn requiere un secreto de cliente para que la cuenta de LinkedIn confíe en Azure AD B2C en nombre de la aplicación. Para almacenar el secreto de aplicación de LinkedIn en su inquilino de Azure AD B2C, siga estos pasos:  

1. En su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.

2. Para ver las claves disponibles en el inquilino, seleccione **Policy Keys** (Claves de directiva).

3. Seleccione **Agregar**.

4. En el cuadro **Opciones**, seleccione **Cargar**.

5. En el cuadro **Nombre**, escriba **B2cRestClientCertificate**.  
    Es posible que se agregue automáticamente el prefijo *B2C_1A_*.

6. En el cuadro **Secreto**, escriba el secreto de la aplicación de LinkedIn desde el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com).

7. En **Uso de la clave**, seleccione **Cifrado**.

8. Seleccione **Crear**. 

9. Confirme que ha creado la clave `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Paso 3: Adición de un proveedor de notificaciones en la directiva de extensión
Si desea que los usuarios inicien sesión mediante su cuenta de LinkedIn, debe definir LinkedIn como proveedor de notificaciones. En otras palabras, debe especificar los puntos de conexión con los que Azure AD B2C se comunica. Los puntos de conexión proporcionan un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Para definir LinkedIn como proveedor de notificaciones, agregue un nodo `<ClaimsProvider>` en el archivo de la directiva de extensión:

1. Abra el archivo de directiva de extensión *TrustFrameworkExtensions.xml* desde el directorio de trabajo. 

2. Busque el elemento `<ClaimsProviders>`.

3. En el elemento `<ClaimsProviders>`, agregue el siguiente fragmento de código XML: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Reemplace el valor *client_id* el identificador de cliente de la aplicación de LinkedIn.

5. Guarde el archivo .

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Paso 4: Registro del proveedor de notificaciones de la cuenta de LinkedIn
Ha configurado el proveedor de identidades. Sin embargo, aún no está disponible en ninguna de las ventanas de registro o inicio de sesión. Ahora tiene que agregar el proveedor de identidades de la cuenta de LinkedIn al recorrido del usuario `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Paso 4.1: Copia del recorrido del usuario
Para que el recorrido del usuario esté disponible, cree un duplicado de una plantilla de recorrido del usuario existente y, después, agregue el proveedor de identidades de LinkedIn:

>[!NOTE]
>Si ha copiado el elemento `<UserJourneys>` del archivo base de su directiva en el archivo de extensión *TrustFrameworkExtensions.xml*, puede omitir esta sección.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).

2. Busque el elemento `<UserJourneys>`, seleccione todo el contenido del nodo `<UserJourney>` y, después, seleccione **Cortar** para mover el texto seleccionado al Portapapeles.

3. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.

4. Pegue todo el contenido del nodo `<UserJourney>`, que movió al Portapapeles en el paso 2, en el elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Paso 4.2: Visualización del "botón"
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden. El nodo `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades en una página de registro o de inicio de sesión. Si agrega un nodo `<ClaimsProviderSelection>` para una cuenta de LinkedIn, cuando un usuario llega a la página se muestra un botón nuevo. Para agregar este elemento, siga estos pasos:

1. Busque el nodo `<UserJourney>` que contenga `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.

2. Busque el nodo `<OrchestrationStep>` que incluya `Order="1"`.

3. En el elemento `<ClaimsProviderSelections>`, agregue el siguiente fragmento de código XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Paso 4.3: Vinculación del botón a una acción
Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de LinkedIn para recibir un token. Vincule el botón a una acción mediante la vinculación del perfil técnico del proveedor de notificaciones de la cuenta de LinkedIn:

1. Busque el nodo `<OrchestrationStep>` que contenga `Order="2"` en el nodo `<UserJourney>`.

2. En el elemento `<ClaimsExchanges>`, agregue el siguiente fragmento de código XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Asegúrese de que `Id` tiene el mismo valor que el de `TargetClaimsExchangeId` en la sección anterior.
    >* Asegúrese de que el identificador `TechnicalProfileReferenceId` se establece en el perfil técnico que creó anteriormente (LinkedIn-OAuth).

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
    Ya debería poder iniciar sesión con la cuenta de LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Paso 7: (Opcional) Registro del proveedor de notificaciones de la cuenta de LinkedIn en el recorrido del usuario de edición de perfil
También puede agregar el proveedor de identidades de la cuenta de LinkedIn al recorrido del usuario `ProfileEdit`. Para que el recorrido del usuario esté disponible, repita el "paso 4". Esta vez, seleccione el nodo `<UserJourney>` que contenga`Id="ProfileEdit"`. Guarde, cargue y pruebe la directiva.

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Descarga de los archivos de directiva completos
Una vez completado el tutorial [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md), le recomendamos que compile su escenario mediante sus archivos de directiva personalizados. Hemos proporcionado [archivos de directiva de ejemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app) para que los tenga como referencia.
