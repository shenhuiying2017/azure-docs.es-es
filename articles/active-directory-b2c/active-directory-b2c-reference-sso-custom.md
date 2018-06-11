---
title: Administración de sesiones de SSO mediante directivas personalizadas en Azure Active Directory B2C | Microsoft Docs
description: Obtenga información sobre cómo administrar sesiones SSO mediante directivas personalizadas en Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e05b057f6d7bfe0d836bb31e3fc656c17d4f18e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34710182"
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: administración de sesión de inicio de sesión único (SSO)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C permite a un administrador controlar cómo Azure AD B2C interactúa con un usuario una vez que el usuario se ha autenticado. Esto se realiza a través de la administración de sesiones de SSO. Por ejemplo, el administrador puede controlar si se muestra la selección de proveedores de identidades, o si se tienen que volver a especificar los detalles de la cuenta local. En este artículo se describe cómo establecer la configuración de SSO para Azure AD B2C.

## <a name="overview"></a>Información general

La administración de sesiones SSO tiene dos partes. La primera trata las interacciones del usuario directamente con Azure AD B2C y la otra las interacciones del usuario con partes externas, como Facebook. Azure AD B2C no invalida ni omite las sesiones de SSO que se pueden mantener mediante entidades externas. En su lugar, se "recuerda" la ruta a través de Azure AD B2C para llegar a la entidad externa, lo que hace que no sea necesario volver a solicitar al usuario que seleccione su proveedor de identidades sociales o de empresa. La decisión de SSO definitiva sigue teniéndola la parte externa.

## <a name="how-does-it-work"></a>¿Cómo funciona?

La administración de sesiones de SSO utiliza la misma semántica que cualquier otro perfil técnico en directivas personalizadas. Cuando se ejecuta un paso de orquestación, el perfil técnico asociado al paso se consulta para la referencia `UseTechnicalProfileForSessionManagement`. Si existe alguno, se comprueba el proveedor de sesión de SSO al que se hace referencia para ver si el usuario es un participante de la sesión. Si es así, se utiliza el proveedor de sesión de SSO para volver a llenar la sesión. De forma similar, una vez completada la ejecución de un paso de orquestación, se utiliza el proveedor para almacenar información en la sesión si se ha especificado un proveedor de sesión de SSO.

Azure AD B2C ha definido un número de proveedores de sesión de SSO que se pueden usar:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Las clases de administración de SSO se especifican mediante el elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` de un perfil técnico.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como el nombre indica, este proveedor no hace nada. Este proveedor se puede utilizar para suprimir el comportamiento de SSO de un perfil técnico específico.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Este proveedor se puede utilizar para almacenar notificaciones en una sesión. Se suele hacer referencia a dicho proveedor en un perfil técnico que se usa para administrar cuentas locales. 

> [!NOTE]
> Cuando use DefaultSSOSessionProvider para almacenar notificaciones en una sesión, debe asegurarse de que todas las que deben devolverse a la aplicación o las que deben usar las condiciones previas en los pasos posteriores, se almacenan en la sesión o se aumentan por una lectura desde el perfil del usuario en el directorio. Esto garantizará que no se producirán errores del proceso de autenticación en las notificaciones que faltan.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Para agregar notificaciones en la sesión, utilice el elemento `<PersistedClaims>` del perfil técnico. Cuando se utiliza el proveedor para volver a llenar la sesión, las notificaciones persistentes se agregan al contenedor de notificaciones. `<OutputClaims>` se utiliza para recuperar notificaciones de la sesión.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Este proveedor se utiliza para suprimir la pantalla "elegir el proveedor de identidades". Normalmente se hace referencia en un perfil técnico configurado para un proveedor de identidades externo, como Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este proveedor se utiliza para administrar las sesiones SAML de Azure AD B2C entre aplicaciones, así como los proveedores de identidad SAML externos.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Hay dos elementos de metadatos en el perfil técnico:

| item | Valor predeterminado | Valores posibles | DESCRIPCIÓN
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true/false | Indica al proveedor que se debe almacenar el índice de la sesión. |
| RegisterServiceProviders | true | true/false | Indica que el proveedor debe registrar todos los proveedores de servicios SAML que han emitido una aserción. |

Cuando se utiliza el proveedor para almacenar una sesión de proveedor de identidades SAML, los elementos anteriores deben ser false. Cuando se utiliza el proveedor para almacenar la sesión SAML de B2C, los elementos anteriores deben ser true u omitirse, ya que los valores predeterminados son true.

>[!NOTE]
> El cierre de sesión de SAML requiere que se completen `SessionIndex` y `NameID`.

## <a name="next-steps"></a>Pasos siguientes

Nos encantan los comentarios y las sugerencias. Si tiene dificultades con este tema, publique en Stack Overflow mediante la etiqueta ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Para las solicitudes de características, vote por ellas en nuestro [foro de comentarios](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

