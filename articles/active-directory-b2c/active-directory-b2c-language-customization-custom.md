---
title: "Azure Active Directory B2C: personalización de idioma con las directivas personalizadas | Microsoft Docs"
description: "Información sobre cómo usar localizar el contenido de las directivas personalizadas para varios idiomas"
services: active-directory-b2c
documentationcenter: 
author: sammak
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 11/13/2017
ms.author: sama
ms.openlocfilehash: 4ed9791d6590e3982a1bc79b96f8592995bc315c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
#<a name="language-customization-in-custom-policies"></a>Personalización de idioma en las directivas personalizadas

> [!NOTE]
> Esta característica está en versión preliminar pública.
> 

En las directivas personalizadas, la personalización de idioma funciona igual que en las directivas integradas.  Vea la [documentación](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) integrada que describe el comportamiento de cómo un idioma se elige en función de los parámetros y la configuración del explorador.

##<a name="enable-supported-languages"></a>Habilitación de los idiomas admitidos
Si no se especificaron las configuraciones regionales de interfaz de usuario y el explorador del usuario solicita uno de estos idiomas, los idiomas admitidos se muestran al usuario.  

Los idiomas admitidos se definen en `<BuildingBlocks>` con el formato siguiente:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

El idioma predeterminado y los idiomas admitidos se comportan de la misma manera que lo hacen en las directivas integradas.

##<a name="enable-custom-language-strings"></a>Habilitación de las cadenas de idioma personalizado

Para crear cadenas de idioma personalizado, hay que realizar dos pasos:
1. Edite el parámetro `<ContentDefinition>` de la página para especificar un identificador de recurso para los idiomas deseados.
2. Cree el parámetro `<LocalizedResources>` con identificadores correspondientes en `<BuildingBlocks>`.

Tenga en cuenta que puede colocar los parámetros `<ContentDefinition>` y `<BuildingBlock>` en el archivo de extensión o el archivo de directiva de confianza dependiendo de si desea que los cambios estén en todas las directivas heredadas o no.

###<a name="edit-the-contentdefinition-for-the-page"></a>Edición de ContentDefinition para la página

En cada página que desee localizar, puede especificar en `<ContentDefinition>` qué recursos de idioma buscar para cada código de idioma.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

En este ejemplo, las cadenas personalizadas de francés (fr) e inglés (en) se agregan a la página de inicio de sesión o registro unificada.  El parámetro `LocalizedResourcesReferenceId` de cada `LocalizedResourcesReference` es igual que su configuración regional, pero podría utilizar cualquier cadena como el identificador.  Para cada combinación de idioma y de página, tendrá que crear un parámetro `<LocalizedResources>` correspondiente tal y como se muestra a continuación.


###<a name="create-the-localizedresources"></a>Creación de LocalizedResources

Las invalidaciones se encuentran en `<BuildingBlocks>` y hay un parámetro `<LocalizedResources>` para cada página y lenguaje que ha especificado en el parámetro `<ContentDefinition>` de cada página.  Cada invalidación se especifica como un parámetro `<LocalizedString>`, como en el ejemplo siguiente:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Hay cuatro tipos de elementos de cadena en la página:

**ClaimsProvider**: etiquetas para los proveedores de identidades (Facebook, Google, Azure AD, etc.). **ClaimType**: etiquetas para los atributos y sus correspondientes textos de ayuda o errores de validación de campos. **UxElement**: otros elementos de cadena de la página que existen de forma predeterminada, como botones, vínculos o texto. **ErrorMessage**: mensajes de error de validación de formularios.

Asegúrese de que los parámetros `StringId` coinciden en la página que está usando estas invalidaciones, en caso contrario, la validación de directivas los bloqueará en la carga.  