---
title: "Azure Active Directory B2C: adición de un proveedor de Azure AD mediante directivas personalizadas | Microsoft Docs"
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 29d30cacb29fee1b2c5b8ef523051fa543bee829
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C: inicio de sesión con cuentas de Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión de los usuarios de una organización de Azure AD concreta mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que ha completado todos los pasos que le muestran cómo [empezar a trabajar con directivas personalizadas](active-directory-b2c-get-started-custom.md).

En ella se incluye:

1. Creación de un inquilino de Azure AD B2C.
1. Creación de una aplicación de Azure AD B2C.
1. Registro de dos aplicaciones de motor de directivas.
1. Configuración de claves.
1. Configurar el paquete de inicio.

## <a name="create-an-azure-ad-app"></a>Creación de una aplicación de Azure AD

Para habilitar el inicio de sesión para los usuarios de una organización específica de Azure AD, es preciso registrar una aplicación en el inquilino de Azure AD de la organización.

>[!NOTE]
> El inquilino de Azure AD de la organización será `contoso.com`, mientras que el inquilino de Azure AD B2C será `fabrikamb2c.onmicrosoft.com`.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1. En la barra superior, haga clic en su cuenta y, en la lista **Directorio**, elija el inquilino de Azure AD de la organización en el que desea registrar la aplicación (p. ej., contoso.com).
1. Haga clic en **More services** (Más servicios) en el panel de navegación izquierdo y busque **Registros de aplicaciones**.
1. Elija **Nuevo registro de aplicaciones**.
1. Escriba el **nombre** de la aplicación (por ejemplo, Aplicación de Azure AD B2C)
1. En Tipo de aplicación, seleccione **Aplicación web o API**.
1. En 'URL de inicio de sesión', escriba la dirección URL siguiente, donde `{tenantName}` se debe reemplazar por el nombre de un inquilino de Azure AD B2C (es decir, fabrikamb2c.onmicrosoft.com).

    ```
    https://login.microsoftonline.com/te/{tenantName}.onmicrosoft.com/oauth2/authresp
    ```

1. Guarde el **identificador de la aplicación**.
1. Haga clic en la aplicación recién creada.
1. En la hoja Configuración, haga clic en **Claves**.
1. Cree una clave nueva y guárdela para la sección siguiente.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adición de la clave de Azure AD a Azure AD B2C

La clave de aplicación `contoso.com` debe almacenarla en su inquilino de Azure AD B2C. Para ello, siga estos pasos:

1. Abra PowerShell y navegue hasta el directorio de trabajo `active-directory-b2c-advanced-policies`.
1. Cambie a la carpeta con la herramienta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importe la herramienta ExploreAdmin en PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. En el siguiente comando, reemplace `tenantName` por el nombre de su inquilino de Azure AD B2C (p. ej. fabrikamb2c.onmicrosoft.com), `SecretReferenceId` por el nombre que va a usar para hacer referencia el secreto (p. ej. ContosoAppSecret) y `ClientSecret` por la clave de la aplicación de `contoso.com`. Ejecute el comando.

    ```PowerShell
    Set-CpimKeyContainer -Tenant {tenantName} -StorageReferenceId {SecretReferenceId} -UnencodedAsciiKey {ClientSecret}
    ```

    Al ejecutar el comando, asegúrese de que inicia sesión con la cuenta de administrador de onmicrosoft.com local para el inquilino de Azure AD B2C. Si recibe un error que indica que no se encuentra 'TokenSigningKeyContainer', consulte la guía de [introducción](active-directory-b2c-get-started-custom.md).

1. Cierre PowerShell.

## <a name="add-a-claims-provider-in-your-base-policy"></a>Adición de un proveedor de notificaciones a una directiva de base

Para permitir que los usuarios inicien sesión con Azure AD, es preciso definir Azure AD como proveedor de notificaciones. En otras palabras, se debe especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión *proporcionará* un conjunto de *notificaciones* que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. Para ello, agregue Azure AD como `<ClaimsProvider>` en el archivo de extensión de la directiva.

1. Abra el archivo de extensión desde el directorio de trabajo (TrustFrameworkExtensions.xml).
1. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
1. Agregue una instancia nueva de `<ClaimsProvider>` como se indica a continuación:

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
            <Key Id="client_secret" StorageReferenceId="ContosoAppSecret"/>
            </CryptographicKeys>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="userId" PartnerClaimType="oid"/>
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

1. En el nodo `<ClaimsProvider>`, actualice el valor de `<Domain>` a un valor único que pueda utilizarse para distinguirse de otros proveedores de identidad.
1. En el nodo `<ClaimsProvider>`, actualice el valor de `<DisplayName>` a un nombre descriptivo del proveedor de notificaciones. Este valor no se utiliza actualmente.

### <a name="update-the-technical-profile"></a>Actualización del perfil técnico

Para obtener un token del punto de conexión de Azure AD es preciso definir los protocolos que Azure AD B2C debe usar para comunicarse con Azure AD. Esto se realiza dentro del elemento `<TechnicalProfile>` de `<ClaimsProvider>`.

