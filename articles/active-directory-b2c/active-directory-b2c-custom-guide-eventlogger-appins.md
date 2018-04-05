---
title: Seguimiento del comportamiento del usuario mediante eventos en Application Insights desde Azure AD B2C | Microsoft Docs
description: Guía paso a paso para habilitar los registros de eventos en Application Insights de recorridos del usuario de Azure AD B2C mediante directivas personalizadas (versión preliminar)
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Seguimiento del comportamiento del usuario dentro de los recorridos de Azure AD B2C mediante Application Insights

Azure Active Directory B2C funciona bien con Azure Application Insights.  Ambos proporcionan registros de eventos detallados y personalizados para los recorridos del usuario creados personalizados.  En esta guía se muestra cómo empezar a trabajar para poder: 
* Obtener información detallada sobre el comportamiento del usuario
* Solucionar problemas con sus propias directivas durante la fase de desarrollo o producción
* Medir el rendimiento
* Crear notificaciones de Application Insights

## <a name="how-it-works"></a>Cómo funciona
Se ha agregado un nuevo proveedor al marco de experiencia de identidad de Azure AD B2C: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Este proveedor envía los datos de eventos directamente a Application Insights mediante la clave de instrumentación proporcionada a Azure AD B2C.  Un perfil técnico usa este proveedor para definir un evento de B2C.  El perfil especifica el nombre del evento, las notificaciones que se registrarán y la clave de instrumentación.  Para publicar un evento, el perfil técnico se agrega entonces como `orchestration step` o `validation technical profile` en un recorrido del usuario personalizado.  Los eventos se pueden unificar mediante Application Insights por medio de un identificador de correlación para registrar una sesión de usuario.  Application Insights permite que el evento y la sesión estén disponibles en cuestión de segundos y presenta muchas herramientas de visualización, exportación y análisis.



## <a name="prerequisites"></a>requisitos previos
Realice los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).  En esta guía se da por supuesto que se está usando el módulo de inicio de directiva personalizada.  Sin embargo, no es obligatorio.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Paso 1. Creación de un recurso de Application Insights y obtención de la clave de instrumentación

Application Insights es una herramienta muy eficaz. Cuando la use con Azure AD B2C, el único requisito es crear un recurso y obtener una clave de instrumentación.  Application Insights se debe crear en [Azure Portal.](https://portal.azure.com)

[Documentación completa de Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Haga clic en `+ Create a resource` en Azure Portal, dentro de su inquilino de suscripción.  Este inquilino no es el inquilino de Azure AD B2C.  
2. Busque y seleccione `Application Insights`.  
3. Cree un recurso de `Application Type` `ASP.NET web application` en una suscripción de su elección.
4. Una vez creado, abra el recurso de Application Insights y anote  el valor de `Instrumentation Key`. 

![Introducción a Application Insights y la clave de instrumentación](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Paso 2. Adición de nuevas definiciones ClaimType al archivo de extensiones del marco de confianza

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Abra el archivo de extensiones desde el módulo de inicio y agregue los siguientes elementos al nodo `<BuildingBlocks>`.  El nombre de archivos de extensiones suele ser `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!--Additional claims used for passing claims to Application Insights Provider -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Paso 3. Adición de nuevos perfiles técnicos que usan el proveedor de Application Insights

Los perfiles técnicos pueden considerarse funciones en el marco de la experiencia de identidad de Azure AD B2C.  En este ejemplo se definen cinco perfiles técnicos para abrir una sesión y publicar eventos.

| Perfil técnico       | Task |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | Conjunto común de parámetros que se incluirán en todos los perfiles técnicos de Azure Insights.     | 
| JourneyContextForInsights   | Abre la sesión en App Insights ay envía un identificador de correlación. |
| AzureInsights-SignInRequest     | Crea un evento de inicio de sesión con un conjunto de notificaciones cuando se ha recibido una solicitud de inicio de sesión.      | 
| AzureInsights-UserSignup | Crea un evento denominado "UserSignup" cuando el usuario ha desencadenado la opción de suscripción en un recorrido de suscripción o inicio de sesión.     | 
| AzureInsights-SignInComplete | Registra la finalización correcta de una autenticación cuando se ha enviado un token a la aplicación de usuario autenticado.     | 

Agregue los perfiles al archivo de extensiones desde el módulo de inicio mediante la adición de estos elementos al nodo `<ClaimsProviders>`.  El nombre de archivos de extensiones suele ser `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

> [!IMPORTANT]
> Cambie el valor de `Instrumentation Key` del perfil técnico `ApplicationInsights-Common` por el GUID que ha proporcionado el recurso de Application Insights.

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Paso 4 Adición de los perfiles técnicos de Application Insights como pasos de orquestación en un recorrido del usuario existente

Llame a `JournyeContextForInsights` como paso de orquestación 1.

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Llame a `Azure-Insights-SignInRequest` como paso de orquestación 2 para realizar el seguimiento de que se ha recibido una solicitud de inicio de sesión o suscripción.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Inmediatamente **antes** del paso de orquestación `SendClaims`, agregue un nuevo paso que llame a `Azure-Insights-UserSignup`. Este paso se desencadena cuando se ha hecho clic el botón de suscripción en un recorrido de suscripción o inicio de sesión.

```xml
        <!-- Handles the user clicking the sign up link in the local account sign in page -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

Inmediatamente después del paso de orquestación `SendClaims`, llame a `Azure-Insights-SignInComplete`.   Este paso refleja un recorrido completado correctamente.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Después de agregar los nuevos pasos de orquestación, renumere los pasos de forma secuencial sin omitir los enteros de 1 a N


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Paso 5. Carga del archivo de extensiones modificado, ejecución de la directiva y visualización de los eventos en Application Insights

Guarde y cargue el nuevo archivo de extensiones del marco de confianza.  A continuación, llame a la directiva de usuario de confianza desde su aplicación o use `Run Now` en la interfaz de Azure AD B2C.  En segundos, los eventos estarán disponibles en Application Insights.

1. Abra el recurso de Application Insights en su inquilino de Azure Active Directory.
2. Haga clic en `Events` en el submenú `USAGE`.
3. Establezca `During` en `Last hour` y `By` en `3 minutes`.  Puede que deba hacer clic en `Refresh` para ver los resultados.

![Hoja de eventos de uso de Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>PASOS SIGUIENTES

Agregue tipos de notificación y eventos adicionales a su recorrido del usuario según sus necesidades.  Esta es una lista de posibles notificaciones con solucionadores de notificaciones adicionales.

### <a name="culture-specific-claims"></a>Notificaciones específicas de la referencia cultural

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Notificaciones específicos de la directiva

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>Notificaciones específicas de OpenIDConnect

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parámetros sin protocolo incluidos con solicitudes OIDC y OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Cualquier nombre de parámetro incluido como parte de una solicitud OIDC u OAuth2 se puede asignar a una notificación en el recorrido del usuario.  Luego se puede registrar en el evento. Por ejemplo, la solicitud de la aplicación puede incluir un parámetro de cadena de consulta con el nombre de `app_session`, `loyalty_number` o `any_string`.

Solicitud de ejemplo de la aplicación:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Las notificaciones se pueden agregar luego al agregarse un elemento de notificación de entrada al evento de Application Insights mediante:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Otras notificaciones del sistema

Algunas notificaciones del sistema se deben agregar al contenedor de notificaciones para que estén disponibles para registrarse como eventos. Se debe llamar al perfil técnico `SimpleUJContext` como paso de orquestación o perfil técnico de validación para que estas notificaciones estén disponibles.

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



