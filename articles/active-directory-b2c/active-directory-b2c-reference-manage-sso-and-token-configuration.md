---
title: "Azure Active Directory B2C: administración de SSO y personalización de token con directivas personalizadas | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: administración de SSO y personalización de token con directivas personalizadas
El uso de directivas personalizadas proporciona el mismo control sobre las configuraciones de token, sesión e inicio de sesión único (SSO) que las directivas integradas.  Para saber lo que hace cada una, consulte la documentación [aquí](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configuración de notificaciones y duración del token
Para cambiar la configuración de la duración del token, debe agregar un elemento `<ClaimsProviders>` en el archivo de usuario de confianza de la directiva que quiere modificar.  El elemento `<ClaimsProviders>` es secundario de `<TrustFrameworkPolicy>`.  Dentro debe colocar la información que afecta a la duración del token.  El XML tiene este aspecto:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Duración del token de acceso**: la duración del token de acceso se puede cambiar mediante la modificación del valor dentro de `<Item>` con la clave = "token_lifetime_secs" en segundos.  El valor predeterminado en el elemento integrado es 3600 (60 minutos).

**Duración del token del identificador**: la duración del token del identificador se puede cambiar mediante la modificación del valor `<Item>` con la clave = "id_token_lifetime_secs" en segundos.  El valor predeterminado en el elemento integrado es 3600 (60 minutos).

**Duración del token de actualización**: la duración del token de actualización se puede cambiar mediante la modificación del valor dentro de `<Item>` con la clave = "refresh_token_lifetime_secs" en segundos.  El valor predeterminado en el elemento integrado es 1209600 segundos (14 días).

**Duración de la ventana deslizante del token de actualización**: si desea establecer una duración de ventana deslizante para su token de actualización, modifique el valor dentro de `<Item>` con la clave Key="rolling_refresh_token_lifetime_secs" en segundos.  El valor predeterminado del elemento integrado es 7776000 (90 días).  Si no desea exigir una duración de ventana deslizante, reemplace esta línea con:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Notificación de emisor (iss)**: si desea cambiar la notificación del emisor (iss), modifique el valor dentro de `<Item>` con la clave = "IssuanceClaimPattern".  Los valores aplicables son `AuthorityAndTenantGuid` y `AuthorityWithTfp`.

**Configuración de notificación que representa el identificador de directiva**: las opciones para configurar este valor son TFTP (directiva de marco de confianza) y ACR (referencia de contexto de autenticación).  
Se recomienda establecer este valor en TFTP; para ello, asegúrese de que exista `<Item>` con la clave = "AuthenticationContextReferenceClaimPattern" y que el valor sea `None`.
En su elemento `<OutputClaims>`, agregue este elemento:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Para ACR, quite `<Item>` con la clave= "AuthenticationContextReferenceClaimPattern".

**Notificación de asunto (sub)**: esta opción se establece de forma predeterminada en ObjectID; si desea cambiarlo a `Not Supported`, haga lo siguiente:

Reemplace esta línea 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
por esta otra:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportamiento de sesión y SSO
Para cambiar las configuraciones de comportamiento de sesión y SSO, debe agregar un elemento `<UserJourneyBehaviors>` dentro del elemento `<RelyingParty>`.  El elemento `<UserJourneyBehaviors>` debe seguir inmediatamente a `<DefaultUserJourney>`.  El interior de su elemento `<UserJourneyBehavors>` debe ser similar al siguiente:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configuración de inicio de sesión único (SSO)**: para cambiar la configuración del inicio de sesión único, debe modificar el valor de `<SingleSignOn>`.  Los valores aplicables son `Tenant`, `Application`, `Policy` y `Disabled`. 

**Duración de sesión de aplicación web (minutos)**: para cambiar la duración de la sesión de aplicación web, debe modificar el valor del elemento `<SessionExpiryInSeconds>`.  El valor predeterminado en las directivas integradas es 86400 segundos (1440 minutos).

**Tiempo de espera de sesión de aplicación web**: para cambiar el tiempo de espera de sesión de una aplicación web, debe modificar el valor de `<SessionExpiryType>`.  Los valores aplicables son `Absolute` y `Rolling`.
