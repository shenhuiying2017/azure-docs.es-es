---
title: "Integración de intercambios de notificaciones de API de REST de Azure AD B2C como validación en directivas personalizadas | Microsoft Docs"
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
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
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como validación en entradas de usuario

El **marco de experiencia de identidad** (IEF) subyacente a Azure AD B2C permite al desarrollador de identidades integrar una interacción con una API de RESTful en un recorrido de usuario.  

Al final de este tutorial podrá crear recorridos de usuario de Azure AD B2C que interactúan con servicios RESTful.

El IEF envía datos en notificaciones y recibe los datos en notificaciones.  La interacción con la API se puede diseñar como un intercambio de notificaciones de API de REST, a modo de un perfil de validación, que tiene lugar dentro de un paso de orquestación.

- Normalmente se valida la entrada del usuario.
- Si se rechaza el valor del usuario, el usuario puede intentar escribir de nuevo un valor válido con la posibilidad de que reciba un mensaje de error.

También se puede diseñar la interacción como un paso de orquestación. Para más información, consulte [Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](active-directory-b2c-rest-api-step-custom.md).

En el ejemplo de perfil de validación, usaremos el recorrido de usuario de edición de perfil del archivo del paquete de inicio ProfileEdit.xml.

Podemos comprobar que el nombre especificado proporcionado por el usuario en la edición del perfil no es parte de una lista de excluidos.

## <a name="prerequisites"></a>Requisitos previos

- Configuración de un inquilino de Azure AD B2C para completar el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).
- Un punto de conexión de API de REST con el que interactuar. Se ha configurado un sitio de demostración [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) con un servicio de API de REST que se usará en este tutorial.

## <a name="step-1---prepare-the-rest-api-function"></a>Paso 1: Preparación de la función de API de REST

> [!NOTE]
> La configuración de funciones de la API de REST escapa del ámbito de este artículo. [Azure Function Apps](https://docs.microsoft.com/azure/azure-functions/functions-reference) proporciona un excelente conjunto de herramientas para crear servicios de RESTful en la nube.

Hemos creado una función de Azure que recibe una notificación que se espera como "playerTag" y que se valida tanto si existe esta notificación como si no. Puede acceder al código completo de la función de Azure en [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

El marco de experiencia de identidad espera la notificación `userMessage` que devuelve la función de Azure. Esta notificación se presenta como una cadena al usuario si la validación produce error, como cuando se devuelve un estado de conflicto 409 en el ejemplo anterior.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Paso 2: Configuración del intercambio de notificaciones de API de RESTful como perfil técnico en el archivo TrustFrameworkExtensions.xml

Un perfil técnico es la configuración completa del intercambio deseado con el servicio RESTful. Abra el archivo `TrustFrameworkExtensions.xml` y agregue el fragmento de código XML siguiente dentro del elemento `<ClaimsProviders>`.

> [!NOTE]
> Considere el proveedor de Restful, versión 1.0.0.0 que se describe a continuación como protocolo, la función que interactuará con el servicio externo.  Puede encontrar una definición completa del esquema en <!-- TODO: Link to RESTful Provider schema definition>-->.

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

El elemento `InputClaims` define las notificaciones que se enviarán desde el IEE al servicio REST. En el ejemplo anterior, el contenido de las notificaciones `givenName` se enviaba al servicio REST como `playerTag`. En este ejemplo, IEE no espera que se devuelvan notificaciones, sino que espera una respuesta del servicio REST y actúa en función de los códigos de estado recibidos.

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>Paso 3: Inclusión del intercambio de notificaciones del servicio RESTful en un perfil técnico autoafirmado donde desea validar la entrada del usuario

El uso más común del paso de validación es en la interacción con un usuario.  Todas las interacciones en las que se espera que el usuario proporcione una entrada, son **perfiles técnicos autoafirmados**. En este ejemplo, agregaremos esta validación al perfil técnico (TP) **Self-Asserted-ProfileUpdate**.  Se trata del TP usado en el archivo de directiva de RP `Profile Edit`.

Para agregar el intercambio de notificaciones al perfil técnico autoafirmado:

1. Abra el archivo TrustFrameworkBase y busque `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Revise la configuración de este TP y observe cómo el intercambio con el usuario se define como notificaciones que se pedirán al usuario (notificaciones de entrada) y notificaciones que se espera que devuelva el proveedor autoafirmado (notificaciones de salida).
3. Busque `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`; observe que este perfil se invoca como el paso de orquestación n.º 6 de `<UserJourney Id="ProfileEdit">`.

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>Paso 4: Carga y prueba del archivo de directiva RP de edición de perfil

1. Cargue la nueva versión del archivo `TrustframeworkExtensions`.
2. Use **Ejecutar ahora** para probar el archivo de directiva RP de edición de perfil.
3. Para probar la validación, proporcione uno de los nombres existentes (por ejemplo: mcvinny) en el campo **Nombre**. Si todo está configurado correctamente, debería recibir un mensaje que informa al usuario de que `player tag` ya está en uso.

## <a name="next-steps"></a>Pasos siguientes

[Modificación de la edición de perfil y el registro de usuario para recopilar información adicional de sus usuarios](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Tutorial: Integración de intercambios de notificaciones de API de REST en los recorridos de usuario de Azure AD B2C como un paso de orquestación](active-directory-b2c-rest-api-step-custom.md)

