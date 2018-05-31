---
title: 'Adición de un proveedor de identidades multiinquilino de Azure AD mediante directivas personalizadas: Azure Active Directory B2C | Microsoft Docs'
description: 'Adición de un proveedor de identidades multiinquilino de Azure AD mediante directivas personalizadas: Azure Active Directory B2C'
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200525"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C permite a los usuarios iniciar sesión en un proveedor de identidades multiinquilino de Azure AD mediante directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión de los usuarios usando el punto de conexión de multiinquilino de Azure Active Directory (Azure AD) mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md). Esto permite a los usuarios de varios inquilinos de Azure AD iniciar sesión en Azure AD B2C sin tener que configurar un proveedor técnico para cada inquilino. Sin embargo, los miembros invitados en cualquiera de estos inquilinos **no** podrán iniciar sesión. Para ello, tendrá que [configurar individualmente cada inquilino](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> En las instrucciones siguientes, usamos "contoso.com" para el inquilino de Azure AD de la organización y "fabrikamb2c.onmicrosoft.com" como inquilino de Azure AD B2C.

## <a name="prerequisites"></a>requisitos previos

Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:
     
1. Creación de un inquilino de Azure Active Directory B2C (Azure AD B2C).
1. Creación de una aplicación de Azure AD B2C.    
1. Registro de dos aplicaciones de motor de directivas.  
1. Configuración de claves. 
1. Configurar el paquete de inicio.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Paso 1. Creación de una aplicación multiinquilino de Azure AD

Para habilitar el inicio de sesión de los usuarios mediante el punto de conexión multiinquilino de Azure AD, debe tener una aplicación multiinquilino registrada en uno de los inquilinos de Azure AD. En este artículo, le mostraremos cómo crear una aplicación multiinquilino de Azure AD en el inquilino de Azure AD B2C. Posteriormente, podrá habilitar el inicio de sesión de los usuarios mediante esa aplicación multiinquilino de Azure AD.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
1. En la barra superior, seleccione su cuenta. En la lista **Directorios**, elija el inquilino de Azure AD B2C para registrar la aplicación de Azure AD (fabrikamb2c.onmicrosoft.com).
1. Seleccione **Más servicios** en el panel izquierdo y busque "Registros de aplicaciones".
1. Seleccione **Nuevo registro de aplicaciones**.
1. Escriba el nombre de la aplicación (por ejemplo, `Azure AD B2C App`).
1. En Tipo de aplicación, seleccione **Aplicación web o API**.
1. En **Dirección URL de inicio de sesión**, escriba la dirección URL siguiente, donde `yourtenant` se sustituye por el nombre del inquilino de Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >El valor de "yourtenant" debe estar en minúsculas en **URL de inicio de sesión**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Guarde el identificador de la aplicación.
1. Seleccione la aplicación recién creada.
1. En la hoja **Configuración**, seleccione **Propiedades**.
1. Establezca **Multiinquilino** en **Sí**.
1. En la hoja **Configuración**, seleccione **Claves**.
1. Cree una clave y guárdela. La usará en los pasos de la sección siguiente.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Paso 2. Adición de la clave de Azure AD a Azure AD B2C

Deberá registrar la clave de aplicación en la configuración de Azure AD B2C. Para ello, siga estos pasos:

1. Vaya al menú de configuración de Azure AD B2C.
1. Haga clic en **Marco de experiencia de identidad** > **Claves de directiva**.
1. Seleccione **+Agregar**.
1. Seleccione o especifique estas opciones:
   * Seleccione **Manual**.
   * En **Nombre**, elija un nombre que coincida con el nombre del inquilino de Azure AD (por ejemplo, `AADAppSecret`).  Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
   * Pegue la clave de la aplicación en el cuadro **Secreto**.
   * Seleccione **Firma**.
1. Seleccione **Crear**.
1. Confirme que ha creado la clave `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Paso 3. Adición de un proveedor de notificaciones a una directiva de base

Si quiere que los usuarios inicien sesión con Azure AD, es preciso definir Azure AD como proveedor de notificaciones. En otras palabras, se debe especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión proporcionará un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. 

Puede definir Azure AD como proveedor de notificaciones. Para ello, agregue Azure AD al nodo `<ClaimsProvider>` en el archivo de extensión de la directiva:

1. Abra el archivo de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo.
1. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
1. Agregue un nuevo nodo `<ClaimsProvider>` como se indica a continuación:

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. En el nodo `<ClaimsProvider>`, actualice el valor de `<Domain>` a un valor único que pueda usarse para distinguirlo de otros proveedores de identidades.
1. En el nodo `<TechnicalProfile>`, actualice el valor de `<DisplayName>`. Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión.
1. Actualice el valor de `<Description>`.
1. Establezca `<Item Key="client_id">` en el identificador de aplicación desde el registro de la aplicación multiinquilino de Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Paso 3.1 Restricción del acceso a la lista específica de inquilinos de Azure AD

> [!NOTE]
> Si se usa `https://sts.windows.net` como el valor de **ValidTokenIssuerPrefixes**, se permitirá a todos los usuarios de Azure AD iniciar sesión en la aplicación.

