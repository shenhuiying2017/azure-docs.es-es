---
title: "Azure Active Directory B2C: intercambios de notificaciones de API de REST como paso de orquestación | Microsoft Docs"
description: Tema sobre las directivas personalizadas de Azure Active Directory B2C que se integran con una API
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
ms.openlocfilehash: dc319c97e64e55861b84cc3943667418077a05d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación

El marco de experiencia de identidad (IEF) subyacente a Azure Active Directory B2C (Azure AD B2C) permite al desarrollador de identidades integrar una interacción con una API de RESTful en un recorrido del usuario.  

Al final de este tutorial podrá crear un recorrido del usuario de Azure AD B2C que interactúe con servicios RESTful.

El IEF envía datos en notificaciones y recibe los datos en notificaciones. El intercambio de notificaciones de la API de REST:

- Puede diseñarse como un paso de orquestación.
- Puede desencadenar una acción externa. Por ejemplo, puede registrar un evento en una base de datos externa.
- Puede usarse para capturar un valor y almacenarlo en la base de datos de usuario.

Puede usar las notificaciones recibidas posteriormente para cambiar el flujo de ejecución.

También puede diseñar la interacción como un perfil de validación. Para obtener más información, vea [Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como validación en entradas de usuario](active-directory-b2c-rest-api-validation-custom.md).

La situación es que cuando un usuario realiza una edición de perfil, nos gustaría:

1. Buscar al usuario en un sistema externo.
2. Obtener la ciudad donde está registrado.
3. Devolver ese atributo como una notificación a la aplicación.

## <a name="prerequisites"></a>Requisitos previos

- Configuración de un inquilino de Azure AD B2C para completar el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).
- Un punto de conexión de API de REST con el que interactuar. Este tutorial usa como ejemplo un webhook simple de la aplicación de función de Azure.
- *Recomendado*: realice el [tutorial sobre el intercambio de notificaciones de API de REST como paso de validación](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Paso 1: Preparación de la función de API de REST

> [!NOTE]
> La configuración de funciones de la API de REST está fuera del ámbito de este artículo. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) proporciona un excelente conjunto de herramientas para crear servicios de RESTful en la nube.

Hemos configurado una función de Azure que recibe una notificación denominada `email` y devuelve la notificación `city` con el valor asignado de `Redmond`. La función de Azure de ejemplo está en [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

La notificación `userMessage` que devuelve la función de Azure es opcional en este contexto y el IEF la ignorará. Se podría usar posiblemente como un mensaje transmitido a la aplicación y presentado al usuario posteriormente.

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

Una aplicación de función de Azure facilita la obtención de la dirección URL de la función, lo que incluye el identificador de la función específica. En este caso, la dirección URL es: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Puede usarla para realizar pruebas.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Paso 2: Configuración del intercambio de notificaciones de API de RESTful como perfil técnico en el archivo TrustFrameworkExtensions.xml

Un perfil técnico es la configuración completa del intercambio deseado con el servicio RESTful. Abra el archivo TrustFrameworkExtensions.xml y agregue el siguiente fragmento de código XML dentro del elemento `<ClaimsProvider>`.

> [!NOTE]
> En el siguiente código XML, el proveedor de RESTful `Version=1.0.0.0` se describe como el protocolo. Considérelo como la función que interactuará con el servicio externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

El elemento `<InputClaims>` define las notificaciones que se enviarán desde el IEF al servicio REST. En este ejemplo, el contenido de la notificación `givenName` se enviará al servicio REST como la notificación `email`.  

El elemento `<OutputClaims>` define las notificaciones que esperará el IEF del servicio REST. Independientemente del número de notificaciones que se reciban, el IEF solo usa las identificadas aquí. En este ejemplo, una notificación recibida como `city` se asignará a una notificación del IEF denominada `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Paso 3: Adición de la nueva notificación `city` al esquema de su archivo TrustFrameworkExtensions.xml

La notificación `city` todavía no se ha definido en ninguna parte de nuestro esquema. Por lo tanto, debe agregar una definición dentro del elemento `<BuildingBlocks>`. Encontrará dicho elemento al principio del archivo TrustFrameworkExtensions.xml.

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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Paso 4: Inclusión del intercambio de notificaciones del servicio REST como paso de orquestación en el recorrido del usuario de edición de perfil del archivo TrustFrameworkExtensions.xml

Agregue un paso al recorrido del usuario de edición de perfil después de que el usuario se haya autenticado (pasos de orquestación del 1 al 4 en el siguiente código XML) y haya proporcionado la información de perfil actualizada (paso 5).

> [!NOTE]
> Hay muchos casos donde la llamada de API de REST puede usarse como paso de orquestación. Como tal, se puede usar como actualización a un sistema externo una vez que un usuario haya realizado correctamente una tarea, por ejemplo, el registro por primera vez, o como actualización de perfil para mantener la información sincronizada. En este caso, se usa para aumentar la información proporcionada a la aplicación después de la edición del perfil.

Copie el código XML del recorrido del usuario de edición de perfil del archivo TrustFrameworkBase.xml en el archivo TrustFrameworkExtensions.xml dentro del elemento `<UserJourneys>`. Después, realice la modificación en el paso 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Si el orden no coincide con su versión, asegúrese de insertar el código como paso antes del tipo `SendClaims` de `ClaimsExchange`.

El código XML final del recorrido del usuario debe tener este aspecto:

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
        <!-- Add a step 6 to the user journey before the JWT token is created-->
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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Paso 5: Adición de la notificación `city` al archivo de directiva de usuario de confianza para que la notificación se envíe a la aplicación

Edite el archivo de usuario de confianza (RP) ProfileEdit.xml y modifique el elemento `<TechnicalProfile Id="PolicyProfile">` para agregar lo siguiente: `<OutputClaim ClaimTypeReferenceId="city" />`.

Después de agregar la nueva notificación, el perfil técnico tendrá el siguiente aspecto:

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

## <a name="step-6-upload-your-changes-and-test"></a>Paso 6: Carga de los cambios y prueba

Sobrescriba las versiones existentes de la directiva.

1.  (Opcional) Guarde la versión existente (mediante descarga) del archivo de extensiones antes de continuar. Para mantener baja la complejidad inicial, se recomienda no cargar varias versiones del archivo de extensiones.
2.  (Opcional) Cambie el nombre de la nueva versión del identificador de directiva del archivo de edición de directiva. Para ello, cambie `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Cargue el archivo de extensiones.
4.  Cargue el archivo de RP de edición de directiva.
5.  Use **Ejecutar ahora** para probar la directiva. Revise el token devuelto por el IEF a la aplicación.

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
