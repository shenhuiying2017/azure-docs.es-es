---
title: "Azure Active Directory B2C: adición de ADFS como proveedor de identidades de SAML mediante directivas personalizadas"
description: "Un artículo de procedimientos sobre cómo configurar ADFS 2016 mediante el protocolo SAML y directivas personalizadas."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 22b360aec8878925ebe8d2c67c76d275a42ca7a8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: adición de ADFS como proveedor de identidades de SAML mediante directivas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo se muestra cómo habilitar el inicio de sesión para usuarios de la cuenta de ADFS mediante [directivas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Requisitos previos

Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

Estos pasos incluyen:

1.  Crear una relación de confianza para usuario autenticado de ADFS
2.  Agregar el certificado de relación de confianza para usuario autenticado de ADFS a Azure AD B2C
3.  Agregar el proveedor de notificaciones a una directiva
4.  Registrar el proveedor de notificaciones de la cuenta de ADFS en un recorrido del usuario
5.  Cargar la directiva en un inquilino de Azure AD B2C y probarla

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Para crear una relación de confianza para usuario autenticado basada en notificaciones, siga estos pasos:

Para usar ADFS como proveedor de identidades en Azure Active Directory (Azure AD) B2C, debe crear una relación de confianza para usuario autenticado de ADFS y suministrarle los parámetros correctos.

Para agregar una nueva relación de confianza para usuario autenticado mediante el complemento de administración de AD FS (Servicios de federación de Active Directory) y configurar manualmente los valores, realice el procedimiento siguiente en un servidor de federación.

Se requiere al menos la pertenencia al grupo **Administradores**, o equivalente, en el equipo local para realizar este procedimiento. Revise los detalles acerca del uso de las cuentas adecuadas y la pertenencia a grupos en [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477) (Grupos locales y de dominio).

1.  En el Administrador del servidor, haga clic en **Herramientas** y, luego, seleccione **ADFS Management** (Administración de ADFS).

2.  Seleccione **Add Relying Party Trust** (Agregar relación de confianza para usuario autenticado).
    ![Asistente para agregar relación de confianza para usuario autenticado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  En la página de **bienvenida**, elija **Claims aware** (Compatible con notificaciones) y haga clic en **Iniciar**.
    ![En la página de bienvenida, selección de Claims aware (Compatible con notificaciones)](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  En la página **Seleccionar origen de datos**, haga clic en **Enter data about the relying party manually** (Escribir manualmente los datos sobre el usuario de confianza) y haga clic en **Siguiente**.
    ![Especificación de los datos sobre el usuario de confianza](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  En la página **Especificar nombre para mostrar**, escriba un nombre en **Nombre para mostrar**, en **Notas** escriba una descripción de esta relación de confianza para usuario autenticado y luego haga clic en **Siguiente**.
    ![Especificación del nombre para mostrar y las notas](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Opcional. Si tiene un certificado de cifrado de tokens opcional, en la página **Configurar certificado**, haga clic en **Examinar** para buscar el archivo de certificado y, a continuación, haga clic en **Siguiente**.
    ![Configuración del certificado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  En la página **Configure URL** (Configurar URL), active la casilla **Enable support for the SAML 2.0 WebSSO protocol** (Habilitar la compatibilidad con el protocolo SAML 2.0 WebSSO). En **Relying party SAML 2.0 SSO service URL** (URL del servicio SSO de SAML 2.0 del usuario de confianza), escriba la URL del punto de conexión de servicio de Lenguaje de marcado de aserción de seguridad (SAML) correspondiente a esta relación de confianza para usuario autenticado y luego haga clic en **Siguiente**.  En **Relying party SAML 2.0 SSO service URL** (URL del servicio SSO de SAML 2.0 del usuario de confianza), pegue la dirección `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Reemplace {tenant} por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com) y {policy} por el nombre de la directiva de extensiones (por ejemplo, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >El nombre de la directiva es el que se hereda de la directiva signup_or_signin, en este caso `B2C_1A_TrustFrameworkExtensions`.
    >Por ejemplo, la dirección URL podría ser: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Dirección URL del servicio SSO de SAML 2.0 del usuario de confianza](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. En la página **Configure Identifiers** (Configurar identificadores), especifique la misma dirección URL que en el paso anterior, haga clic en **Agregar** para agregarla a la lista y, luego, haga clic en **Siguiente**.
    ![Identificadores de relación de confianza para usuario autenticado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  En **Choose Access Control Policy** (Elegir directiva de control de acceso), seleccione una directiva y haga clic en **Siguiente**.
    ![Elección de la directiva de Access Control](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  En la página **Ready to Add Trust** (Listo para agregar confianza), revise la configuración y luego haga clic en **Siguiente** para guardar la información de la relación de confianza para usuario autenticado.
    ![Guardado de la información de relación de confianza para usuario autenticado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  En la página **Finalizar**, haga clic en **Cerrar**; esta acción muestra automáticamente el cuadro de diálogo **Edit Claim Rules** (Editar reglas de notificación).
    ![Edición de reglas de notificación](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Haga clic en **Agregar regla**.  
      ![Adición de nueva regla](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  En **Claim rule template** (Plantilla de regla de notificación), seleccione **Send LDAP attributes as claims** (Enviar atributos LDAP como notificaciones).
    ![Selección de Enviar atributos LDAP como regla de plantilla de notificaciones](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Proporcione el **nombre de la regla de notificación**. Para **Attribute store** (Almacén de atributos), seleccione **Select Active Directory** (Seleccionar Active Directory), agregue las siguientes notificaciones y haga clic en **Finalizar** y luego en **Aceptar**.
    ![Establecimiento de las propiedades de la regla](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  En el Administrador del servidor, seleccione **Relying Party Trusts** (Relaciones de confianza para usuario autenticado), seleccione la relación de confianza para usuario autenticado que ha creado y haga clic en **Propiedades**.
    ![Propiedades de edición de usuario de confianza](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Una vez en la ventana de propiedades de la relación de confianza para usuario autenticado (demo B2C), haga clic en la pestaña **Firma** y luego en **Agregar**.  
    ![Establecimiento de firma](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Agregue el certificado de firma (archivo .cert, sin clave privada).  
    ![Adición del certificado de firma](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  En la ventana de propiedades de la relación de confianza para usuario autenticado (demo B2C), haga clic en la pestaña **Avanzado** y cambie el **algoritmo hash seguro** a **SHA-1** y luego haga clic en **Aceptar**.  
    ![Establecimiento del algoritmo hash seguro a SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adición de la clave de aplicación de la cuenta de ADFS a Azure AD B2C
La federación con cuentas de ADFS requiere un secreto de cliente para que la cuenta de ADFS confíe en Azure AD B2C en nombre de la aplicación. Debe almacenar el certificado de ADFS en el inquilino de Azure AD B2C. 

1.  Vaya a su inquilino de Azure AD B2C y seleccione **B2C Settings (Configuración de B2C)** > **Marco de experiencia de identidad**.
2.  Seleccione **Policy Keys** (Claves de directiva) para ver las claves disponibles en el inquilino.
3.  Haga clic en **+Agregar**.
4.  En **Opciones**, use **Cargar**.
5.  En **Nombre**, use `ADFSSamlCert`.  
    Es posible que se agregue automáticamente el prefijo `B2C_1A_`.
6.  En la carga de archivos, ** seleccione el archivo .pfx de certificado con clave privada. Nota: Este certificado (con la clave privada) debe ser el mismo que el que se emite y se usa con el usuario de confianza de ADFS.
![Carga de la clave de directiva](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Haga clic en **Crear**
8.  Confirme que ha creado la clave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adición de un proveedor de notificaciones en la directiva de extensión
Si quiere que los usuarios inicien sesión mediante la cuenta de ADFS, debe definir esta cuenta como un proveedor de notificaciones. En otras palabras, debe especificar un punto de conexión con el que Azure AD B2C se comunica. El punto de conexión proporciona un conjunto de notificaciones que Azure AD B2C usa para comprobar que un usuario concreto se ha autenticado.

Defina la cuenta de ADFS como proveedor de notificaciones; para ello, agregue el nodo `<ClaimsProvider>` en el archivo de directiva de extensión:

1. Abra el archivo de directiva de extensión (TrustFrameworkExtensions.xml) desde el directorio de trabajo. Si necesita un editor XML, [pruebe Visual Studio Code](https://code.visualstudio.com/download), un editor multiplataforma ligero.
2. Busque la sección `<ClaimsProviders>`.
3. Agregue el siguiente fragmento de código XML en el elemento `ClaimsProviders` y reemplace `identityProvider` por el DNS (valor arbitrario que indica el dominio) y guarde el archivo. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registro del proveedor de notificaciones de la cuenta de ADFS para el recorrido del usuario de registro o inicio de sesión
En este momento, el proveedor de identidades se ha configurado.  Sin embargo, no está disponible en ninguna de las pantallas de registro o inicio de sesión. Ahora tiene que agregar el proveedor de identidades de la cuenta de ADFS al recorrido del usuario `SignUpOrSignIn` de su usuario. Para que esté disponible, se crea un duplicado de un recorrido del usuario de plantilla ya existente.  Luego se modifica, de forma que incluya el proveedor de identidades de ADFS:

>[!NOTE]
>Si copió anteriormente el elemento `<UserJourneys>` del archivo base de su directiva en el archivo de extensión (TrustFrameworkExtensions.xml), puede omitir esta sección.

1.  Abra el archivo base de la directiva (por ejemplo, TrustFrameworkBase.xml).
2.  Busque el elemento `<UserJourneys>` y copie el contenido entero del nodo `<UserJourneys>`.
3.  Abra el archivo de extensión (por ejemplo, TrustFrameworkExtensions.xml) y busque el elemento `<UserJourneys>`. Si el elemento no existe, agréguelo.
4.  Pegue el contenido entero del nodo `<UserJournesy>` que copió como secundario del elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Visualización del botón
El elemento `<ClaimsProviderSelections>` define la lista de opciones de selección del proveedor de notificaciones y su orden.  El elemento `<ClaimsProviderSelection>` es análogo a un botón de proveedor de identidades de una página de registro o inicio de sesión. Si agrega un elemento `<ClaimsProviderSelection>` para la cuenta de ADFS, se muestra un nuevo botón cuando un usuario llega a la página. Para agregar este elemento:

1.  Busque el nodo `<UserJourney>` que incluye `Id="SignUpOrSignIn"` en el recorrido del usuario que ha copiado.
2.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
3.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción

Ahora que hay un botón colocado, es preciso vincularlo a una acción. En este caso, la acción es para que Azure AD B2C se comunique con la cuenta de ADFS para recibir un token. Vincule el botón a una acción mediante la vinculación del perfil técnico del proveedor de notificaciones de la cuenta de ADFS:

1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Actualice `Id` con el mismo valor que el de `TargetClaimsExchangeId` en la sección anterior.
> * Asegúrese de que `TechnicalProfileReferenceId` esté establecido en el perfil técnico que creó anteriormente (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Carga de la directiva en el inquilino
1.  En [Azure Portal](https://portal.azure.com), cambie al [contexto del inquilino de Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) y abra la hoja **Azure AD B2C**.
2.  Seleccione **Marco de experiencia de identidad**.
3.  Abra la hoja **Todas las directivas**.
4.  Seleccione **Cargar directiva**.
5.  Active la casilla **Sobrescribir la directiva si existe**.
6.  **Cargue** TrustFrameworkExtensions.xml y asegúrese de que no se produce ningún error en la validación.

## <a name="test-the-custom-policy-by-using-run-now"></a>Probar la directiva personalizada con Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
2.  Abra **B2C_1A_signup_signin**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de ADFS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registro del proveedor de notificaciones de la cuenta de ADFS en el recorrido del usuario de edición de perfil
Puede que también desee agregar el proveedor de identidades de la cuenta de ADFS al recorrido del usuario `ProfileEdit` de su usuario. Para que esté disponible, se repiten los dos últimos pasos:

### <a name="display-the-button"></a>Visualización del botón
1.  Abra el archivo de extensión de su directiva (por ejemplo, TrustFrameworkExtensions.xml).
2.  Busque el nodo `<UserJourney>` que incluye `Id="ProfileEdit"` en el recorrido del usuario que ha copiado.
3.  Busque el nodo `<OrchestrationStep>` que incluye `Order="1"`.
4.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular el botón a una acción
1.  Busque el nodo `<OrchestrationStep>` que incluye `Order="2"` en el nodo `<UserJourney>`.
2.  Agregue el siguiente fragmento de código XML en el nodo `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Prueba de la directiva de edición de perfil personalizada mediante Ejecutar ahora
1.  Abra la **configuración de Azure AD B2C** y vaya a **Marco de experiencia de identidad**.
2.  Abra **B2C_1A_ProfileEdit**, que es la directiva personalizada del usuario de confianza (RP) que ha cargado. Seleccione **Ejecutar ahora**.
3.  Debería poder iniciar sesión con la cuenta de ADFS.

## <a name="download-the-complete-policy-files"></a>Descarga de los archivos de directiva completos
Opcional: se recomienda que cree su escenario con sus propios archivos de directiva personalizada tras completar el tutorial de introducción a las directivas personalizadas. [Archivos de directiva de ejemplo solo de referencia](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
