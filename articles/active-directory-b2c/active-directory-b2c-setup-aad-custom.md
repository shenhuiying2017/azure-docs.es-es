---
title: 'Azure Active Directory B2C: agregar un proveedor de Azure AD mediante directivas personalizadas | Microsoft Docs'
description: "Obtenga información sobre las directivas personalizadas de Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c073d70debfdc3560405955d65fa9ccaa7d8b1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: inicio de sesión con cuentas de Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión de los usuarios de una organización de Azure Active Directory (Azure AD) concreta mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos

Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:

1. Creación de un inquilino de Azure Active Directory B2C (Azure AD B2C).
2. Creación de una aplicación de Azure AD B2C.
3. Registro de dos aplicaciones de motor de directivas.
4. Configuración de claves.
5. Configurar el paquete de inicio.

## <a name="create-an-azure-ad-app"></a>Creación de una aplicación de Azure AD

Para habilitar el inicio de sesión para los usuarios de una organización específica de Azure AD, es preciso registrar una aplicación en el inquilino de Azure AD de la organización.

>[!NOTE]
> En las instrucciones siguientes, usamos "contoso.com" para el inquilino de Azure AD de la organización y "fabrikamb2c.onmicrosoft.com" como inquilino de Azure AD B2C.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1. En la barra superior, seleccione su cuenta. En la lista **Directorio**, elija el inquilino de Azure AD de la organización donde quiere registrar la aplicación (contoso.com).
1. Seleccione **Más servicios** en el panel izquierdo y busque "Registros de aplicaciones".
1. Seleccione **Nuevo registro de aplicaciones**.
1. Escriba el nombre de la aplicación (por ejemplo, `Azure AD B2C App`).
1. En Tipo de aplicación, seleccione **Aplicación web o API**.
1. En **Dirección URL de inicio de sesión**, escriba la dirección URL siguiente, donde `yourtenant` se sustituye por el nombre del inquilino de Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Guarde el identificador de la aplicación.
1. Seleccione la aplicación recién creada.
1. En la hoja **Configuración**, seleccione **Claves**.
1. Cree una clave y guárdela. La usará en los pasos de la sección siguiente.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adición de la clave de Azure AD a Azure AD B2C

Debe almacenar la clave de aplicación contoso.com en su inquilino de Azure AD B2C. Para ello, siga estos pasos:
1. Vaya a su inquilino de Azure AD B2C y abra **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad** > **Claves de directiva**.
1. Seleccione **+Agregar**.
1. Seleccione o especifique estas opciones:
   * Seleccione **Manual**.
   * En **Nombre**, elija un nombre que coincida con el nombre del inquilino de Azure AD (por ejemplo, `ContosoAppSecret`).  Se agregará el prefijo `B2C_1A_` automáticamente al nombre de la clave.
   * Pegue la clave de la aplicación en el cuadro **Secreto**.
   * Seleccione **Firma**.
1. Seleccione **Crear**.
1. Confirme que ha creado la clave `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Adición de un proveedor de notificaciones a una directiva de base

Si quiere que los usuarios inicien sesión con Azure AD, es preciso definir Azure AD como proveedor de notificaciones. En otras palabras, se debe especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión proporcionará un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. 

Puede definir Azure AD como proveedor de notificaciones. Para ello, agregue Azure AD al nodo `<ClaimsProvider>` en el archivo de extensión de la directiva:

1. Abra el archivo de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo.
1. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
1. Agregue un nuevo nodo `<ClaimsProvider>` como se indica a continuación:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. En el nodo `<ClaimsProvider>`, actualice el valor de `<Domain>` a un valor único que pueda usarse para distinguirlo de otros proveedores de identidades.
1. En el nodo `<ClaimsProvider>`, actualice el valor de `<DisplayName>` a un nombre descriptivo del proveedor de notificaciones. Este valor no se utiliza actualmente.

### <a name="update-the-technical-profile"></a>Actualización del perfil técnico

Para obtener un token del punto de conexión de Azure AD es preciso definir los protocolos que Azure AD B2C debe usar para comunicarse con Azure AD. Esto se hace dentro del elemento `<TechnicalProfile>` de `<ClaimsProvider>`.
1. Actualice el identificador del nodo `<TechnicalProfile>`. Este identificador se usa para hacer referencia a este perfil técnico desde otras partes de la directiva.
1. Actualice el valor de `<DisplayName>`. Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión.
1. Actualice el valor de `<Description>`.
1. Azure AD usa el protocolo OpenID Connect, por lo que debe asegurarse de que el valor de `<Protocol>` es `"OpenIdConnect"`.

Debe actualizar la sección `<Metadata>` del archivo XML al que se ha hecho referencia anteriormente para reflejar la configuración de su inquilino de Azure AD específico. En el archivo XML, actualice los valores de los metadatos como se indica a continuación:

1. Establezca `<Item Key="METADATA">` en `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, donde `yourAzureADtenant` es el nombre del inquilino de Azure AD (contoso.com).
1. Abra el explorador y vaya a la dirección URL `METADATA` que acaba de actualizar.
1. En el explorador, busque el objeto "issuer" y copie su valor. Debería ser similar a lo siguiente: `https://sts.windows.net/{tenantId}/`.
1. Pegue el valor de `<Item Key="ProviderName">` en el archivo XML.
1. Establezca `<Item Key="client_id">` en el identificador de aplicación desde el registro de aplicaciones.
1. Establezca `<Item Key="IdTokenAudience">` en el identificador de aplicación desde el registro de aplicaciones.
1. Asegúrese de que `<Item Key="response_types">` está establecido en `id_token`.
1. Asegúrese de que `<Item Key="UsePolicyInRedirectUri">` está establecido en `false`.

