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
ms.date: 04/16/2018
ms.author: davidmu
ms.openlocfilehash: db9ba62cb6ef0d2627dbeb07cb8165a4ac7ae490
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Seguimiento del comportamiento del usuario dentro de los recorridos de Azure AD B2C mediante Application Insights

Azure Active Directory B2C (Azure AD B2C) funciona bien con Azure Application Insights. Ambos proporcionan registros de eventos detallados y personalizados para los recorridos del usuario creados personalizados. En este artículo se muestra cómo empezar a trabajar para:

* Obtener información detallada sobre el comportamiento del usuario.
* Solucionar problemas con sus propias directivas durante la fase de desarrollo o producción.
* Medir el rendimiento.
* Crear notificaciones de Application Insights.

> [!NOTE]
> Esta característica se encuentra en su versión preliminar.

## <a name="how-it-works"></a>Cómo funciona

El Marco de experiencia de identidad en Azure AD B2C ahora incluye el proveedor `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Este proveedor envía los datos de eventos directamente a Application Insights mediante la clave de instrumentación proporcionada a Azure AD B2C.

Un perfil técnico usa este proveedor para definir un evento de B2C.  El perfil especifica el nombre del evento, las notificaciones que se registrarán y la clave de instrumentación.  Para publicar un evento, el perfil técnico se agrega entonces como `orchestration step` o `validation technical profile` en un recorrido del usuario personalizado.

Application Insights puede unificar los eventos por medio de un identificador de correlación para registrar una sesión de usuario. Application Insights permite que el evento y la sesión estén disponibles en cuestión de segundos y presenta muchas herramientas de visualización, exportación y análisis.

## <a name="prerequisites"></a>requisitos previos

Realice los pasos del artículo [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md). En este artículo se da por supuesto que está usando el módulo de inicio de la directiva personalizada. Pero el módulo de inicio no es necesario.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Paso 1. Creación de un recurso de Application Insights y obtención de la clave de instrumentación

Cuando usa Application Insights con Azure AD B2C, el único requisito es crear un recurso y obtener una clave de instrumentación. El recurso se crea en [Azure Portal](https://portal.azure.com).

1. En Azure Portal, dentro de su inquilino de suscripción, seleccione **+ Crear un recurso**. Este inquilino no es el inquilino de Azure AD B2C.  
2. Busque y seleccione **Application Insights**.  
3. Cree un recurso que use **aplicación web ASP.NET** como **tipo de aplicación**, en la suscripción que desee.
4. Después de crear el recurso de Application Insights, ábralo y anote la clave de instrumentación.

![Introducción a Application Insights y la clave de instrumentación](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Paso 2. Adición de nuevas definiciones ClaimType al archivo de extensiones del marco de confianza

Abra el archivo de extensiones desde el módulo de inicio y agregue los siguientes elementos al nodo `<BuildingBlocks>`. El nombre de archivo suele ser `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

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

Los perfiles técnicos pueden considerarse funciones en el Marco de experiencia de identidad de Azure AD B2C. En este ejemplo se definen cinco perfiles técnicos para abrir una sesión y publicar eventos:

| Perfil técnico | Task |
| ----------------- | -----|
| AzureInsights-Common | Crea un conjunto común de parámetros que se incluirán en todos los perfiles técnicos de Azure Insights. | 
| JourneyContextForInsights | Abre la sesión en Application Insights y envía un identificador de correlación. |
| AzureInsights-SignInRequest | Crea un evento `SignIn` con un conjunto de notificaciones cuando se ha recibido una solicitud de inicio de sesión. | 
| AzureInsights-UserSignup | Crea un evento UserSignup cuando el usuario activa la opción de inicio de sesión en un recorrido de inicio de sesión y registro. | 
| AzureInsights-SignInComplete | Registra la finalización correcta de una autenticación cuando se ha enviado un token a la aplicación de usuario autenticado. | 

