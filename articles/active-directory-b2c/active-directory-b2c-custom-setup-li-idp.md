---
title: "Azure Active Directory B2C: adición de LinkedIn como proveedor de identidades de OAuth2 mediante directivas personalizadas"
description: "Un artículo con instrucciones sobre cómo configurar la aplicación de LinkedIn con las directivas personalizadas y de protocolo de OAuth2"
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adición de LinkedIn como proveedor de identidades mediante directivas personalizadas
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para usuarios de la cuenta de LinkedIn mediante [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos
Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:
1.  Crear una aplicación de la cuenta de LinkedIn.
2.  Agregar la clave de aplicación de la cuenta de LinkedIn a Azure AD B2C
3.  Agregar un proveedor de notificaciones a una directiva
4.  Registrar el proveedor de notificaciones de la cuenta de LinkedIn en un recorrido del usuario
5.  Cargar la directiva en un inquilino de Azure AD B2C y probarla

## <a name="step-1-create-a-linkedin-account-application"></a>Paso 1: Creación de una aplicación de la cuenta de LinkedIn
Para usar LinkedIn como proveedor de identidades en Azure Active Directory (Azure AD) B2C, primero debe crear una aplicación de LinkedIn y suministrarle los parámetros correctos. Puede registrar una aplicación de LinkedIn aquí: [https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  Navegue hasta el sitio web [administración de aplicaciones de LinkedIn](https://www.linkedin.com/secure/developer?newapp=), inicie sesión con sus credenciales de cuenta de LinkedIn y haga clic en **Crear aplicación**.

    ![Cuenta de LinkedIn: Crear aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  Escriba el **Nombre de la compañía**, proporcione un **Nombre** descriptivo y una **Descripción** para la nueva aplicación.
    2.  Cargar **Logotipo de la aplicación**
    3.  Seleccionar **Uso de la aplicación**
    4.  Pegue en `https://login.microsoftonline.com` para el valor **URL del sitio web**.
    5.  Escriba su **correo electrónico del trabajo** y el **teléfono del trabajo**
    6.  En la parte inferior de la página, lea y acepte los términos de uso. Haga clic en **Enviar**

    ![Cuenta de LinkedIn: Configurar propiedades de la aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  En el menú, haga clic en **Autenticación**. Tome nota de los valores de **identificación de cliente** y **secreto de cliente**.

    Para **redirigir las URL autorizadas**, pegue `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Reemplace {tenant} por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). y asegúrese de que está utilizando el esquema HTTPS y haga clic en **Agregar**

    ![Cuenta de LinkedIn: Establecer la redirección de URL autorizadas](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

4.  Haga clic en la pestaña **Configuración**, cambie el **Estado de la aplicación** a **Live**, a continuación, haga clic en **Actualización**.

    ![Cuenta de LinkedIn: Establecer el estado de la aplicación](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Paso 2: Adición de la clave de aplicación de la cuenta de LinkedIn a Azure AD B2C
La federación con cuentas de LinkedIn requiere un secreto de cliente para que la cuenta de LinkedIn confíe en Azure AD B2C en nombre de la aplicación. El secreto de aplicación de LinkedIn debe almacenarse en su inquilino de Azure AD B2C:  

1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Manual**.
5.  En **Nombre**, use `LinkedInSecret`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En el cuadro **Secreto**, escriba el secreto de aplicación de LinkedIn desde https://apps.dev.microsoft.com
7.  En **Uso de claves**, use **Cifrado**.
8.  Haga clic en **Crear** 
9.  Confirme que ha creado la clave `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Paso 3: Adición de un proveedor de notificaciones en la directiva de extensión
Si quiere que los usuarios inicien sesión mediante la cuenta de LinkedIn, debe definir esta cuenta como un proveedor de notificaciones. En otras palabras, debe especificar los puntos de conexión con los que Azure AD B2C se comunica. Los puntos de conexión proporcionan un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Defina la cuenta de LinkedIn como proveedor de notificaciones; para ello, agregue el nodo `<ClaimsProvider>` en el archivo de directiva de extensión:

1.  Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. 
2.  Busque la sección `<ClaimsProviders>`.
3.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviders>`:  

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

4.  Reemplace el valor `client_id` por el identificador de cliente de aplicación de LinkedIn
5.  Guarde el archivo .

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Paso 4: Registro del proveedor de notificaciones de la cuenta de LinkedIn para el recorrido del usuario de registro o inicio de sesión
En este momento, el proveedor de identidades se ha configurado. Sin embargo, no está disponible en ninguna de las pantallas de registro o inicio de sesión. Ahora tiene que agregar el proveedor de identidades de la cuenta de LinkedIn al recorrido del usuario `SignUpOrSignIn` de su usuario.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Paso 4.1: Copia del recorrido del usuario
Para que esté disponible, se crea un duplicado de un recorrido del usuario de plantilla ya existente. A continuación, agregue el proveedor de identidad de LinkedIn:

> [!NOTE]
>
>Si ha copiado el elemento `<UserJourneys>` del archivo base de su directiva en el archivo de extensión (TrustFrameworkExtensions.xml), puede omitir esta sección.

1.  Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2.  Busque el elemento `<UserJourneys>` y copie el contenido entero del nodo `<UserJourneys>`.
3.  Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
4.  Pegue el contenido entero del nodo `<UserJournesy>` que copió como secundario del elemento `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Paso 4.2: Visualización del "botón"
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden.  El elemento `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades de una página de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para la cuenta de LinkedIn, se muestra un nuevo botón cuando un usuario llega a la página. Para agregar este elemento:

1.  Busque el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.
2.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
3.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>Paso 4.3: Vinculación del botón a una acción
Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de LinkedIn para recibir un token. Vincule el botón a una acción mediante la vinculación del perfil técnico del proveedor de notificaciones de la cuenta de LinkedIn:

1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * Asegúrese de que el valor de `Id` sea el mismo que el de `TargetClaimsExchangeId` en la sección anterior.
    > * Asegúrese de que el identificador de `TechnicalProfileReferenceId` se establece en el perfil técnico que creó anteriormente (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Paso 5: Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y haga clic en **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Haga clic en **Todas las directivas**.
4.  Seleccione **Cargar directiva**.
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Paso 6: Prueba de la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.

    > [!NOTE]
    >
    >**Ejecutar ahora** Requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>Paso 7: (Opcional) Registro del proveedor de notificaciones de la cuenta de LinkedIn en el recorrido del usuario de edición de perfil
Puede que también desee agregar el proveedor de identidades de la cuenta de LinkedIn al recorrido del usuario `ProfileEdit` de su usuario. Para que esté disponible, repita el paso número 4. Esta vez, seleccione el nodo `<UserJourney>` que incluye `Id="ProfileEdit"`. Guarde, cargue y pruebe la directiva.

## <a name="download-the-complete-policy-files"></a>Descarga de los archivos de directiva completos
Opcional: se recomienda que, en lugar de usar estos archivos de ejemplo, cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas.  [Archivos de directiva de ejemplo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)