---
title: "Complejidad de contraseñas en las directivas personalizadas: Azure AD B2C | Microsoft Docs"
description: "Cómo configurar los requisitos de complejidad para las contraseñas en una directiva personalizada"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: eb187a120399089f1c3c145a06fbe993f50fb92b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>Configuración de la complejidad de contraseñas en las directivas personalizadas

> [!NOTE]
> **Esta característica se encuentra en versión preliminar.**  Póngase en contacto con [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) para que su inquilino de prueba tenga esta característica habilitada.  No pruebe esto en inquilinos de producción.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artículo es una descripción avanzada de cómo funciona y cómo se habilita la complejidad de contraseñas utilizando las directivas personalizadas de Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: configuración de los requisitos de complejidad para las contraseñas

Azure Active Directory B2C (Azure AD B2C) admite el cambio de los requisitos de complejidad de las contraseñas proporcionadas por un usuario final al crear una cuenta.  De forma predeterminada, Azure AD B2C utiliza contraseñas **seguras**.  Azure AD B2C también admite opciones de configuración para controlar la complejidad de las contraseñas que los clientes pueden usar.  En este artículo se habla sobre cómo configurar la complejidad de las contraseñas en las directivas personalizadas.  También es posible utilizar [la configuración de complejidad de contraseñas en las directivas integradas](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Requisitos previos

Configuración de un inquilino de Azure AD B2C para completar el registro o inicio de sesión de una cuenta local como se describe en [Introducción](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Procedimientos para la configuración de la complejidad de contraseñas en las directivas personalizadas

Para configurar la complejidad de las contraseñas en una directiva personalizada, la estructura general de la directiva personalizada tiene que incluir un elemento `ClaimsSchema`, `Predicates`, y `InputValidations` dentro de `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

El propósito de estos elementos es el siguiente:

- Cada elemento `Predicate` define una comprobación de validación básica de cadena que devuelve un valor true o false.
- El elemento `InputValidations` contiene uno o más elementos `InputValidation`.  Cada `InputValidation` se construye utilizando una serie de elementos `Predicate`. Este elemento le permite realizar agregaciones booleanas (similares a `and` y `or`).
- El elemento `ClaimsSchema` define cuál es la notificación que se está validando.  A continuación, define que regla `InputValidation` se usa para validar esa notificación.

### <a name="defining-a-predicate-element"></a>Definición de un elemento de predicado

Los predicados tienen dos tipos de métodos: IsLengthRange o MatchesRegex. Vamos a ver un ejemplo de cada uno.  En primer lugar, tenemos un ejemplo de MatchesRegex, que se utiliza para realizar una coincidencia con una expresión regular.  En este ejemplo, la coincidencia es con una cadena que contiene números.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

A continuación vamos a ver un ejemplo de IsLengthRange.  Este método toma una longitud de cadena mínima y máxima.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Use el atributo `HelpText` para proporcionar un mensaje de error para los usuarios finales si se produce un error en la comprobación.  Esta cadena se puede localizar con el [característica de personalización de idioma](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definición de un elemento InputValidation

Un elemento `InputValidation` es una agregación de `PredicateReferences`. Cada `PredicateReferences` tiene que ser true para que `InputValidation` sea correcta.  Sin embargo, dentro del elemento `PredicateReferences` se usa un atributo denominado `MatchAtLeast` para especificar cuántas comprobaciones `PredicateReference` tienen que devolver true.  Opcionalmente, se puede definir un atributo `HelpText` para invalidar el mensaje de error definido en los elementos `Predicate` al que hace referencia.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definición de un elemento ClaimsSchema

Los tipos de notificación `newPassword` y `reenterPassword` se consideran especiales, por lo que no se pueden cambiar los nombres.  La interfaz de usuario valida si el usuario volvió a escribir su contraseña correctamente durante la creación de cuentas en función de estos elementos `ClaimType`.  Para buscar los mismos elementos `ClaimType`, busque en TrustFrameworkBase.xml en el módulo de inicio.  Lo que este ejemplo tiene de nuevo es que estamos reemplazando estos elementos para definir una `InputValidationReference`. El atributo `ID` de este nuevo elemento apunta al elemento `InputValidation`que hemos definido.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Resumen

Este ejemplo muestra cómo encajan todas las piezas para formar una directiva de trabajo.  Para usar este ejemplo:

1. Siga las instrucciones que aparecen en los requisitos previos del artículo de [introducción](active-directory-b2c-get-started-custom.md) para descargar, configurar y cargar TrustFrameworkBase.xml y TrustFrameworkExtensions.xml
1. Cree un archivo SignUporSignIn.xml utilizando el contenido del ejemplo de esta sección.
1. Actualice SignUporSignIn.xml reemplazando `yourtenant` con su nombre de inquilino de Azure AD B2C.
1. Cargue el archivo de directiva SignUporSignIn.xml al final.

Este ejemplo contiene una validación para contraseñas de pin y otra para las contraseñas seguras:

- Busque `PINpassword`. Este elemento `InputValidation` valida un pin de cualquier longitud.  No se usa en este momento, porque no tiene referencia en el elemento `InputValidationReference` dentro de `ClaimType`. 
- Busque `PasswordValidation`. Este elemento `InputValidation` valida una contraseña que tenga de 8 y 16 caracteres y contenga 3 de 4 números, letras mayúsculas, minúsculas o símbolos.  Tiene referencia en `ClaimType`.  Por lo tanto, esta regla se va a aplicar a esta directiva.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
