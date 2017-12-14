---
title: "Azure Active Directory B2C: Cambio de contraseña de autoservicio | Microsoft Docs"
description: "Tema en el que se demuestra cómo configurar el cambio de contraseña de autoservicio para los consumidores en Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
ms.assetid: 712a7128-5788-4914-8a52-24e200aa4de1
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: 76e7ed328716d09dc57e25f15c411f07fda77bb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: Configuración del cambio de contraseña en las directivas personalizadas  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Con la característica de cambio de contraseña, los consumidores con sesión iniciada (mediante cuentas locales) pueden cambiar sus contraseñas sin tener que demostrar su autenticidad por comprobación de correo electrónico como se describe en el [flujo de restablecimiento de contraseña de autoservicio](active-directory-b2c-reference-sspr.md). Si la sesión expira cuando el consumidor recibe el flujo de cambio de contraseña, se solicita al usuario que inicie sesión de nuevo. 

## <a name="prerequisites"></a>Requisitos previos

Configuración de un inquilino de Azure AD B2C para completar el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Procedimientos para la configuración del cambio de contraseñas en las directivas personalizadas

Para configurar el cambio de contraseña en la directiva personalizada, realice los siguientes cambios en la directiva de extensiones del marco de confianza 

## <a name="define-a-claimtype-oldpassword"></a>Definir un ClaimType 'oldPassword'

La estructura general de la directiva personalizada debe incluir un `ClaimsSchema` y definir una nueva `ClaimType` 'oldPassword' como sigue 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

El propósito de estos elementos es el siguiente:

- El elemento `ClaimsSchema` define cuál es la notificación que se está validando.  En este caso, se validará la contraseña antigua. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Agregar un proveedor de notificaciones de cambio de contraseña con sus elementos auxiliares

El proveedor de notificaciones de cambio de contraseña

1. Autenticará al usuario con la contraseña antigua
2. Y si "nueva contraseña" coincide con "Confirmar nueva contraseña", este valor se almacena en el almacén de datos de B2C y, por tanto, la contraseña se cambia correctamente. 

![img](images/passwordchange.jpg)

Agregue el siguiente proveedor de notificaciones a la directiva de extensiones. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>Incorporación de identificadores de aplicación a la directiva personalizada

Agregue los identificadores de aplicación al archivo de extensiones (`TrustFrameworkExtensions.xml`):

1. En el archivo de extensiones (TrustFrameworkExtensions.xml) busque el elemento `<TechnicalProfile Id="login-NonInteractive">` y `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Reemplace todas las instancias de `IdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad como se describe en la [Introducción](active-directory-b2c-get-started-custom.md). Aquí tiene un ejemplo:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Reemplace todas las instancias de `ProxyIdentityExperienceFrameworkAppId` por el identificador de la aplicación del marco de experiencia de identidad del proxy como se describe en la [Introducción](active-directory-b2c-get-started-custom.md).

4. Guarde el archivo de extensiones.



## <a name="create-a-password-change-user-journey"></a>Cree un recorrido de usuario de cambio de contraseña

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

Ya ha terminado la modificación del archivo de extensión. Guarde y cargue este archivo. Asegúrese de que todas las validaciones se han realizado correctamente.



## <a name="create-a-relying-party-rp-file"></a>Crear un archivo (RP) de un usuario de confianza

Después, actualice el archivo del usuario de confianza (RP) que inicia el recorrido del usuario que ha creado:

1. Realice una copia de ProfileEdit.xml en el directorio de trabajo. A continuación, cámbiela de nombre (por ejemplo, PasswordChange.xml).
2. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único. Este es el nombre de la directiva (por ejemplo, PasswordChange).
3. Modifique el atributo `ReferenceId` en `<DefaultUserJourney>` para que coincida con el `Id` del nuevo recorrido del usuario que ha creado (por ejemplo, PasswordChange).
4. Guarde los cambios y después cargue el archivo.
5. Para probar la directiva personalizada que ha cargado, en Azure Portal, vaya a la hoja de la directiva y, después, haga clic en **Ejecutar ahora**.




## <a name="link-to-password-change-sample-policy"></a>Vincule la directiva de ejemplo de cambio de contraseña

Puede encontrar la directiva de ejemplo [aquí](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










