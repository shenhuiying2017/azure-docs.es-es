---
title: "Azure Active Directory B2C: modificación del registro en las directivas personalizadas y configuración de un proveedor autoafirmado"
description: "Un tutorial sobre cómo agregar notificaciones para registrarse y configurar la entrada del usuario"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: tbd
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.openlocfilehash: e9eb9fa941569c508c4dddc6b85786537a5a0fac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: modificación del registro para agregar nuevas notificaciones y configuración de la entrada del usuario.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo, se agregará una nueva entrada (una notificación) por parte del usuario a un recorrido del usuario de la suscripción.  La entrada se configurará como una lista desplegable y se definirá si es necesario.

## <a name="prerequisites"></a>Requisitos previos

* Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).  Pruebe el recorrido del usuario de suscripción o inicio de sesión para registrar una nueva cuenta local antes de continuar.


La recopilación de los datos iniciales de los usuarios se logra a través de la suscripción o del inicio de sesión.  Posteriormente se pueden recopilar más notificaciones a través de los recorridos de los usuarios de edición de perfil. Cada vez que Azure AD B2C recoge información directamente del usuario de forma interactiva, el marco de la experiencia de identidad usa `selfasserted provider`. Los pasos siguientes se aplican siempre que se usa este proveedor.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Definición de la notificación, su nombre para mostrar y el tipo de entrada del usuario
Vamos a pedir al usuario que indique su ciudad.  Agregue el siguiente elemento al elemento `<ClaimsSchema>` del archivo de directiva TrustFrameworkBase:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Aquí puede elegir otras opciones para personalizar la notificación.  Para ver todo el esquema, consulte la guía **Identity Experience Framework Technical Reference Guide** (Guía de referencia técnica del marco de experiencia de identidad).  Esta guía se publicará pronto en la sección de referencias.

* `<DisplayName>` es una cadena que define la *etiqueta* a nivel de usuario

* `<UserHelpText>` ayuda al usuario a saber lo que se requiere

* `<UserInputType>` tiene las cuatro opciones siguientes resaltadas:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration`: exige una sola selección.
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect`: permite la selección del único valor válido.

![Captura de pantalla de la opción de la lista desplegable](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect` Permite la selección de uno o varios valores.

![Captura de pantalla de una opción con selección múltiple](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Adición de la notificación al recorrido del usuario de registro o inicio de sesión

1. Agregue la notificación como `<OutputClaim ClaimTypeReferenceId="city"/>` a TechnicalProfile `LocalAccountSignUpWithLogonEmail` (que se encuentra en el archivo de directiva TrustFrameworkBase).  Tenga en cuenta que TechnicalProfile utiliza SelfAssertedAttributeProvider.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. Agregue la notificación a AAD-UserWriteUsingLogonEmail como `<PersistedClaim ClaimTypeReferenceId="city" />` para escribir la notificación en el directorio de AAD después de recopilarla del usuario. Este paso se puede omitir si se prefiere no conservar la notificación en el directorio para usarla en el futuro.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. Agregue la notificación a la instancia de TechnicalProfile que lee del directorio cuando un usuario inicia sesión como `<OutputClaim ClaimTypeReferenceId="city" />`

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. Agregue `<OutputClaim ClaimTypeReferenceId="city" />` al archivo de directiva de RP SignUporSignIn.xml, con el fin de que esta notificación se envíe a la aplicación en el token después de un recorrido correcto del usuario.

  ```xml
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>Prueba de la directiva personalizada con "Ejecutar ahora"

1. Abra la **hoja de Azure AD B2C** y vaya a **Marco de experiencia de identidad > Directivas personalizadas**.
2. Seleccione la directiva personalizada que cargó y, luego, haga clic en el botón **Ejecutar ahora**.
3. Debe poder registrarse con una dirección de correo electrónico.

La pantalla de registro en modo de prueba debe parecerse a la siguiente:

![Captura de pantalla de la opción de registro modificada](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  El token que vuelve a la aplicación incluirá la notificación `city`, como se muestra a continuación
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>Eliminación opcional de la comprobación del correo electrónico en el recorrido del registro

Para omitir la comprobación del correo electrónico, el autor de la directiva puede elegir quitar `PartnerClaimType="Verified.Email"`. La dirección de correo electrónico será necesaria pero no se comprobará, salvo que se quite "Required" = true.  Considere cuidadosamente si esta opción es adecuada para sus casos de uso.

El correo electrónico comprobado está habilitado de forma predeterminada en el `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` del archivo de directiva TrustFrameworkBase del módulo de inicio:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Pasos siguientes

Agregue la nueva notificación a los flujos de inicios de sesión de cuentas sociales cambiando las instancias de TechnicalProfiles que se enumeran a continuación. Estas dos instancias se usan en los inicios de sesión de cuentas sociales o federadas para leer y escribir los datos de usuario con alternativeSecurityId como localizador.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```
