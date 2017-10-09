---
title: "Azure AD Connect: Autenticación de paso a través - Bloqueo inteligente | Microsoft Docs"
description: "En este artículo se describe cómo la autenticación de paso a través de Azure Active Directory (Azure AD) protege las cuentas locales de ataques por fuerza bruta de contraseñas en la nube."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 7e05c469260a445578c80cdf77fab2d5ffb48022
ms.contentlocale: es-es
ms.lasthandoff: 09/28/2017

---

# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticación de paso a través de Azure Active Directory: Bloqueo inteligente

## <a name="overview"></a>Información general

Azure AD protege frente a los ataques de contraseña por fuerza bruta e impide que se bloquee a los usuarios originales de sus aplicaciones de Office 365 y SaaS. Esta capacidad, denominada **Bloqueo inteligente**, se admite cuando se usa la autenticación de paso a través como método de inicio de sesión. Bloqueo inteligente está habilitado de forma predeterminada para todos los inquilinos y las cuentas de usuario se protegen en todo momento; no es necesario activarlo.

Bloqueo inteligente funciona mediante el seguimiento de los errores de inicio de sesión y después de un determinado **Umbral de bloqueo**, a partir de una **Duración del bloqueo**. Durante la duración del bloqueo se rechazan los intentos de inicio de sesión del atacante. Si el ataque continúa, los errores de inicio de sesión siguientes después de que finalice la duración del bloqueo generan una duración del bloqueo más larga.

>[!NOTE]
>El valor predeterminado del umbral de bloqueo es de 10 intentos incorrectos y la duración del bloqueo predeterminada es de 60 segundos.

Bloqueo inteligente también distingue entre inicios de sesión de usuarios originales y de los atacantes, y solo bloquea a los atacantes en la mayoría de los casos. Esta funcionalidad impide que los atacantes bloqueen de forma malintencionada a los usuarios originales. Se usa el comportamiento de inicio de sesión pasado, los exploradores y dispositivos de los usuarios, y otras señales para distinguir entre atacantes y usuarios originales. Estamos mejorando constantemente nuestros algoritmos.