Tiene que actualizar la lista de emisores de tokens válidos y restringir el acceso a la lista específica de inquilinos de Azure AD en los que los usuarios pueden iniciar sesión. Para obtener los valores, debe examinar los metadatos de cada uno de los inquilinos específicos de Azure AD desde los que le gustaría que los usuarios iniciaran sesión. El formato de los datos es similar al siguiente: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, donde `yourAzureADtenant` es el nombre del inquilino de Azure AD (contoso.com o cualquier otro inquilino de Azure AD).
1. Abra el explorador y vaya a la dirección URL de los metadatos.
1. En el explorador, busque el objeto "issuer" y copie su valor. Debería ser similar a lo siguiente: `https://sts.windows.net/{tenantId}/`.
1. Pegue el valor de la clave `ValidTokenIssuerPrefixes`. Puede agregar varios separándolos con una coma. Un ejemplo de esto se describe en el ejemplo de XML anterior.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Paso 4 Registro del proveedor de notificaciones de la cuenta de Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Paso 4.1: Copia del recorrido del usuario

Ahora es preciso agregar el proveedor de identidades de Azure AD a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión.

Para que esté disponible, crearemos un duplicado de un recorrido del usuario de plantilla existente y, después, lo modificaremos para que también tenga el proveedor de identidades de Azure AD:

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
1. Busque el elemento `<UserJourneys>` y copie todo el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"`.
1. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
1. Pegue todo el nodo `<UserJourney>` que ha copiado como elemento secundario de `<UserJourneys>`.
1. Cambie el nombre del identificador del nuevo recorrido del usuario (por ejemplo, cambie el nombre a `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Paso 4.2: Visualización del "botón"

El elemento `<ClaimsProviderSelection>` es análogo a un botón del proveedor de identidades en una pantalla de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para Azure AD, se mostrará un botón nuevo cuando un usuario llega a la página. Para agregar este elemento:

1. Busque el nodo `<OrchestrationStep>` que incluye `Order="1"` en el recorrido del usuario que ha creado.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Establezca `TargetClaimsExchangeId` en valor apropiado. Se recomienda seguir la misma convención que otros:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Paso 4.3: Vinculación del botón a una acción

Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Azure AD para recibir un token. Para vincular un botón a una acción, vincule el perfil técnico del proveedor de notificaciones de Azure AD:

1. Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Actualice `Id` con el mismo valor que el de `TargetClaimsExchangeId` en la sección anterior.
1. Actualice `TechnicalProfileReferenceId` con el identificador del perfil técnico que ha creado anteriormente (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Paso 5: Creación de una nueva directiva RP

Ahora es preciso actualizar el archivo del usuario de confianza (RP) que iniciará el recorrido del usuario que acaba de crear:
 
1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo y cambie su nombre (por ejemplo, SignUpOrSignInWithAAD.xml).  
1. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único (por ejemplo, SignUpOrSignInWithAAD). Este será el nombre de la directiva (por ejemplo, B2C\_1A\_SignUpOrSignInWithAAD). 
1. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el identificador del nuevo recorrido del usuario que ha creado (SignUpOrSignUsingAzureAD). 
1. Guarde los cambios y cargue el archivo. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Paso 6: Carga de la directiva en el inquilino

1. En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y seleccione **Azure AD B2C**.
1. Seleccione **Marco de experiencia de identidad**.
1. Seleccione **Todas las directivas**.
1. Seleccione **Cargar directiva**.
1. Active la casilla **Sobrescribir la directiva si existe**.
1. Cargue el archivo `TrustFrameworkExtensions.xml` y el archivo RP (por ejemplo, `SignUpOrSignInWithAAD.xml`) y asegúrese que pasan la validación.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Paso 7: Prueba de la directiva personalizada con Ejecutar ahora

1. Seleccione **Configuración de Azure AD B2C** y, después, **Marco de experiencia de identidad**.
    > [!NOTE]
    > La función Ejecutar ahora requiere al menos que una aplicación esté registrada previamente en el inquilino. Para obtener información sobre cómo registrar aplicaciones, vea el artículo de [introducción](active-directory-b2c-get-started.md) a Azure AD B2C o el artículo [Registro de la aplicación](active-directory-b2c-app-registration.md) de Azure AD B2C.

1. Abra la directiva personalizada del usuario de confianza (RP) que ha cargado, (*B2C\_1A\_SignUpOrSignInWithAAD*), y seleccione **Ejecutar ahora**.
1. Debería poder iniciar sesión con la cuenta de Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Opcional) Paso 8: Registro del proveedor de notificaciones de la cuenta de Azure AD en el recorrido del usuario de edición de perfil

También puede agregar el proveedor de identidades de la cuenta de Azure AD al recorrido del usuario `ProfileEdit`. Para que el recorrido del usuario esté disponible, repita los pasos 4 a 6. Esta vez, seleccione el nodo `<UserJourney>` que contenga`Id="ProfileEdit"`. Guarde, cargue y pruebe la directiva.

## <a name="troubleshooting"></a>solución de problemas

Para diagnosticar problemas, obtenga información sobre la [solución de problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes

Envíe sus comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
