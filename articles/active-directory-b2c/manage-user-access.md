---
title: Administración del acceso de usuarios en Azure Active Directory B2C | Microsoft Docs
description: Aprenda a identificar a los menores, recopilar datos sobre la fecha y país de nacimiento y a obtener la aceptación de términos de uso de la aplicación con Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 52cf34d56f87d2543fb272ce99dbe011bc0ea037
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34711134"
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Administración del acceso de usuarios en Azure AD B2C

Este artículo proporciona información acerca de cómo puede administrar el acceso de usuarios a las aplicaciones con Azure Active Directory (AD) B2C. La administración de acceso de la aplicación incluye:

- Identificación de los menores y control del acceso para usar la aplicación
- Solicitud del consentimiento de los padres para que los menores puedan usar las aplicaciones
- Recopilación de datos sobre la fecha y país de nacimiento del usuario
- Captura del contrato de términos de uso y control de acceso

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>Este artículo proporciona información que se puede utilizar para cumplir con sus obligaciones con respecto al RGPD. Si desea obtener información general sobre el RGPD, consulte la [sección sobre RGPD del Portal de confianza de servicios](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="control-minor-access"></a>Control del acceso de menores

Las aplicaciones y organizaciones pueden decidir impedir a los menores el uso de aplicaciones y servicios que no están dirigidos a este tipo de audiencia. Alternativamente, las aplicaciones y organizaciones pueden decidir aceptar a los menores y, posteriormente administrar el consentimiento de los padres, y proporcionar experiencias permitidas para los menores según lo dictado por las reglas de negocio y lo permitido por la ley. 

Si se identifica a un usuario como un menor, el flujo de usuario de Azure AD B2C se puede establecer en una de estas tres opciones:

- **Enviar un id_token de JWT firmado a la aplicación**: el usuario se registra en el directorio y se devuelve un token a la aplicación. A continuación, la aplicación empieza a usar las reglas de negocio. Por ejemplo, la aplicación puede continuar con un proceso de consentimiento de los padres. Para ello, elige recibir las notificaciones **ageGroup** y **consentProvidedForMinor** de la aplicación.
- **Enviar un token JSON sin firmar a la aplicación**: Azure AD B2C notifica a la aplicación que el usuario es un menor y proporciona el estado del consentimiento de los padres del usuario. A continuación, la aplicación empieza a usar las reglas de negocio. Un token JSON no completa una autenticación correcta con la aplicación. La aplicación debe procesar al usuario no autenticado según las notificaciones incluidas en el token JSON, entre las cuales puede que se incluyan **name**, **email**, **ageGroup** y **consentProvidedForMinor**.
- **Bloquear al usuario**: si el usuario es un menor y no se ha proporcionado el consentimiento de los padres.  En este caso, Azure AD B2C puede presentar al usuario una pantalla que le informa que está bloqueado.  No se emite ningún token, el acceso está bloqueado y no se crea la cuenta de usuario durante una operación de registro. Para implementar esto, puede proporcionar una página de contenido HTML/CSS adecuada para informar al usuario y ofrecer las opciones apropiadas. La aplicación no necesita ninguna otra acción adicional para los nuevos registros.

## <a name="get-parental-consent"></a>Obtención del consentimiento de los padres

En función de la normativa de la aplicación, puede que se solicite el consentimiento de los padres a un usuario verificado como adulto.  Azure AD B2C no proporciona ninguna experiencia para comprobar la edad de un individuo que, a continuación, permita que un adulto verificado pueda otorgar el consentimiento paterno a un menor.  Esta experiencia la debe proporcionar la aplicación u otro proveedor de servicios.

El siguiente es un ejemplo de flujo de usuario para recopilar el consentimiento de los padres:

1. Una operación de [Graph API de Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) identifica al usuario como un menor y devuelve los datos del usuario a la aplicación como un token JSON sin firmar.
2. La aplicación procesa el token JSON y muestra una pantalla al menor que le notifica que se necesita el consentimiento de los padres y solicita este en línea. 
3. Azure AD B2C muestra una operación de inicio de sesión en la que se permite al usuario iniciar sesión normalmente y emite un token a la aplicación que se ha establecido para que incluya **legalAgeGroupClassification = “minorWithParentalConsent”**. La aplicación recopila la dirección de correo electrónico del padre y comprueba que este es un adulto mediante una fuente de confianza como una oficina de identificación nacional, comprobación de licencias o de la tarjeta de crédito. Si el proceso es correcto, la aplicación solicita al menor que inicie sesión con el flujo de usuario de Azure AD B2C. Si se ha rechazado el consentimiento (por ejemplo, **legalAgeGroupClassification = "minorWithoutParentalConsent"**, Azure AD B2C devuelve un token JSON (no un inicio de sesión) a la aplicación para que vuelva a iniciar el proceso de consentimiento. Opcionalmente, es posible, personalizar el flujo de usuario para que un menor o un adulto pueda recuperar el acceso a la cuenta del menor enviando un código de registro a la dirección de correo electrónico del menor o la dirección de correo electrónico registrada del adulto.
4. La aplicación ofrece una opción al menor para revocar el consentimiento.
5. Si el menor o el adulto revoca el consentimiento, se puede usar Graph API de Azure AD para cambiar **consentProvidedForMinor** a **denegado**. Alternativamente, la aplicación puede elegir eliminar a un menor cuyo consentimiento se haya revocado. Opcionalmente, es posible personalizar el flujo de usuario de forma que el menor autenticado (o el padre que usa la cuenta del menor) pueda revocar el consentimiento. Azure AD B2C registra **consentProvidedForMinor** como **denegado**.

Para más información sobre **legalAgeGroupClassification**, **consentProvidedForMinor** y **ageGroup**, consulte [Tipo de recurso de usuario](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user). Para más información acerca de los atributos personalizados, consulte [Uso de atributos personalizados para recopilar información sobre los consumidores](active-directory-b2c-reference-custom-attr.md). Al dirigirse a atributos extendidos mediante Graph API de Azure AD, la versión extendida del atributo se debe usar como "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z"

## <a name="gather-date-of-birth-and-country-data"></a>Recopilación de datos sobre la fecha y el país de nacimiento

Algunas aplicaciones pueden depender de Azure AD B2C para recopilar la fecha de nacimiento y el país de procedencia de todos los usuarios durante el registro. Si la información sobre la fecha de nacimiento o el país no existe, se le puede solicitar al usuario en la siguiente operación de autenticación (inicio de sesión). Los usuarios no podrán continuar sin proporcionar información sobre la fecha de nacimiento y el país. Según el país y la fecha de nacimiento que se proporcione, Azure AD B2C determinará si la persona se considera un menor según la normativa legal de ese país. 

Un flujo de usuario personalizado puede recopilar la información sobre la fecha de nacimiento y el país y usar la transformación de notificaciones de Azure AD B2C para determinar **ageGroup** y conservar el resultado (o conservar directamente la información sobre la fecha de nacimiento y el país) en el directorio.

Los pasos siguientes muestran la lógica que se usa para calcular **ageGroup** a partir de la fecha de nacimiento:

1. Intente encontrar el país por su código en la lista. Si no se encuentra el país, revierta a **Predeterminado**.
2. Si el nodo **MinorConsent** está presente en el elemento de país:  <br>a. Calcule la fecha mínima en la que el usuario tendría que haber nacido para que se considere un adulto. Ejemplo: la fecha de nacimiento es 14/3/2015 y **MinorConsent** es 18. En este caso la fecha de nacimiento mínima tendría que ser 14/3/2000.
    <br>b. Compare la fecha de nacimiento mínima con la fecha de nacimiento real. Si la fecha de nacimiento mínima es anterior a la fecha de nacimiento del usuario, el cálculo devuelve **Menor** como grupo de edad.
3. Si el nodo **MinorNoConsentRequired** está presente en el elemento de país, repita los pasos 2a y 2b utilizando el valor de **MinorNoConsentRequired**. La salida de 2b devuelve **MinorNoConsentRequired** si la fecha de nacimiento mínima es anterior a la fecha de nacimiento del usuario. 
4. Si ninguno de los cálculos devuelve true, el cálculo devuelve **Adulto**.

Si una aplicación ha recopilado de forma confiable la información sobre la fecha de nacimiento y el país, puede usar GRAPH API para actualizar el registro del usuario con esta información. Por ejemplo: 

- Si se sabe que un usuario es un adulto, actualice el atributo del directorio **ageGroup** con un valor de **Adulto**.
- Si se sabe que un usuario es un menor, actualice el atributo del directorio **ageGroup** con un valor de **Menor** y establezca **consentProvidedForMinor** como apropiado.

Para más información acerca de la recopilación de datos sobre la fecha de nacimiento, consulte [Using age gating in Azure AD B2C](basic-age-gating.md) (Uso del control de edad en Azure AD B2C).

## <a name="capture-terms-of-use-agreement"></a>Captura del contrato de términos de uso

Cuando desarrolla la aplicación, normalmente captura la aceptación del usuario de los términos de uso dentro de sus aplicaciones, sin necesidad de participación, o con una participación mínima, del directorio del usuario.  Sin embargo, es posible usar un flujo de usuario de Azure AD B2C para recopilar la aceptación de los términos de uso, restringir el acceso a menos que se otorgue esta aceptación y aplicar la aceptación de cambios futuros en los términos de uso según la fecha de la última aceptación y la fecha de la última versión de los términos de uso.

Los **términos de uso** también pueden incluir la opción "Acepto compartir los datos con terceros".  La aceptación positiva de estas condiciones por parte de un usuario se puede recopilar técnicamente como una combinación, o es posible que el usuario pueda aceptar una y no la otra según las leyes y reglas de negocio locales.

Los pasos siguientes describen las funcionalidades para administrar los términos de uso:

1. Registre la aceptación de los términos de uso y la fecha de esta mediante Graph API y los atributos extendidos. Esto se puede hacer mediante los flujos de usuario integrados y otros personalizados. Se recomienda que use los atributos **extension_termsOfUseConsentDateTime** y **extension_termsOfUseConsentVersion**.
2. Cree una casilla de verificación obligatoria titulada "Aceptar términos de uso" y registre el resultado durante el proceso de registro. Esto se puede hacer mediante los flujos de usuario integrados y otros personalizados.
3. Azure AD B2C almacena el contrato de términos de uso y el consentimiento. Graph API se puede usar para consultar el estado de cualquier usuario mediante la lectura del atributo de extensión que se usó para registrar la respuesta, por ejemplo la lectura de **termsOfUseTestUpdateDateTime**. Esto se puede hacer mediante los flujos de usuario integrados y otros personalizados.
4. Solicite la aceptación de los términos de uso actualizados mediante la comparación de la fecha de aceptación con la fecha de la última versión de los términos de uso. Esto solo puede se puede hacer mediante un flujo de usuario personalizado. Use el atributo extendido **extension_termsOfUseConsentDateTime** y compare el valor con la notificación de **termsOfUseTextUpdateDateTime**. Si la aceptación es antigua, fuerce una nueva pantalla autoafirmada de aceptación. En caso contrario, bloquee el acceso mediante la lógica de directiva.
5. Solicite la aceptación de términos de uso actualizados comparando el número de versión de la aceptación con el último número de versión aceptado. Esto solo puede se puede hacer mediante un flujo de usuario personalizado. Use el atributo extendido **extension_termsOfUseConsentDateTime** y compare el valor con la notificación de **extension_termsOfUseConsentVersion**. Si la aceptación es antigua, fuerce una nueva pantalla autoafirmada de aceptación. En caso contrario, bloquee el acceso mediante la lógica de directiva.

La captura del consentimiento de los términos de uso se puede presentar al usuario en los siguientes escenarios:

- Se está registrando un nuevo usuario. Se muestran los términos de uso y se almacena el resultado del consentimiento.
- Un usuario está iniciando sesión y ya ha aceptado anteriormente el consentimiento de los términos de uso más recientes o activos. No se muestran los términos de uso.
- Un usuario está iniciando sesión y no ha aceptado anteriormente el consentimiento de los términos de uso más recientes o activos. Se muestran los términos de uso y se almacena el resultado del consentimiento.
- Un usuario está iniciando sesión y ya ha aceptado el consentimiento de unos términos de uso antiguos, los cuales ahora se han actualizado a una versión posterior. Se muestran los términos de uso y se almacena el resultado del consentimiento.

En la imagen siguiente se muestra el flujo de usuario recomendado:

![flujo de usuario de aceptación](./media/manage-user-access/user-flow.png) 

El siguiente es un ejemplo de consentimiento de términos de uso basado en DateTime en una notificación:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

El siguiente es un ejemplo de consentimiento de términos de uso basado en Version en una notificación:

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a eliminar y exportar datos de usuario en [Administración de datos del usuario](manage-user-data.md)