1. Actualice el identificador del nodo `<TechnicalProfile>`. Este identificador se usa para hacer referencia a este perfil técnico desde otras partes de la directiva.
1. Actualice el valor de `<DisplayName>`. Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión.
1. Actualice el valor de `<Description>`.
1. Azure AD usa el protocolo OpenID Connect, por lo que debe asegurarse de que el valor de `<Protocol>` es "OpenIDConnect".

Debe actualizar la sección `<Metdata>` del XML anterior para reflejar la configuración de su inquilino de Azure AD específico. En el XML, actualice los valores de los metadatos como se indica a continuación:

1. Establezca `<Item Key="METADATA">` en `https://login.windows.net/{tenantName}/.well-known/openid-configuration`, donde `tenantName` es el nombre del inquilino de Azure AD (por ejemplo, contoso.com).
1. Abra el explorador y navegue hasta la dirección URL `Metadata` que acaba de actualizar.
1. En el explorador, busque el objeto "issuer" y copie su valor. Debería ser como el siguiente `https://sts.windows.net/{tenantId}/`.
1. Pegue el valor de `<Item Key="ProviderName">` en el XML.
1. Establezca `<Item Key="client_id">` en `Application ID` desde el registro de aplicaciones.
1. Establezca `<Item Key="IdTokenAudience">` en `Application ID` desde el registro de aplicaciones.
1. Asegúrese de que `<Item Key="response_types">` está establecido en `id_token`.
1. Asegúrese de que `<Item Key="UsePolicyInRedirectUri">` está establecido en `false`.

También es preciso que vincule el [secreto de Azure AD registró en el inquilino de Azure AD B2C ](#add-the-azure-ad-key-to-azure-ad-b2c) a `<ClaimsProvider>` de Azure AD.

1. En la sección `<CryptographicKeys>` del XML anterior, actualice el valor de `StorageReferenceId` al identificador de referencia del secreto que definió (p. ej., ContosoAppSecret).

### <a name="upload-the-extension-file-for-verification"></a>Carga del archivo de extensión para su comprobación

Hasta ahora, ha configurado la directiva para que Azure AD B2C sepa cómo comunicarse con su directorio de Azure AD. Pruebe a cargar el archivo de extensión de la directiva para confirmar que no tiene problemas. Para ello:

1. Vaya a la hoja **All Policies** (Todas las directivas) en el inquilino de Azure AD B2C.
1. Active la casilla **Sobrescribir la directiva si existe**.
1. Cargue el archivo de extensión (TrustFrameworkExtensions.xml) y asegúrese de que no se produce ningún error en la comprobación.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registro del proveedor de notificaciones de Azure AD en un recorrido del usuario

Ahora es preciso agregar el proveedor de identidades de Azure AD a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión. Para ello, se creará un duplicado de un recorrido del usuario existente y, a continuación, se modificará para que también tenga el proveedor de identidades de Azure AD.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml)
1. Busque el elemento `<UserJourneys>` y copie todo `<UserJourney>` con Id = "SignUpOrSignIn".
1. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
1. Pegue todo el elemento `<UserJourney>` que copió como elemento secundario de `<UserJourneys>`.
1. Cambiar el nombre del identificador del nuevo elemento `<UserJourney>` (es decir, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Incorporación del "botón"

El elemento `<ClaimsProviderSelection>` es análogo a un botón del proveedor de identidades en una pantalla de registro o inicio de sesión. Al agregar un elemento `<ClaimsProviderSelection>` para Azure AD, se mostrará un botón nuevo cuando a un usuario accede a la página. Para ello, siga estos pasos:

1. Busque `<OrchestrationStep>` con `Order="1"` en el elemento `<UserJourney>` que acaba de crear.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Establezca `TargetClaimsExchangeId` en valor apropiado. Se recomienda seguir la misma convención que otros:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincule el "botón" a una acción

Ahora que hay un "botón" colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Azure AD para recibir un token. Para ello, vincule el perfil técnico del proveedor de notificaciones de Azure AD.

1. Busque `<OrchestrationStep>` con `Order="2"` en el nodo `<UserJourney>`
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Actualice `Id` para que tenga al mismo valor que `TargetClaimsExchangeId`.
1. Actualice `TechnicalProfileReferenceId` al valor `Id` del perfil técnico que creó anteriormente (p. ej. ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Cargar el archivo de extensión actualizado

Ya ha terminado la modificación del archivo de extensión. Guarde y cargue el archivo y asegúrese de que el resultado de todas las comprobaciones es satisfactorio.

### <a name="update-the-rp-file"></a>Actualización del archivo de RP

Ahora es preciso actualizar el archivo de RP que iniciará el recorrido del usuario que acaba de crear.

1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo y cambie su nombre (por ejemplo, SignUpOrSignInWithAAD.xml).
1. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único. Este será el nombre de la directiva (por ejemplo, SignUpOrSignInWithAAD).
1. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el identificador del nuevo recorrido del usuario que ha creado (por ejemplo, SignUpOrSignUsingContoso).
1. Guarde los cambios y cargue el archivo.

## <a name="troubleshooting"></a>Solución de problemas

Pruebe la directiva personalizada que acaba de cargar, para lo que debe abrir su hoja y hacer clic en "Ejecutar ahora". Si se produce algún error, consulte cómo [solucionarlo](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes
 
Envíe sus comentarios a AADB2CPreview@microsoft.com.