Agregue los perfiles al archivo de extensiones desde el módulo de inicio mediante la adición de estos elementos al nodo `<ClaimsProviders>`.  El nombre de archivo suele ser `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

> [!IMPORTANT]
> Cambie la clave de instrumentación del perfil técnico de `ApplicationInsights-Common` por el GUID que su recurso de Application Insights proporciona.

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
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
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
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Paso 4 Adición de los perfiles técnicos de Application Insights como pasos de orquestación en un recorrido del usuario existente

Llame a `JournyeContextForInsights` como paso de orquestación 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Llame a `Azure-Insights-SignInRequest` como paso de orquestación 2 para realizar el seguimiento de que se ha recibido una solicitud de inicio de sesión o suscripción:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Inmediatamente *antes* del paso de orquestación `SendClaims`, agregue un nuevo paso que llame a `Azure-Insights-UserSignup`. Se desencadena cuando el usuario selecciona el botón de suscripción en un recorrido de inicio de sesión o suscripción.

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

Inmediatamente después del paso de orquestación `SendClaims`, llame a `Azure-Insights-SignInComplete`. Este paso refleja un recorrido completado correctamente.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Después de agregar los nuevos pasos de orquestación, vuelva a enumerar los pasos de forma secuencial sin omitir los enteros de 1 a N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Paso 5. Carga del archivo de extensiones modificado, ejecución de la directiva y visualización de los eventos en Application Insights

Guarde y cargue el nuevo archivo de extensiones del marco de confianza. A continuación, llame a la directiva de usuario de confianza desde su aplicación o use `Run Now` en la interfaz de Azure AD B2C. En segundos, los eventos están disponibles en Application Insights.

1. Abra el recurso de **Application Insights** en su inquilino de Azure Active Directory.
2. Seleccione **Uso** > **Eventos**.
3. Establezca **During** (Durante) en **Last hour** (Última hora) y **By** (por) en **3 minutes** (3 minutos).  Puede que tenga que seleccionar **Refresh** (Actualizar) para ver los resultados.

![Hoja de eventos de uso de Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Pasos siguientes

Agregue tipos de notificación y eventos a su recorrido del usuario según sus necesidades. Esta es una lista de posibles notificaciones con solucionadores de notificaciones adicionales.

### <a name="culture-specific-claims"></a>Notificaciones específicas de la referencia cultural

```xml
Referenced using: {Culture:One of the property names below}
```

| Notificación | Definición | Ejemplo |
| ----- | -----------| --------|
| LanguageName | Código ISO de dos letras para el idioma | en |
| RegionName | Código ISO de dos letras para la región | US |
| RFC5646 | Código de idioma RFC5646 | es-ES |
| LCID   | El identificador de configuración regional del código de idioma | 1033 |

### <a name="policy-specific-claims"></a>Notificaciones específicos de la directiva

```xml
Referenced using {Policy:One of the property names below}
```

| Notificación | Definición | Ejemplo |
| ----- | -----------| --------|
| TrustFrameworkTenantId | El identificador de inquilino de trustframework | N/D |
| RelyingPartyTenantId | El identificador de inquilino del usuario de confianza | N/D |
| PolicyId | El identificador de directiva de la directiva | N/D |
| TenantObjectId | El identificador de inquilino de la directiva | N/D |

### <a name="openid-connect-specific-claims"></a>Notificaciones específicas de OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Notificación | Parámetro OpenIdConnect | Ejemplo |
| ----- | ----------------------- | --------|
| Prompt | símbolo del sistema | N/D |
| LoginHint |  login_hint | N/D |
| DomainHint | domain_hint | N/D |
|  MaxAge | max_age | N/D |
| ClientId | client_id | N/D |
| Nombre de usuario | login_hint | N/D |
| Password | domain_hint | N/D |
|  Recurso | resource| N/D |
| AuthenticationContextReferences | acr_values | N/D |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parámetros sin protocolo incluidos con solicitudes OIDC y OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Cualquier nombre de parámetro incluido como parte de una solicitud OIDC u OAuth2 se puede asignar a una notificación en el recorrido del usuario. A continuación, puede registrar el evento. Por ejemplo, la solicitud de la aplicación puede incluir un parámetro de cadena de consulta con el nombre de `app_session`, `loyalty_number` o `any_string`.

Este es un ejemplo de solicitud de la aplicación:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Puede agregar notificaciones después agregando un elemento `Input Claim` al evento de Application Insights mediante:

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
```


