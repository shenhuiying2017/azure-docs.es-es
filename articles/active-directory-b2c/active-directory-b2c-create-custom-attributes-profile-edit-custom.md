---
title: "Azure Active Directory B2C: adición de sus propios atributos a las directivas personalizadas y su uso en la edición del perfil | Microsoft Docs"
description: "Un tutorial acerca de cómo utilizar las propiedades de extensión y los atributos personalizados, y cómo incluirlos en la interfaz de usuario"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 0d4ee064c15c914eea7353900c6bb5a77b3e3b3b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: creación y uso de atributos personalizados en una directiva de edición de perfil personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En este artículo, creará un atributo personalizado en un directorio de Azure AD B2C y lo usará como notificación personalizada en el recorrido del usuario de la edición del perfil.

## <a name="prerequisites"></a>Requisitos previos

Complete los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Uso de los atributos personalizados para recopilar información acerca de los clientes en Azure Active Directory B2C mediante directivas personalizadas
El directorio de Azure Active Directory (Azure AD) B2C incluye un conjunto integrado de atributos: Given Name, Surname, City, Postal Code, userPrincipalName, etc.  A menudo, necesitará crear sus propios atributos.  Por ejemplo:
* Una aplicación orientada al cliente necesita conservar un atributo como "LoyaltyNumber".
* Un proveedor de identidades tiene un identificador de usuario único que se debe guardar como "uniqueUserGUID"".
* Un recorrido del usuario personalizado necesita conservar el estado del usuario como "migrationStatus".