También es preciso que vincule el secreto de Azure AD que ha registrado en el inquilino de Azure AD B2C a la información de `<ClaimsProvider>` de Azure AD:

* En la sección `<CryptographicKeys>` del archivo XML anterior, actualice el valor de `StorageReferenceId` al identificador de referencia del secreto que ha definido (por ejemplo, `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Carga del archivo de extensión para su comprobación

Por el momento, ha configurado la directiva para que Azure AD B2C sepa cómo comunicarse con su directorio de Azure AD. Pruebe a cargar el archivo de extensión de la directiva para confirmar que no tiene problemas. Para ello:

1. Vaya a la hoja **Todas las directivas** en el inquilino de Azure AD B2C.
1. Active la casilla **Sobrescribir la directiva si existe**.
1. Cargue el archivo de extensión (TrustFrameworkExtensions.xml) y asegúrese de que no se produce ningún error en la validación.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrar el proveedor de notificaciones de Azure AD en un recorrido del usuario

Ahora es preciso agregar el proveedor de identidades de Azure AD a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión. Para que esté disponible, crearemos un duplicado de un recorrido del usuario de plantilla existente y, después, lo modificaremos para que también tenga el proveedor de identidades de Azure AD:

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
1. Busque el elemento `<UserJourneys>` y copie todo el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"`.
1. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
1. Pegue todo el nodo `<UserJourney>` que ha copiado como elemento secundario de `<UserJourneys>`.
1. Cambie el nombre del identificador del nuevo recorrido del usuario (por ejemplo, cambie el nombre a `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Incorporación del "botón"

El elemento `<ClaimsProviderSelection>` es análogo a un botón del proveedor de identidades en una pantalla de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para Azure AD, se mostrará un botón nuevo cuando un usuario llega a la página. Para agregar este elemento:

1. Busque el nodo `<OrchestrationStep>` que incluye `Order="1"` en el recorrido del usuario que acaba de crear.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Establezca `TargetClaimsExchangeId` en valor apropiado. Se recomienda seguir la misma convención que otros:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción

Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Azure AD para recibir un token. Para vincular un botón a una acción, vincule el perfil técnico del proveedor de notificaciones de Azure AD:

1. Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Actualice `Id` con el mismo valor que el de `TargetClaimsExchangeId` en la sección anterior.
1. Actualice `TechnicalProfileReferenceId` con el identificador del perfil técnico que ha creado anteriormente (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Cargar el archivo de extensión actualizado

Ya ha terminado la modificación del archivo de extensión. Guarde y cargue el archivo y asegúrese de que el resultado de todas las validaciones es satisfactorio.

### <a name="update-the-rp-file"></a>Actualización del archivo de RP

Ahora es preciso actualizar el archivo del usuario de confianza (RP) que iniciará el recorrido del usuario que acaba de crear:

1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo y cambie su nombre (por ejemplo, SignUpOrSignInWithAAD.xml).
1. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único (por ejemplo, SignUpOrSignInWithAAD). <br> Este será el nombre de la directiva (por ejemplo, B2C\_1A\_SignUpOrSignInWithAAD).
1. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el identificador del nuevo recorrido del usuario que ha creado (SignUpOrSignUsingContoso).
1. Guarde los cambios y cargue el archivo.

## <a name="troubleshooting"></a>Solución de problemas

Pruebe la directiva personalizada que acaba de cargar. Para ello, abra su hoja y haga clic en **Ejecutar ahora**. Para diagnosticar problemas, obtenga información sobre la [solución de problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes

Envíe sus comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
