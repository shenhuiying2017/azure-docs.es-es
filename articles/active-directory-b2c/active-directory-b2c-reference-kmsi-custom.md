---
title: 'Azure Active Directory B2C: KMSI| Microsoft Docs'
description: "Un tema que muestra cómo configurar \"mantener la sesión iniciada\""
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: a3d78945f862d1ae12cec05da0cf0ea7df511f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Habilitar "Mantener la sesión iniciada" (KMSI)  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C ahora permite a la web y a las aplicaciones nativas habilitar la funcionalidad "Mantener la sesión iniciada" (KMSI). Esta característica concede acceso para que los usuarios vuelvan a la aplicación sin solicitarles que vuelvan a escribir el nombre de usuario y la contraseña. Este acceso se revocará cuando el usuario cierre la sesión. 

Se recomienda no activar esta opción en equipos públicos. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Requisitos previos

Configuración de un inquilino de Azure AD B2C para permitir el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Cómo habilitar KMSI

Realice los cambios siguientes en la directiva de extensiones del marco de trabajo de confianza.

## <a name="adding-a-content-definition-element"></a>Adición de un elemento de la definición de contenido 

El nodo `BuildingBlocks` del archivo de extensión debe incluir un elemento `ContentDefinitions`. 

1. En la sección `ContentDefinitions`, defina un nuevo `ContentDefinition` con el identificador `api.signuporsigninwithkmsi`.
2. El nuevo `ContentDefinition` debe incluir un `LoadUri`, `RecoveryUri` y `DataUri` como se indica a continuación.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` es un identificador que una máquina puede comprender que abrirá una casilla para KMSI en las páginas de inicio de sesión. Asegúrese de no cambiar este valor. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Agregar un proveedor de notificaciones de inicio de sesión de cuenta local 

Puede definir Local Account SignIn como proveedor de notificaciones al nodo `<ClaimsProvider>` en el archivo de extensión de la directiva:

1. Abra el archivo de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. 
2. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
3. El módulo de inicio de [Introducción](active-directory-b2c-get-started-custom.md) viene con un proveedor de notificaciones "Local Account SignIn". 
4. Si no es así, agregue un nuevo nodo `<ClaimsProvider>` como se indica a continuación:

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
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

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Crear KMSI en el recorrido habilitado de usuario

Debe agregar el proveedor de notificaciones de Local Account SignIn al recorrido de usuario. 

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2. Busque el elemento `<UserJourneys>` y copie todo el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"`.
3. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
4. Pegue todo el nodo `<UserJourney>` que ha copiado como elemento secundario de `<UserJourneys>`.
5. Cambie el nombre del identificador del nuevo recorrido del usuario (por ejemplo, cambie el nombre a `SignUpOrSignInWithKmsi`).
6. Por último, en `OrchestrationStep 1` cambiar `ContentDefinitionReferenceId` a `api.signuporsigninwithkmsi`, se define en los pasos anteriores. Esto habilita la casilla en el recorrido de usuario. 
7. Ya ha terminado la modificación del archivo de extensión. Guarde y cargue este archivo. Asegúrese de que todas las validaciones se han realizado correctamente.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Crear un archivo (RP) de un usuario de confianza

Después, actualice el archivo del usuario de confianza (RP) que inicia el recorrido del usuario que ha creado:

1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo. Después, cambie el nombre (por ejemplo, SignUpOrSignInWithKmsi.xml).

2. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único. Este es el nombre de la directiva (por ejemplo, SignUpOrSignInWithKmsi).

3. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el `Id` del nuevo recorrido del usuario que ha creado (por ejemplo, SignUpOrSignInWithKmsi).

4. KMSI se configura en `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** controla cuánto tiempo permanece el usuario con la sesión iniciada. En el ejemplo siguiente, la sesión KMSI expira automáticamente transcurridos 14 días sin tener en cuenta la frecuencia con la que el usuario realiza la autenticación en modo silencioso.

   Establecer el valor `KeepAliveInDays` en 0 desactiva la funcionalidad KMSI. De manera predeterminada, este valor es 0

6. Si **`SessionExpiryType`** es *Gradual*, a continuación, la sesión KMSI se extiende a 14 días cada vez que el usuario realiza la autenticación en modo silencioso.  Si está seleccionado *Gradual*, se recomienda mantener el número de días al mínimo. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Guarde los cambios y después cargue el archivo.

8. Para probar la directiva personalizada que ha cargado, en Azure Portal, vaya a la hoja de la directiva y, después, haga clic en **Ejecutar ahora**.


## <a name="link-to-sample-policy"></a>Vincular a la directiva de ejemplo

Puede encontrar la directiva de ejemplo [aquí](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








