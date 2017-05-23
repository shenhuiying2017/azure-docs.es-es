---
title: 'Tutorial: Intercambio de notificaciones de API de REST como paso en las directivas personalizadas B2C| Microsoft Docs'
description: "Un tema acerca de la integración de las directivas personalizadas de Azure Active Directory B2C con API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27d066cc6f985565a765989837e4a2744387a628
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-an-orchestration-step"></a>Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación

El **marco de experiencia de identidad** (IEF) subyacente a Azure AD B2C permite al desarrollador de identidades integrar una interacción con una API de RESTful en un recorrido de usuario.  

Al final de este tutorial podrá crear recorridos de usuario de Azure AD B2C que interactúan con servicios RESTful.

El IEF envía datos en notificaciones y recibe los datos en notificaciones.  El intercambio de notificaciones de API de REST se puede diseñar como un paso de orquestación.

- En este caso se puede desencadenar una acción externa, por ejemplo, se puede registrar un evento en una base de datos externa.
- También se puede usar para capturar un valor y posteriormente almacenarlo en la base de datos de usuario.
- Las notificaciones recibidas pueden usarse posteriormente para cambiar el flujo de ejecución.

También se puede diseñar la interacción como un perfil de validación. Para más información, consulte [Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como validación en entradas de usuario](active-directory-b2c-rest-api-validation-custom.md).

La situación es que cuando un usuario realiza una edición de perfil, nos gustaría buscar al usuario en un sistema externo, obtener la ciudad donde está registrado y devolver ese atributo como una notificación a la aplicación.

## <a name="prerequisites"></a>Requisitos previos

- Configuración de un inquilino de Azure AD B2C para completar el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).
- Un punto de conexión de API de REST con el que interactuar: en este tutorial se usa como ejemplo un webhook muy sencillo de Azure Function Apps.
- **Recomendado**: realice el [tutorial sobre el intercambio de notificaciones de API de REST como paso de validación](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1---prepare-the-rest-api-function"></a>Paso 1: Preparación de la función de API de REST

> [!NOTE]
> La configuración de funciones de la API de REST escapa del ámbito de este artículo. [Azure Function Apps](https://docs.microsoft.com/azure/azure-functions/functions-reference) proporciona un excelente conjunto de herramientas para crear servicios de RESTful en la nube.

Hemos configurado con una función de Azure que recibe una notificación `email` y devuelve simplemente la notificación `city` con el valor asignado de `Redmond`. La función de Azure de ejemplo está aquí: [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

La notificación `userMessage` que devuelve la función de Azure es opcional en este contexto y el IEF la ignorará.  Se podría usar posiblemente como un mensaje transmitido a la aplicación y presentado al usuario posteriormente.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

**Azure Function Apps** facilita la obtención de la dirección URL de la función, lo que incluye el identificador de la función específica.  En este caso, la dirección URL es: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==, y podría usarla con fines de prueba.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Paso 2: Configuración del intercambio de notificaciones de API de RESTful como perfil técnico en el archivo TrustFrameworkExtensions.xml

Un perfil técnico es la configuración completa del intercambio deseado con el servicio RESTful. Abra el archivo `TrustFrameworkExtensions.xml` y agregue el fragmento de código XML siguiente dentro del elemento `<ClaimsProvider>`.

> [!NOTE]
> Considere el proveedor de Restful, versión 1.0.0.0 que se describe a continuación como protocolo, la función que interactuará con el servicio externo.  Puede encontrar una definición completa del esquema en <!-- TODO: Link to RESTful Provider schema definition>-->.

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

El elemento `<InputClaims>` define las notificaciones que se enviarán desde el IEF al servicio REST. En el ejemplo anterior, el contenido de la notificación `givenName` se enviará al servicio REST como notificación `email`.  

El elemento `<OutputClaims>` define las notificaciones que esperará el IEF del servicio REST. Con independencia del número de notificaciones que se reciban, el IEF solo usa las identificadas aquí. En este ejemplo, una notificación recibida como `city` se asignará a una notificación del IEF `city`.

## <a name="step-3---add-a-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Paso 3: Adición de una nueva notificación `city` al esquema de su archivo TrustFrameworkExtensions.xml

La notificación `city` no se define en ninguna parte de nuestro esquema. Así que agregaremos una definición dentro del elemento `<BuildingBlocks>` que se puede encontrar al principio del archivo `TrustFrameworkExtensions.xml`.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4---include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-your-trustframeworkextensionsxml"></a>Paso 4: Inclusión del intercambio de notificaciones del servicio REST como paso de orquestación en el recorrido de usuario de edición de perfil del archivo TrustFrameworkExtensions.xml

Hemos decidido agregar el paso del recorrido de usuario de edición de perfil después de que el usuario se ha autenticado (pasos de orquestación del 1 al 4, consulte a continuación) y ha proporcionado la información de perfil actualizada (paso 5).

> [!NOTE]
> Hay muchos casos donde la llamada de API de REST puede usarse como paso de orquestación.  Como tal, se puede usar como actualización a un sistema externo una vez que un usuario ha realizado correctamente una tarea, por ejemplo, registro por primera vez o actualización de perfil para mantener la información sincronizada.  En este caso, se usa para aumentar la información proporcionada a la aplicación después de la edición del perfil.

Copie el código XML del recorrido de usuario de edición de perfil del archivo `TrustFrameworkBase.xml` al archivo `TrustFrameworkExtensions.xml` dentro del elemento `<UserJourneys>` y luego realice la modificación del paso 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Si el orden no coincide con su versión, asegúrese de insertarlo como paso antes del tipo `SendClaims` de ClaimsExchange.

El XML UserJourney final debe tener este aspecto:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the jwt token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5---add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Paso 5: Adición de la notificación "city" al archivo de directiva de usuario de confianza para que la notificación se envíe a la aplicación

Para ello, edite el archivo de RP `ProfileEdit.xml` y modifique el elemento `<TechnicalProfile Id="PolicyProfile">` para agregar lo siguiente: `<OutputClaim ClaimTypeReferenceId="city" />`.

Después de agregar la nueva notificación, el elemento TechnicalProfile tendrá el siguiente aspecto:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6---upload-your-changes-and-test"></a>Paso 6: Carga de los cambios y prueba

Las versiones existentes de la directiva se sobrescribirán.

1.    (OPCIONAL) Guarde la versión existente (mediante descarga) del archivo de extensiones antes de continuar.  Se recomienda no cargar varias versiones del archivo de extensiones para mantener baja la complejidad inicial.
2.    (OPCIONAL) Puede cambiar el nombre de la nueva versión del archivo de edición de directiva PolicyId mediante la modificación de PolicyId="B2C_1A_TrustFrameworkProfileEdit".
3.    Cargue el archivo de extensiones.
4.    Cargue el archivo de usuario de confianza de edición de directiva.
5.    Use **Ejecutar ahora** para probar la directiva.  Revise el token devuelto por el IEF a la aplicación.

Si todo está configurado correctamente, el token incluirá la nueva notificación `city`, con el valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Pasos siguientes

[Uso de una API de REST como paso de validación](active-directory-b2c-rest-api-validation-custom.md)

[Modificación de la edición de perfil para recopilar información adicional de sus usuarios](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

