---
title: "Azure Active Directory B2C: incorporación de un proveedor de Salesforce SAML mediante el uso de directivas personalizadas|Microsoft Docs"
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: dee24deacbe69ada64519802c0eb1b83f565f57e
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


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

## <a name="get-the-salesforce-saml-metadata"></a>Obtención de los metadatos de Salesforce SAML
>[!NOTE]
> Este tutorial supone que usa [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Inicie sesión en Salesforce](https://login.salesforce.com/) 
1. En el menú izquierdo, en **Settings** (Configuración), expanda **Identity** (Identidad) y haga clic en **Identity Provider** (Proveedor de identidades)
1. Haga clic en **Enable Identity Provider** (Habilitar proveedor de identidades)
1. **Seleccione el certificado** que desea que Salesforce use al comunicarse con Azure AD B2C y haga clic en **Save** (Guardar). Puede usar el certificado predeterminado.
1. Haga clic en el botón **Download Metadata** (Descargar metadatos) que está ahora disponible y guarde el archivo de metadatos que utilizará en otro paso más adelante.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Incorporación de un certificado de firma de SAML a Azure AD B2C
Tiene que almacenar el certificado de Salesforce en el inquilino de Azure AD B2C. Para ello, siga estos pasos:

1. Abra PowerShell y navegue hasta el directorio de trabajo `active-directory-b2c-advanced-policies`.
1. Cambie a la carpeta con la herramienta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importar la herramienta ExploreAdmin en Powershell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. En el siguiente comando, reemplace `tenantName` con el nombre de su inquilino de Azure AD B2C (por ejemplo, fabrikamb2c.onmicrosoft.com), `certificateId` con un nombre para el certificado que se va a usar para hacer referencia a él más adelante en la directiva (por ejemplo, ContosoSalesforceCert) y, finalmente, `pathToCert` y `password` con la ruta de acceso y la contraseña del certificado. Ejecute el comando.

    ```PowerShell
    Set-CpimCertificate -TenantId {tenantName} -CertificateId {certificateId} -CertificateFileName {pathToCert} - CertificatePassword {password}
    ```

    Al ejecutar el comando, asegúrese de que inicia sesión con la cuenta de administrador de onmicrosoft.com local para el inquilino de Azure AD B2C. 

1. Cierre PowerShell.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Creación del proveedor de notificaciones de Salesforce SAML en la directiva de base

Para permitir que los usuarios inicien sesión con Salesforce, es preciso definir Salesforce como proveedor de notificaciones. En otras palabras, tiene que especificar el punto de conexión con el que Azure AD B2C se va a comunicar. El punto de conexión *proporcionará* un conjunto de *notificaciones* que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado. Para ello, agregue un `<ClaimsProvider>` para Salesforce en el archivo de extensión de la directiva.

1. Abra el archivo de extensión desde el directorio de trabajo (TrustFrameworkExtensions.xml).
1. Busque la sección `<ClaimsProviders>`. Si no existe, agréguela debajo del nodo raíz.
1. Agregue una instancia nueva de `<ClaimsProvider>` como se indica a continuación:

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
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

1. En el nodo `<ClaimsProvider>`, actualice el valor de `<Domain>` a un valor único que pueda utilizarse para distinguirse de otros proveedores de identidad.
1. En el nodo `<ClaimsProvider>`, actualice el valor de `<DisplayName>` a un nombre descriptivo del proveedor de notificaciones. Este valor no se utiliza actualmente.

### <a name="update-the-technical-profile"></a>Actualización del perfil técnico

Para obtener un token de SAML de Salesforce, es preciso definir los protocolos que Azure AD B2C debe usar para comunicarse con Azure AD. Esto se realiza dentro del elemento `<TechnicalProfile>` de `<ClaimsProvider>`.

1. Actualice el identificador del nodo `<TechnicalProfile>`. Este identificador se usa para hacer referencia a este perfil técnico desde otras partes de la directiva.
1. Actualice el valor de `<DisplayName>`. Este valor se mostrará en el botón de inicio de sesión de la pantalla de inicio de sesión.
1. Actualice el valor de `<Description>`.
1. Azure AD usa el protocolo OpenID Connect, por lo que debe asegurarse de que el valor de `<Protocol>` es "SAML2".

Debe actualizar la sección `<Metadata>` del XML anterior para reflejar la configuración de su inquilino de Azure AD específico. En el XML, actualice los valores de los metadatos como se indica a continuación:

1. Actualice el valor de `<Item Key="PartnerEntity">`, con el contenido de Metadata.xml que descargó de Salesforce. **Asegúrese de que lo encapsula con <![CDATA[ …metadata… ]]>** .

1. En la sección `<CryptographicKeys>` del XML anterior, actualice el valor de ambos `StorageReferenceId` al identificador de certificado que definió (por ejemplo, ContosoSalesforceCert).

### <a name="upload-the-extension-file-for-verification"></a>Carga del archivo de extensión para su comprobación

Por ahora, ha configurado la directiva para que Azure AD B2C sepa cómo comunicarse con su directorio de Azure AD. Pruebe a cargar el archivo de extensión de la directiva para confirmar que no tiene problemas. Para ello:

1. Vaya a la hoja **All Policies** (Todas las directivas) en el inquilino de Azure AD B2C.
1. Active la casilla **Sobrescribir la directiva si existe**.
1. Cargue el archivo de extensión (TrustFrameworkExtensions.xml) y asegúrese de que no se produce ningún error de validación.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registro del proveedor de notificaciones de Salesforce SAML en un recorrido del usuario

Ahora tiene que agregar el proveedor de identidades de Salesforce SAML a uno de los recorridos del usuario. El proveedor de identidades ya se ha configurado, pero no está disponible en ninguna de las pantallas de registro/inicio de sesión. Para que lo esté, se creará un duplicado de un recorrido del usuario existente y, a continuación, se modificará para que también tenga el proveedor de identidades de Azure AD.

1. Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml)
1. Busque el elemento `<UserJourneys>` y copie todo el `<UserJourney>` con Id = "SignUpOrSignIn".
1. Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
1. Pegue todo el elemento `<UserJourney>` que copió como elemento secundario de `<UserJourneys>`.
1. Cambie el nombre del identificador del nuevo elemento `<UserJourney>` (es decir, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Incorporación del "botón"

El elemento `<ClaimsProviderSelection>` es análogo a un botón del proveedor de identidades en una pantalla de registro o inicio de sesión. Al agregar un elemento `<ClaimsProviderSelection>` para Salesforce, se mostrará un botón nuevo cuando a un usuario accede a la página. Para ello, siga estos pasos:

1. Busque `<OrchestrationStep>` con `Order="1"` en el elemento `<UserJourney>` que acaba de crear.
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Establezca `TargetClaimsExchangeId` en un valor apropiado. Se recomienda seguir la misma convención que otros:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincule el "botón" a una acción

Ahora que hay un "botón" colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con Salesforce para recibir un token SAML. Para ello, vincule el perfil técnico del proveedor de notificaciones de Salesforce SAML.

1. Busque `<OrchestrationStep>` con `Order="2"` en el nodo `<UserJourney>`
1. Agregue la siguiente línea de código:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Actualice `Id` para que tenga al mismo valor que `TargetClaimsExchangeId`.
1. Actualice `TechnicalProfileReferenceId` al valor `Id` del perfil técnico que creó anteriormente (por ejemplo, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carga del archivo de extensión actualizado

Ya ha terminado la modificación del archivo de extensión. Guarde y cargue el archivo y asegúrese de que el resultado de todas las validaciones es satisfactorio.

### <a name="update-the-rp-file"></a>Actualización del archivo de RP

Ahora tiene que actualizar el archivo de RP que iniciará el recorrido del usuario que acaba de crear.

1. Realice una copia de SignUpOrSignIn.xml en el directorio de trabajo y cambie su nombre (por ejemplo, SignUpOrSignInWithAAD.xml).
1. Abra el archivo nuevo y actualice el atributo `PolicyId` de `<TrustFrameworkPolicy>` con un valor único. Este será el nombre de la directiva (por ejemplo, SignUpOrSignInWithAAD).
1. Modifique el atributo `ReferenceId` de `<DefaultUserJourney>` para que coincida con el identificador del nuevo recorrido del usuario que ha creado (por ejemplo, SignUpOrSignUsingContoso).
1. Guarde los cambios y cargue el archivo.

## <a name="create-a-connected-app-in-salesforce"></a>Creación de una aplicación conectada en Salesforce
Es preciso que registre Azure AD B2C como una aplicación conectada en Salesforce.

1. [Inicie sesión en Salesforce](https://login.salesforce.com/) 
1. En el menú izquierdo, en **Settings** (Configuración), expanda **Identity** (Identidad) y haga clic en **Identity Provider** (Proveedor de identidades)
1. En la parte inferior de la sección **Service Providers** (Proveedores de servicios), haga clic en **Service Providers are now created via Connected Apps (Los proveedores de servicios se crean ahora a través de aplicaciones conectadas). Haga clic aquí.**
1. Proporcione la **información básica** necesaria para la aplicación conectada.
1. Ahora, en la sección **Web App Settings** (Configuración de aplicación web):
    1. Haga clic en **Enable SAML** (Habilitar SAML)
    1. Escriba la dirección URL siguiente en el campo **Entity ID** (Identificador de entidad), no se olvide de reemplazar el `tenantName`. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base
        ```

    1. Escriba la dirección URL siguiente en el campo **ACS URL** (URL de ACS), no se olvide de reemplazar el `tenantName`. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base/samlp/sso/assertionconsumer
        ```

    1. Deje todas las demás opciones con sus valores predeterminados
1. Desplácese hasta la parte inferior y haga clic en el botón **Save** (Guardar)


## <a name="troubleshooting"></a>Solución de problemas

Pruebe la directiva personalizada que acaba de cargar, para lo que debe abrir su hoja y hacer clic en "Ejecutar ahora". Si se produce algún error, consulte cómo [solucionarlo](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Pasos siguientes
 
Envíe sus comentarios a [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).