Con Azure AD B2C, puede ampliar el conjunto de atributos que se almacenan en cada cuenta de usuario. También puede leer y escribir estos atributos mediante la [API de Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

Las propiedades de extensión extienden el esquema de los objetos de usuario en el directorio.  Los términos propiedad de la extensión, atributo personalizado y notificación personalizada hacen referencia a la misma cosa en el contexto de este artículo y el nombre varía según el contexto (aplicación, objeto, directiva).

Las propiedades de extensión solo se pueden registrar en un objeto de aplicación, aunque pueden contener datos de un usuario. La propiedad está conectada a la aplicación. El objeto de aplicación debe tener acceso de escritura para registrar una propiedad de extensión. En un objeto individual se pueden escribir cien propiedades de extensión (de TODOS los tipos y TODAS las aplicaciones). Las propiedades de extensión se agregan al tipo de directorio de destino y se puede acceder a ellas de inmediato en el inquilino del directorio de Azure AD B2C.
Si la aplicación se elimina, también se quitan las propiedades de extensión, junto con los datos que contienen de todos los usuarios. Si la aplicación elimina una propiedad de extensión, se quita en los objetos de directorio de destino, y también se quitan los valores.

Las propiedades de extensión solo existen en el contexto de una aplicación registrada en el inquilino. El identificador de objeto de la aplicación debe estar incluido en el elemento TechnicalProfile que lo utiliza.

>[!NOTE]
>El directorio de Azure AD B2C normalmente incluye una aplicación de web llamada `b2c-extensions-app`.  Esta aplicación la usan principalmente las directivas integradas de b2c para las notificaciones personalizadas creadas a través de Azure Portal.  Se recomienda que sean los usuarios avanzados quienes usen esta aplicación para registrar extensiones para las directivas personalizadas de b2c.  En la sección Pasos siguientes de este artículo encontrará instrucciones para esto.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Creación de una nueva aplicación para almacenar las propiedades de extensión

1. Abra una sesión de exploración, vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con credenciales administrativas del directorio B2C que desea configurar.
1. Haga clic en **Azure Active Directory** en el panel de navegación izquierdo. Puede que tenga que buscarlo, para lo que debe seleccionar Más servicios>.
1. Seleccione **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicaciones**.
1. Especifique las siguientes entradas recomendadas:
  * Especifique un nombre para la aplicación web: **WebApp-GraphAPI-DirectoryExtensions**.
  * Tipo de aplicación: aplicación web o API
  * URL de inicio de sesión: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Seleccione **Crear. La finalización correcta aparece en las **notificaciones**.
1. Seleccione la aplicación web recién creada: **WebApp-GraphAPI-DirectoryExtensions**.
1. Seleccione la configuración de **Permisos necesarios**.
1. Seleccione la API **Windows Azure Active Directory**
1. Coloque una marca en los permisos de aplicación **Leer y escribir en datos de directorio** y seleccione **Guardar**.
1. Seleccione **Conceder permisos** y haga clic en **Sí** para confirmar.
1. Realice la copiar en el Portapapeles y guarde los siguientes identificadores de WebApp-GraphAPI-DirectoryExtensions > Configuración > Propiedades >
*  **Identificador de aplicación**. Ejemplo: `103ee0e6-f92d-4183-b576-8c3739027780`
* **Identificador de objeto**. Ejemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modificación de una directiva personalizada para agregar ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
><TechnicalProfile Id="AAD-Common"> es "común", porque sus elementos están incluidos y se reutilizan en todas las instancias de TechnicalProfiles de Azure Active Directory mediante el elemento: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>La primera vez que la instancia de TechnicalProfile escribe en la propiedad de extensión recién creada, puede surgir un error que se da una sola vez.  La propiedad de extensión se crea la primera vez que se utiliza.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Uso de la nueva propiedad de extensión o atributo personalizado en un recorrido del usuario


1. Abra el archivo del usuario de confianza (RP) que describe el recorrido del usuario de edición de la directiva.  Si va a empezar, es posible que sea aconsejable descargar la versión ya configurada del archivo RP-PolicyEdit directamente de la sección Directiva de Azure B2C personalizada de Azure Portal.  Como alternativa, abra el archivo XML desde la carpeta de almacenamiento.
2. Agregue una notificación personalizada `loyaltyId`.  Mediante la inclusión de la notificación personalizada en el elemento `<RelyingParty>`, se usa como en UserJourney TechnicalProfiles y se incluye en el token de la aplicación.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Agregue una definición de la notificación al archivo de la directiva de extensión `TrustFrameworkExtensions.xml` del elemento `<ClaimsSchema>` como se muestra a continuación.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Agregue la misma definición de la notificación al archivo de la directiva base `TrustFrameworkBase.xml`.  
>Normalmente no es preciso agregar una definición `ClaimType` tanto en el archivo base como en el archivo de extensiones, pero dado que los siguientes pasos agregarán extension_loyaltyId a TechnicalProfiles en el archivo base, el validador de la directiva rechazará la carga del archivo base si no la tiene.
>Es posible que sea útil realizar un seguimiento de la ejecución del recorrido del usuario denominado "ProfileEdit" del archivo TrustFrameworkBase.xml.  Busque el recorrido del usuario que tenga el mismo nombre en el editor y observe que en el paso 5 de la orquestación se invoca a TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Busque e inspeccione esta instancia de TechnicalProfile para familiarizarse con el flujo.
5. Agregue loyaltyId como notificación de entrada y salida en la instancia de TechnicalProfile "SelfAsserted ProfileUpdate"
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Agregue la notificación de TechnicalProfile "AAD-UserWriteProfileUsingObjectId" para conservar el valor de la notificación en la propiedad de extensión del usuario actual en el directorio.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Agregue la notificación de TechnicalProfile "AAD-UserReadUsingObjectId" para leer el valor del atributo de extensión cada vez que un usuario inicia sesión. Hasta ahora las instancias de TechnicalProfiles solo se han cambiado en el flujo de las cuentas locales.  Si el nuevo atributo se desea en el flujo de una cuenta social o federada, es preciso cambiar otro conjunto de instancias de TechnicalProfiles. Consulte los pasos siguientes.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>El elemento IncludeTechnicalProfile agrega todos los elementos de AAD-Common a esta instancia de TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Prueba de la directiva personalizada con "Ejecutar ahora"
1. Abra la **hoja de Azure AD B2C** y vaya a **Marco de experiencia de identidad > Directivas personalizadas**.
1. Seleccione la directiva personalizada que cargó y, luego, haga clic en el botón **Ejecutar ahora**.
1. Debe poder registrarse con una dirección de correo electrónico.

El token del identificador que se devuelve a la aplicación incluirá la nueva propiedad de extensión como notificación personalizada precedida por extension_loyaltyId. Vea el ejemplo.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Pasos siguientes

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Agregue la nueva notificación a los flujos de inicios de sesión de cuentas sociales cambiando las instancias de TechnicalProfiles que se enumeran a continuación. Estas dos instancias de TechnicalProfiles las usan los inicios de sesión de cuentas sociales o federadas para leer y escribir los datos de usuario, para lo que usan alternativeSecurityId como localizador del objeto de usuario.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Se usan los mismos atributos de extensión entre directivas integradas y personalizadas.
Cuando se agregan atributos de extensión (también conocidos como atributos personalizados) a través de la experiencia del portal, los atributos se registran mediante **b2c-extensions-app que existe en cada inquilino b2c.  Para usar estos atributos de extensión en la directiva personalizada:
1. En el inquilino b2c en portal.azure.com, vaya a **Azure Active Directory** y seleccione **Registros de aplicaciones**
2. Encuentre su **b2c-extensiones-app** y selecciónelo
3. En "Información esencial" anote el **Identificador de la aplicación** y el **Identificador del objeto**
4. Inclúyalos en sus metadatos de perfil técnico de AAD-Common de la siguiente forma:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Para mantener la consistencia con la experiencia del portal, cree estos atributos utilizando la interfaz de usuario del portal *antes* de usarlos en sus directivas personalizadas.  Cuando se crea un atributo "ActivationStatus" en el portal, tiene que hacer referencia a él como sigue:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referencia

* Un **perfil técnico (TP)** es un tipo de elemento que se puede considerar una *función* que define el nombre de un punto de conexión, sus metadatos y su protocolo, y que detalla el intercambio de notificaciones que el marco de experiencia de identidad debe realizar.  Cuando se llama a esta *función* en un paso de la orquestación o desde otra instancia de TechnicalProfile, quien realiza la llamada especifica InputClaims y OutputClaims como parámetros.


* En el artículo [Extensiones de esquema de directorio | Conceptos de la API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) se tratan exhaustivamente las propiedades de extensión.

>[!NOTE]
>Los atributos de extensión en la API Graph se denominan mediante la convención `extension_ApplicationObjectID_attributename`. Las directivas personalizadas hacen referencia a los atributos de extensión como extension_attributename, con lo que se omite ApplicationObjectId en el archivo XML
