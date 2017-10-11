---
title: "Azure Active Directory B2C: incorporación de un proveedor de Salesforce SAML mediante el uso de directivas personalizadas|Microsoft Docs"
description: "Obtenga información sobre cómo crear y administrar directivas personalizadas de Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.openlocfilehash: 269cbd80fb6e861fa8588025eec70b6c6e2890d7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: inicio de sesión mediante cuentas de Salesforce a través de SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo usar [directivas personalizadas](active-directory-b2c-overview-custom.md) para configurar el inicio de sesión de los usuarios de una organización de Salesforce concreta.

## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-ad-b2c-setup"></a>Configuración de Azure AD B2C

Asegúrese de que ha completado todos los pasos que le muestran cómo [empezar a trabajar con directivas personalizadas](active-directory-b2c-get-started-custom.md) en Azure Active Directory B2C (Azure AD B2C).

Entre ellos se incluyen los siguientes:

* Crear un inquilino de Azure AD B2C.
* Crear una aplicación de Azure AD B2C.
* Registrar dos aplicaciones de motor de directivas.
* Configurar las claves.
* Configurar el paquete de inicio.

### <a name="salesforce-setup"></a>Configuración de Salesforce

En este artículo se supone que ya ha realizado lo siguiente:

* Se ha registrado para obtener una cuenta de Salesforce. Puede registrarse para [una cuenta gratuita de Developer Edition](https://developer.salesforce.com/signup).
* [Ha configurado un Mi dominio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para su organización de Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Configurar Salesforce, para que los usuarios se puedan federar

Para permitir que Azure AD B2C se comunique con Salesforce, debe obtener la dirección URL de metadatos de Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar Salesforce como proveedor de identidades

> [!NOTE]
> En este artículo, se supone que usa [Lightning Experience de Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Inicie sesión en Salesforce](https://login.salesforce.com/). 
2. En el menú de la izquierda, en **Settings** (Configuración), expanda **Identity** (Identidad) y haga clic en **Identity Provider** (Proveedor de identidades).
3. Haga clic en **Enable Identity Provider** (Habilitar proveedor de identidades).
4. Bajo **Select the certificate** (Seleccionar el certificado), seleccione el certificado que quiere que Salesforce use para comunicarse con Azure AD B2C. (Puede usar el certificado predeterminado). Haga clic en **Guardar**. 

### <a name="create-a-connected-app-in-salesforce"></a>Crear una aplicación conectada en Salesforce

1. En la página **Identity Provider** (Proveedor de identidades), vaya a **Service Providers** (Proveedores de servicios).
2. Haga clic en **Service Providers are now created via Connected Apps (Los proveedores de servicios se crean ahora a través de aplicaciones conectadas). Haga clic aquí.**
3. En **Basic Information**(Información básica), escriba los valores necesarios para la aplicación conectada.
4. En **Web App Settings** (Configuración de aplicación web), active la casilla **Enable SAML** (Habilitar SAML).
5. En el campo **Entity ID** (Id. de entidad), escriba la siguiente dirección URL. Asegúrese de que reemplaza el valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. En el campo **ACS URL** (Dirección URL de ACS), escriba la siguiente dirección URL. Asegúrese de que reemplaza el valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Deje los valores predeterminados de las demás opciones de configuración.
8. Desplácese hasta la parte inferior de la lista y, después, haga clic en **Guardar**.

### <a name="get-the-metadata-url"></a>Obtener la dirección URL de metadatos

1. En la página de información general de la aplicación conectada, haga clic en **Administrar**.
2. Copie el valor de **punto de conexión de detección de metadatos** y después guárdelo. Lo usará más adelante en este artículo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar usuarios de Salesforce para la federación

1. En la página **Administrar** de la aplicación conectada, vaya a **Perfiles**.
2. Haga clic en **Administrar perfiles**.
3. Seleccione los perfiles (o grupos de usuarios) que quiere que realicen la federación con Azure AD B2C. Como administrador del sistema, active la casilla **Administrador del sistema** para poder realizar la federación con su cuenta de Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generar un certificado de firma para Azure AD B2C

Las solicitudes enviadas a Salesforce deben estar firmadas mediante Azure AD B2C. Para generar un certificado de firma, abra Azure PowerShell y ejecute los comandos siguientes.

> [!NOTE]
> Asegúrese de actualizar el nombre de inquilino y la contraseña en las dos líneas superiores.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Agregar un certificado de firma de SAML a Azure AD B2C

Cargue el certificado de firma en el inquilino de Azure AD B2C: 

1. Vaya al inquilino de Azure AD B2C. Haga clic en **Configuración** > **Marco de experiencia de identidad** > **Claves de directiva**.
2. Haga clic en **+Agregar** y después:
    1. Haga clic en **Opciones** > **Cargar**.
    2. Escriba un **nombre** (por ejemplo, SAMLSigningCert). El prefijo *B2C_1A_* se agrega automáticamente al nombre de la clave.
    3. Para seleccionar el certificado, use el **control de carga de archivos**. 
    4. Escriba la contraseña del certificado que ha establecido en el script de PowerShell.
3. Haga clic en **Crear**.
4. Compruebe que ha creado una clave (por ejemplo, B2C_1A_SAMLSigningCert). Tome nota del nombre completo (incluido *B2C_1A_*). Hará referencia a esta clave más adelante en la directiva.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Creación del proveedor de notificaciones de Salesforce SAML en la directiva de base

Para que los usuarios puedan iniciar sesión mediante Salesforce, debe definir Salesforce como proveedor de notificaciones. En otras palabras, tiene que especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión *proporcionará* un conjunto de *notificaciones* que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. Para ello, agregue un `<ClaimsProvider>` para Salesforce en el archivo de extensión de la directiva:

1. En el directorio de trabajo, abra el archivo de extensión (TrustFrameworkExtensions.xml).
2. Busque la sección `<ClaimsProviders>`. Si no existe, créelo debajo del nodo raíz.
3. Agregar un nuevo `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

En el nodo `<ClaimsProvider>`:

1. Cambie el valor de `<Domain>` a un valor único que distinga `<ClaimsProvider>` de otros proveedores de identidades.
2. Actualice el valor de `<DisplayName>` a un nombre para mostrar para el proveedor de notificaciones. Actualmente este valor no se usa.

### <a name="update-the-technical-profile"></a>Actualización del perfil técnico

Para obtener un token de SAML de Salesforce, defina los protocolos que Azure AD B2C va a usar para comunicarse con Azure Active Directory (Azure AD). Puede hacer esto en el elemento `<TechnicalProfile>` de `<ClaimsProvider>`:

1. Actualice el identificador del nodo `<TechnicalProfile>`. Este identificador se usa para hacer referencia a este perfil técnico desde otras partes de la directiva.
2. Actualice el valor de `<DisplayName>`. Este valor se muestra en el botón de inicio de sesión de la página de inicio de sesión.
3. Actualice el valor de `<Description>`.
4. Salesforce usa el protocolo SAML 2.0. Asegúrese de que el valor de `<Protocol>` es **SAML2**.

Actualice la sección `<Metadata>` del código XML anterior para reflejar la configuración de su cuenta de Salesforce específica. En el código XML, actualice los valores de metadatos:

1. Actualice el valor de `<Item Key="PartnerEntity">` con la dirección URL de metadatos de Salesforce que ha copiado anteriormente. Tiene el siguiente formato: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. En la sección `<CryptographicKeys>`, actualice el valor de las dos instancias de `StorageReferenceId` con el nombre de la clave de su certificado de firma (por ejemplo, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Carga del archivo de extensión para su comprobación

Ahora la directiva está configurada para que Azure AD B2C sepa cómo comunicarse con Salesforce. Pruebe a cargar el archivo de extensión de la directiva, para confirmar que no tiene problemas. Para cargar el archivo de extensión de la directiva:

1. En el inquilino de Azure AD B2C, vaya a la hoja **All Policies** (Todas las directivas).
2. Active la casilla **Sobrescribir la directiva si existe**.
3. Cargue el archivo de extensión (TrustFrameworkExtensions.xml). Asegúrese de que no se produce un error en la validación.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrar el proveedor de notificaciones de Salesforce SAML en un recorrido del usuario

Después, agregue el proveedor de identidades de Salesforce SAML a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las páginas de registro o inicio de sesión de usuario. Para agregar el proveedor de identidades a una página de inicio de sesión, en primer lugar cree un duplicado de una plantilla de recorrido del usuario existente. Después, modifique la plantilla para que también tenga el proveedor de identidades de Azure AD.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2. Busque el elemento `<UserJourneys>` y copie el valor `<UserJourney>` completo, incluido Id="SignUpOrSignIn".
3. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml). Busque el elemento `<UserJourneys>`. Si el elemento no existe, cree uno.
4. Pegue todo el elemento `<UserJourney>` copiado como elemento secundario de `<UserJourneys>`.
5. Cambie el nombre del Id. del nuevo `<UserJourney>` (por ejemplo, SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Mostrar el botón de proveedor de identidades

El elemento `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades en una página de registro o inicio de sesión. Al agregar un elemento `<ClaimsProviderSelection>` para Salesforce, aparece un botón nuevo cuando un usuario se desplaza a esta página. Para mostrar el botón de proveedor de identidades:

1. En el `<UserJourney>` que ha creado, busque `<OrchestrationStep>` con `Order="1"`.
2. Agregue el siguiente código XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Establezca `TargetClaimsExchangeId` en un valor lógico. Se recomienda seguir la misma convención que en el resto: (por ejemplo, *\[NombreProveedorDeNotificaciones\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Vincular el botón de proveedor de identidades a una acción

Ahora que hay un botón de proveedor de identidades colocado, vincúlelo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Salesforce para recibir un token SAML. Para ello, vincule el perfil técnico del proveedor de notificaciones de Salesforce SAML:

1. En el nodo `<UserJourney>`, busque `<OrchestrationStep>` con `Order="2"`.
2. Agregue el siguiente código XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Actualice `Id` al mismo valor que ha usado previamente para `TargetClaimsExchangeId`.
4. Actualice `TechnicalProfileReferenceId` al valor `Id` del perfil técnico que ha creado anteriormente (por ejemplo, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Cargar el archivo de extensión actualizado

Ya ha terminado la modificación del archivo de extensión. Guarde y cargue este archivo. Asegúrese de que todas las validaciones se han realizado correctamente.

### <a name="update-the-relying-party-file"></a>Actualizar el archivo de usuario de confianza

Después, actualice el archivo del usuario de confianza (RP) que inicia el recorrido del usuario que ha creado:

1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo. Después, cambie el nombre (por ejemplo, SignUpOrSignInWithAAD.xml).
2. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único. Este es el nombre de la directiva (por ejemplo, SignUpOrSignInWithAAD).
3. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el `Id` del nuevo recorrido del usuario que ha creado (por ejemplo, SignUpOrSignUsingContoso).
4. Guarde los cambios y después cargue el archivo.

## <a name="test-and-troubleshoot"></a>Pruebas y solución de problemas

Para probar la directiva personalizada que acaba de cargar, en Azure Portal, vaya a la hoja de la directiva y, después, haga clic en **Ejecutar ahora**. Si se produce un error, vea [Solución de problemas de directivas personalizadas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes

Envíe sus comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
