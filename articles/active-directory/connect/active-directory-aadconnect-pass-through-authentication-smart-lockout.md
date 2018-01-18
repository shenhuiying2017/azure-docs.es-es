---
title: "Azure AD Connect: Autenticación de paso a través - Bloqueo inteligente | Microsoft Docs"
description: "En este artículo se describe cómo la autenticación de paso a través de Azure Active Directory (Azure AD) protege las cuentas locales de ataques por fuerza bruta de contraseñas en la nube."
services: active-directory
keywords: "Autenticación de paso a través de Azure AD Connect, instalación de Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: billmath
ms.openlocfilehash: fc46fe1d68538757ba5a8c5aa1acb4b51f8a171b
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticación de paso a través de Azure Active Directory: Bloqueo inteligente

## <a name="overview"></a>Información general

Azure Active Directory (Azure AD) protege frente a los ataques de contraseña por fuerza bruta e impide que se bloquee a los usuarios legítimos de sus aplicaciones de Office 365 y SaaS. Esta capacidad, denominada *Bloqueo inteligente*, se admite cuando se usa la autenticación de paso a través como método de inicio de sesión. El bloqueo inteligente está habilitado de forma predeterminada para todos los inquilinos y protege continuamente las cuentas de usuario.

Además, realiza un seguimiento de los intentos de inicio de sesión fallidos. Después de un determinado *umbral de bloqueo*, se inicia una *duración de bloqueo*. El bloqueo inteligente rechaza los intentos de inicio de sesión del atacante durante la duración del bloqueo. Si el ataque continúa, los errores de inicio de sesión que se produzcan tras finalizar la duración del bloqueo generan una duración del bloqueo más larga.

>[!NOTE]
>El umbral de bloqueo predeterminado es de diez intentos fallidos. La duración del bloqueo predeterminado es de 60 segundos.

El bloqueo inteligente también distingue entre inicios de sesión de usuarios originales y de atacantes. En la mayoría de los casos, impide solo los de atacantes. Esta funcionalidad impide que los atacantes bloqueen de forma malintencionada a los usuarios originales. El bloqueo inteligente usa el comportamiento de inicio de sesión pasado, los exploradores y dispositivos de los usuarios y otras señales para distinguir entre atacantes y usuarios legítimos. Realizamos mejoras en los algoritmos constantemente.

La autenticación de paso a través reenvía las solicitudes de validación de contraseñas a la instancia local de Active Directory, de modo que tendrá que evitar que los atacantes bloqueen las cuentas de Active Directory de sus usuarios. Active Directory tiene sus propias directivas de bloqueo de cuenta: [Umbral de bloqueo de cuenta](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) y [Restablecer contador de bloqueo de cuenta tras](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx). Configure los valores de duración y umbral de bloqueo de Azure AD correctamente para filtrar ataques en la nube antes de que lleguen al sistema local en Active Directory.

>[!NOTE]
>>La característica de bloqueo inteligente es gratuita y está _activada_ de forma predeterminada para todos los clientes. Sin embargo, la modificación de los valores de Umbral de bloqueo y Duración del bloqueo de Azure AD mediante la API Graph requiere que el inquilino disponga al menos de una licencia de Azure AD Premium P2 activa. 

Para asegurarse de que las cuentas locales de Active Directory de los usuarios están bien protegidas, debe asegurarse de que:

   * El umbral de bloqueo de Azure AD sea _inferior_ al umbral de bloqueo de cuenta de Active Directory. Establezca los valores de modo que el umbral de bloqueo de cuenta de Active Directory sea al menos dos o tres veces mayor que el umbral de bloqueo de Azure AD.
   * La duración del bloqueo de Azure AD (representada en segundos) es _mayor_ que la del contador de restablecimiento de bloqueo de cuenta de AD (representada en minutos).

>[!IMPORTANT]
>Actualmente, un administrador no puede desbloquear cuentas en la nube de los usuarios si estos han sido bloqueados por la funcionalidad de bloqueo inteligente. El administrador deberá esperar a que expire la duración del bloqueo.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Comprobación de las directivas de bloqueo de cuenta de Active Directory

Siga las instrucciones siguientes para comprobar las directivas de bloqueo de cuentas de Active Directory:

1.  Abra las herramientas de administración de directivas de grupo.
2.  Edite la directiva de grupo que se aplica a todos los usuarios, por ejemplo, la **directiva de dominio predeterminada**.
3.  Vaya a **Configuración del equipo** > **Directivas** > **Configuración de Windows** > **Configuración de seguridad**  >  **Directivas de cuenta** > **Directiva de bloqueo de cuenta**.
4.  Compruebe los valores de **Umbral de bloqueo de cuenta** y **Restablecer contador de bloqueo de cuenta tras**.

![Directivas de bloqueo de cuenta de Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Uso de la API Graph para administrar los valores de bloqueo inteligente del inquilino (requiere una licencia Premium)

>[!IMPORTANT]
>La modificación de los valores de umbral y duración de bloqueo de Azure AD mediante la API Graph es una característica de Azure AD Premium P2. También debe ser administrador global en su inquilino.

Puede usar el [Probador de Graph](https://developer.microsoft.com/graph/graph-explorer) para leer, establecer y actualizar los valores de bloqueo inteligente de Azure AD. También puede realizar estas operaciones mediante programación.

### <a name="view-smart-lockout-values"></a>Consulta de los valores de bloqueo inteligente

Siga estos pasos para ver los valores de bloqueo inteligente de su inquilino:

1. Inicie sesión en el Probador de Graph como administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en **Modificar permisos** y seleccione el permiso **Directory.ReadWrite.All**.
3. Configure la solicitud de la API Graph de este modo: establezca la versión en **BETA**, el tipo de solicitud en **GET** y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Haga clic en **Ejecutar consulta** para ver los valores de bloqueo inteligente del inquilino. Si no ha establecido los valores del inquilino antes, verá un conjunto vacío.

### <a name="set-smart-lockout-values"></a>Establecer valores de bloqueo de inteligente

Siga estos pasos para establecer los valores de bloqueo inteligente del inquilino (se requiere para la primera vez):

1. Inicie sesión en el Probador de Graph como administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en **Modificar permisos** y seleccione el permiso **Directory.ReadWrite.All**.
3. Configure la solicitud de la API Graph de este modo: establezca la versión en **BETA**, el tipo de solicitud en **POST** y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie y pegue la siguiente solicitud JSON en el campo **Cuerpo de la solicitud**.
5. Haga clic en **Ejecutar consulta** para establecer los valores de bloqueo inteligente del inquilino.

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
>Puede dejar los valores **BannedPasswordList** y **EnableBannedPasswordCheck** como vacíos (**""**) y **false** respectivamente si no los usa.

Compruebe que haya establecido correctamente los valores de bloqueo inteligente del inquilino mediante los pasos que se describen en [Ver los valores de Bloqueo inteligente](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Actualizar los valores de bloqueo de inteligente

Siga estos pasos para actualizar los valores de bloqueo inteligente del inquilino (si ya los ha configurado antes):

1. Inicie sesión en el Probador de Graph como administrador global del inquilino. Si se le solicita, conceda acceso para los permisos solicitados.
2. Haga clic en **Modificar permisos** y seleccione el permiso **Directory.ReadWrite.All**.
3. [Siga estos pasos para ver los valores de bloqueo inteligente de su inquilino](#view-smart-lockout-values). Copie el valor `id` (GUID) del elemento con **displayName** como **PasswordRuleSettings**.
4. Configure la solicitud de la API Graph de este modo: establezca la versión en **BETA**, el tipo de solicitud en **PATCH** y la dirección URL en `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Use el GUID del paso 3 para `<id>`.
5. Copie y pegue la siguiente solicitud JSON en el campo **Cuerpo de la solicitud**. Cambie los valores de bloqueo inteligente según corresponda.
6. Haga clic en **Ejecutar consulta** para actualizar los valores de bloqueo inteligente del inquilino.

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

Compruebe que haya actualizado correctamente los valores de bloqueo inteligente del inquilino mediante los pasos que se describen en [Ver los valores de Bloqueo inteligente](#view-smart-lockout-values).

## <a name="next-steps"></a>pasos siguientes
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use el foro de Azure Active Directory para solicitar nuevas características.