Como la autenticación de paso a través reenvía las solicitudes de validación de contraseñas a la instancia local de Active Directory (AD), tendrá que evitar que los atacantes bloqueen las cuentas de AD de sus usuarios. Como tiene sus propias directivas de bloqueo de cuentas de AD (en concreto, [**Umbral de bloqueo de cuenta**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) y [**Restablecer contador de bloqueo de cuenta después de** ](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), debe configurar los valores de umbral de bloqueo y duración del bloqueo de Azure AD adecuadamente para filtrar ataques en la nube antes de que lleguen a la instancia local de AD.

>[!NOTE]
>La característica de bloqueo inteligente es gratuita y está _activada_ de forma predeterminada para todos los clientes. Sin embargo, la modificación de los valores de Umbral de bloqueo y Duración del bloqueo de Azure AD mediante la API Graph requiere que el inquilino disponga al menos de una licencia de Azure AD Premium P2. No se necesita una licencia de Azure AD Premium P2 _por usuario_ para habilitar la característica de bloqueo inteligente con la autenticación de paso a través.

Para asegurarse de que las cuentas locales de AD de los usuarios están bien protegidas, debe asegurarse de que:

1.  El umbral de bloqueo de Azure AD es _menos_ que el umbral de bloqueo de cuentas de AD. Se recomienda establecer los valores de modo que el umbral de bloqueo de cuentas de AD sea al menos dos o tres veces el umbral de bloqueo de Azure AD.
2.  La duración del bloqueo de Azure AD (representada en segundos) es _mayor_ que el valor de Restablecer el bloqueo de cuenta después de AD de (representado en minutos).

## <a name="verify-your-ad-account-lockout-policies"></a>Comprobar las directivas de bloqueo de cuentas de AD

Use las siguientes instrucciones para comprobar las directivas de bloqueo de cuentas de AD:

1.  Abra las herramientas de administración de directivas de grupo.
2.  Edite la directiva de grupo que se aplica a todos los usuarios, por ejemplo, la directiva de dominio predeterminada.
3.  Vaya a Configuración del equipo\Directivas\Configuración de Windows\Configuración de seguridad\Directivas de cuenta\Directiva de bloqueo.
4.  Compruebe los valores de Umbral de bloqueo de cuenta y Restablecer contador de bloqueo de cuenta después de.

![Directivas de bloqueo de cuentas de AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Use la API Graph para administrar los valores de Bloqueo inteligente del inquilino (necesita licencia Premium)

>[!IMPORTANT]
>Modificar los valores de umbral de bloqueo y duración del bloqueo de Azure AD mediante la API de Graph es una característica de Azure AD Premium P2. También debe ser un administrador global en su inquilino.

Puede usar [Explorador de gráficos](https://developer.microsoft.com/graph/graph-explorer) para leer, establecer y actualizar los valores de bloqueo inteligente de Azure AD. Pero también puede realizar estas operaciones mediante programación.

### <a name="read-smart-lockout-values"></a>Leer valores de bloqueo de inteligente

Siga estos pasos para leer los valores de bloqueo inteligente del inquilino:

1. Inicie sesión en el Explorador de gráficos como un administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en "Modificar permisos" y seleccione el permiso "Directory.ReadWrite.All".
3. Configure la solicitud de la API de Graph como sigue: establezca la versión en "BETA", el tipo de solicitud en "GET" y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Haga clic en "Ejecutar consulta" para ver los valores de bloqueo inteligente del inquilino. Si no ha establecido los valores del inquilino antes, verá un conjunto vacío.

### <a name="set-smart-lockout-values"></a>Establecer valores de bloqueo de inteligente

Siga estos pasos para establecer los valores de bloqueo inteligente del inquilino (solo para la primera vez):

1. Inicie sesión en el Explorador de gráficos como un administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en "Modificar permisos" y seleccione el permiso "Directory.ReadWrite.All".
3. Configure la solicitud de la API de Graph como sigue: establezca la versión en "BETA", el tipo de solicitud en "POST" y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie y pegue la siguiente solicitud de JSON en el campo "Cuerpo de la solicitud".
5. Haga clic en "Ejecutar consulta" para establecer los valores de bloqueo inteligente del inquilino.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Si no los está usando, puede dejar los valores **BannedPasswordList** y **EnableBannedPasswordCheck** como vacío ("") y "false" respectivamente.

Compruebe que ha establecido correctamente los valores de bloqueo inteligente del inquilino mediante [estos pasos](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Actualizar los valores de bloqueo de inteligente

Siga estos pasos para actualizar los valores de bloqueo inteligente del inquilino (si ya los ha configurado antes):

1. Inicie sesión en el Explorador de gráficos como un administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en "Modificar permisos" y seleccione el permiso "Directory.ReadWrite.All".
3. [Siga estos pasos para leer los valores de bloqueo inteligente del inquilino](#read-smart-lockout-values). Copie el valor `id` (GUID) del elemento con "displayName" como "PasswordRuleSettings".
4. Configure la solicitud de la API de Graph como sigue: establezca la versión en "BETA", el tipo de solicitud en "PATCH" y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` (use el GUID del Paso 3 para `<id>`).
5. Copie y pegue la siguiente solicitud de JSON en el campo "Cuerpo de la solicitud". Cambie los valores de bloqueo inteligente según corresponda.
6. Haga clic en "Ejecutar consulta" para actualizar los valores de bloqueo inteligente del inquilino.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Compruebe que ha actualizado correctamente los valores de bloqueo inteligente del inquilino mediante [estos pasos](#read-smart-lockout-values).

## <a name="next-steps"></a>Pasos siguientes
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para rellenar solicitudes de características nuevas.

