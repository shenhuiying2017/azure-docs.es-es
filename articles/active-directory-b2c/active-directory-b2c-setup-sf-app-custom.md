---
title: "Azure Active Directory B2C: incorporación de un proveedor de Salesforce SAML mediante el uso de directivas personalizadas|Microsoft Docs"
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dec042efe30b80d68b3f200a4aad4fd5d24cd158
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: inicio de sesión usando cuentas de Salesforce mediante SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión de los usuarios de una organización de Salesforce concreta mediante el uso de [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-ad-b2c-setup"></a>Configuración de Azure AD B2C

Asegúrese de que ha completado todos los pasos que le muestran cómo [empezar a trabajar con directivas personalizadas](active-directory-b2c-get-started-custom.md).

En ella se incluye:

1. Creación de un inquilino de Azure AD B2C.
1. Creación de una aplicación de Azure AD B2C.
1. Registro de dos aplicaciones de motor de directivas.
1. Configuración de claves.
1. Configuración del paquete de inicio.

### <a name="salesforce-setup"></a>Configuración de Salesforce

En este tutorial se asume que ya:

1. Se ha registrado para obtener una cuenta de Salesforce. Puede registrarse para [Developer Edition de forma gratuita](https://developer.salesforce.com/signup).
1. [Ha instalado un Mi dominio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para su organización de Salesforce.

## <a name="configure-salesforce-to-allow-users-to-federate"></a>Configurar Salesforce para permitir que los usuarios realicen la federación

Para permitir que Azure AD B2C se comunique con Salesforce, debe obtener la dirección URL de metadatos de Salesforce.

### <a name="enable-salesforce-as-an-identity-provider"></a>Habilitar Salesforce como proveedor de identidades

>[!NOTE]
> Este tutorial supone que usa [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Inicie sesión en Salesforce](https://login.salesforce.com/) 
1. En el menú izquierdo, en **Settings** (Configuración), expanda **Identity** (Identidad) y haga clic en **Identity Provider** (Proveedor de identidades)
1. Haga clic en **Enable Identity Provider** (Habilitar proveedor de identidades)
1. **Seleccione el certificado** que desea que Salesforce use al comunicarse con Azure AD B2C y haga clic en **Save** (Guardar). Puede usar el certificado predeterminado.

### <a name="create-a-connected-app-in-salesforce"></a>Crear una aplicación conectada en Salesforce

1. En la página **Proveedor de identidades**, vaya a la sección **Proveedores de servicios**.
1. Haga clic en **Service Providers are now created via Connected Apps (Los proveedores de servicios se crean ahora a través de aplicaciones conectadas). Haga clic aquí.**
1. Proporcione la **información básica** necesaria para la aplicación conectada.
1. En la sección **Configuración de aplicación web**, marque **Habilitar SAML**.
1. Escriba la dirección URL siguiente en el campo **Id. de entidad**. No se olvide de reemplazar el valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
1. Escriba la dirección URL siguiente en el campo **ACS URL** (URL de ACS). No se olvide de reemplazar el valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
1. Deje todas las demás opciones con sus valores predeterminados.
1. Desplácese hasta la parte inferior y haga clic en el botón **Guardar**.

### <a name="get-the-metadata-url"></a>Obtener la dirección URL de metadatos

1. Haga clic en **Administrar** en la página de información general de la aplicación conectada.
1. Copie el **punto de conexión de detección de metadatos** y guárdelo para más tarde.

### <a name="enable-salesforce-users-to-federate"></a>Permitir que los usuarios de Salesforce realicen la federación

1. En la página "Administrar" de la aplicación conectada, desplácese hasta la sección **Perfiles**.
1. Haga clic en **Administrar perfiles**.
1. Seleccione los perfiles (o grupos de usuarios) que quiere que puedan realizar la federación con Azure AD B2C. Como administrador del sistema, debe activar la casilla **Administrador del sistema** para poder realizar la federación con su cuenta de Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generar un certificado de firma para Azure AD B2C

Las solicitudes enviadas a Salesforce deben estar firmadas mediante Azure AD B2C. Para generar un certificado de firma, abra PowerShell y ejecute los comandos siguientes:

**Asegúrese de actualizar el nombre de inquilino y la contraseña en las dos líneas superiores.**

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Agregar un certificado de firma de SAML a Azure AD B2C

Debe cargar el certificado de firma en el inquilino de Azure AD B2C. Para ello, siga estos pasos:

1. Vaya a su inquilino de Azure AD B2C y abra **Settings > Identity Experience Framework > Policy Keys** (Configuración > Marco de experiencia de identidad > Claves de directiva) de B2C.
1. Haga clic en **+Add** (+Agregar).
    * Seleccione **Options > Upload** (Opciones > Cargar).
    * Escriba un **nombre** (por ejemplo, `SAMLSigningCert`). Se agregará el prefijo B2C_1A_ automáticamente al nombre de su clave.
    * Use el **control de archivos de carga** para seleccionar el certificado.
    * Escriba la contraseña del certificado que ha establecido en el script de PowerShell.
1. Haga clic en **Crear**.
1. Confirme que ha creado una clave (por ejemplo, `B2C_1A_SAMLSigningCert`). Tome nota del nombre completo (con B2C_1A_) tal y como se hará referencia en la directiva más adelante.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Creación del proveedor de notificaciones de Salesforce SAML en la directiva de base

Para permitir que los usuarios inicien sesión con Salesforce, es preciso definir Salesforce como proveedor de notificaciones. En otras palabras, tiene que especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión *proporcionará* un conjunto de *notificaciones* que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. Para ello, agregue un `<ClaimsProvider>` para Salesforce en el archivo de extensión de la directiva.

1. Abra el archivo de extensión desde el directorio de trabajo (TrustFrameworkExtensions.xml).
1. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
1. Agregue una instancia nueva de `<ClaimsProvider>` como se indica a continuación:

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

* Actualice el valor de `<Domain>` a un valor único que pueda usarse para distinguirse de otros proveedores de identidades.
* Actualice el valor de `<DisplayName>` a un nombre descriptivo para el proveedor de notificaciones. Este valor no se utiliza actualmente.

### <a name="update-the-technical-profile"></a>Actualización del perfil técnico

Para obtener un token de SAML de Salesforce, es preciso definir los protocolos que Azure AD B2C debe usar para comunicarse con Azure AD. Esto se realiza dentro del elemento `<TechnicalProfile>` de `<ClaimsProvider>`.

1. Actualice el identificador del nodo `<TechnicalProfile>`. Este identificador se usa para hacer referencia a este perfil técnico desde otras partes de la directiva.
1. Actualice el valor de `<DisplayName>`. Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión.
1. Actualice el valor de `<Description>`.
1. Salesforce usa el protocolo SAML 2.0, por lo que debe asegurarse de que `<Protocol>` sea "SAML2".

Debe actualizar la sección `<Metadata>` del XML anterior para reflejar la configuración de su cuenta de Salesforce específica. En el XML, actualice los valores de los metadatos como se indica a continuación:

1. Actualice el valor de `<Item Key="PartnerEntity">` con la dirección URL de metadatos de Salesforce que ha copiado anteriormente. Su formato es `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`.

1. En la sección `<CryptographicKeys>` del XML anterior, actualice los dos valores de `StorageReferenceId` con el nombre de la clave de su certificado de firma (por ejemplo, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Carga del archivo de extensión para su comprobación

Por el momento, ha configurado la directiva para que Azure AD B2C sepa cómo comunicarse con Salesforce. Pruebe a cargar el archivo de extensión de la directiva para confirmar que no tiene problemas. Para ello:

1. Vaya a la hoja **All Policies** (Todas las directivas) en el inquilino de Azure AD B2C.
1. Active la casilla **Sobrescribir la directiva si existe**.
1. Cargue el archivo de extensión (TrustFrameworkExtensions.xml) y asegúrese de que no se produce ningún error de validación.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registro del proveedor de notificaciones de Salesforce SAML en un recorrido del usuario

Ahora tiene que agregar el proveedor de identidades de Salesforce SAML a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión. Para ello, se creará un duplicado de un recorrido del usuario existente y, a continuación, se modificará para que también tenga el proveedor de identidades de Azure AD.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml)
1. Busque el elemento `<UserJourneys>` y copie todo `<UserJourney>` con Id = "SignUpOrSignIn".
1. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
1. Pegue todo el elemento `<UserJourney>` que copió como elemento secundario de `<UserJourneys>`.
1. Cambiar el nombre del identificador del nuevo elemento `<UserJourney>` (es decir, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Incorporación del "botón"

El elemento `<ClaimsProviderSelection>` es análogo a un botón del proveedor de identidades en una pantalla de registro o inicio de sesión. Al agregar un elemento `<ClaimsProviderSelection>` para Salesforce, se mostrará un botón nuevo cuando a un usuario accede a la página. Para ello, siga estos pasos:

1. Busque `<OrchestrationStep>` con `Order="1"` en el elemento `<UserJourney>` que acaba de crear.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Establezca `TargetClaimsExchangeId` en valor apropiado. Se recomienda seguir la misma convención que otros:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincule el "botón" a una acción

Ahora que hay un "botón" colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Salesforce para recibir un token SAML. Para ello, vincule el perfil técnico del proveedor de notificaciones de Salesforce SAML.

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

## <a name="testing-and-troubleshooting"></a>Pruebas y solución de problemas

Pruebe la directiva personalizada que acaba de cargar, para lo que debe abrir su hoja y hacer clic en "Ejecutar ahora". Si se produce algún error, consulte cómo [solucionarlo](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes

Envíe sus comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
