---
title: Estados de usuario de Microsoft Azure Multi-Factor Authentication
description: "Obtenga información sobre los estados de usuario en Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Exigencia de verificación en dos pasos para un usuario o grupo

Puede elegir uno de dos enfoques para exigir la verificación en dos pasos. La primera opción consiste en habilitar a cada usuario para Azure Multi-factor Authentication (MFA). Cuando los usuarios se habilitan de forma individual, realizan la verificación en dos pasos cada vez que inician sesión (con algunas excepciones, como cuando inician sesión desde direcciones IP de confianza o si se activa la característica _recordar dispositivos_). La segunda opción consiste en configurar una directiva de acceso condicional que requiere la verificación en dos pasos en determinadas condiciones.

>[!TIP] 
>Elija uno de estos métodos para requerir la verificación en dos pasos, no ambos. La habilitación de un usuario para Azure Multi-Factor Authentication invalida las directivas de acceso condicional.

## <a name="which-option-is-right-for-you"></a>¿Cuál es la opción adecuada para usted?

**Habilitar Azure Multi-Factor Authentication mediante el cambio de estado del usuario** es el enfoque tradicional para exigir la verificación en dos pasos. Funciona tanto en Azure MFA en la nube como en el servidor de Azure MFA. Todos los usuarios que habilita realizan la verificación en dos pasos cada vez que inician sesión. La habilitación de un usuario invalida las directivas de acceso condicional que pudieran afectar a dicho usuario. 

**Habilitar Azure Multi-Factor Authentication con una directiva de acceso condicional** es un enfoque más flexible para exigir la verificación en dos pasos. Sin embargo, solo funciona para Azure MFA en la nube y el _acceso condicional_ es una [característica de pago de Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Puede crear directivas de acceso condicional que se aplican a grupos, así como a usuarios individuales. Los grupos de alto riesgo pueden recibir más restricciones que los grupos de bajo riesgo o bien la verificación en dos pasos puede ser necesaria solo para aplicaciones de alto riesgo en la nube y omitida para las de bajo riesgo. 

Ambas opciones solicitan a los usuarios registrarse en Azure Multi-Factor Authentication la primera vez que inicien sesión después de activar los requisitos. Ambas opciones también funcionan con la [configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Habilitar Azure MFA mediante el cambio de estado del usuario

Las cuentas de usuario de Azure Multi-Factor Authentication tienen los siguientes tres estados:

| Status | DESCRIPCIÓN | Aplicaciones que no son de explorador afectadas | Aplicaciones que son de explorador afectadas | Autenticación moderna afectada |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Estado predeterminado para un usuario nuevo no inscrito en Azure MFA. |Sin  |Sin  |Sin  |
| habilitado |El usuario se ha inscrito en Azure MFA, pero no se ha registrado. La próxima vez que inicie sesión, se le pedirá registrarse. |Nº  Continúa funcionando hasta que se complete el proceso de registro. | Sí. Una vez que expire la sesión, se requerirá el registro de Azure MFA.| Sí. Una vez que expire el token de acceso, se requerirá el registro de Azure MFA. |
| Aplicado |El usuario se ha inscrito y ha completado el proceso de registro de Azure MFA. |Sí.  Las aplicaciones requieren contraseñas de aplicación. |Sí. Se requiere Azure MFA en el inicio de sesión. | Sí. Se requiere Azure MFA en el inicio de sesión. |

El estado de un usuario refleja si un administrador lo ha inscrito en Azure MFA, y si ha completado el proceso de registro.

Todos los usuarios comienzan con el estado *Deshabilitado*. Cuando inscribe usuarios en Azure MFA, cambia a *Habilitado*. Cuando los usuarios habilitados inician sesión y completan el proceso de registro, el estado cambia a *Aplicado*.  

### <a name="view-the-status-for-a-user"></a>Ver el estado de un usuario

Para acceder a la página donde puede ver y administrar los estados de usuario, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
   Seleccione ![Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Se abre una nueva página que muestra los estados de usuario.
   ![Estado de usuario de Multi-Factor Authentication (captura de pantalla)](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Cambiar el estado de un usuario

1. Use los pasos anteriores para acceder a la página de **usuarios** de Azure Multi-Factor Authentication.
2. Busque el usuario que desea habilitar para Azure MFA. Puede que necesite cambiar la vista en la parte superior. 
   ![Búsqueda de usuario (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Active la casilla situada junto a su nombre.
4. En el lado derecho, bajo los **pasos rápidos**, elija **Habilitar** o **Deshabilitar**.
   ![Habilitación del usuario seleccionado (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*Habilitado* significa que los usuarios cambian automáticamente a *Aplicado* cuando se registren en Azure MFA. No cambie manualmente el estado de usuario a *Aplicado*. 

5. Confirme la selección en la ventana emergente que se abre. 

Después de habilitar los usuarios, notifíquelos por correo electrónico. Debe indicarles que se les pedirá que se registren la próxima vez que inicien sesión. Además, si su organización utiliza aplicaciones sin explorador que no son compatibles con la autenticación moderna, deben crear contraseñas de aplicación. También puede incluir un vínculo a la [Guía del usuario final de Azure MFA](./end-user/multi-factor-authentication-end-user.md), que les ayudará a empezar a trabajar.

### <a name="use-powershell"></a>Uso de PowerShell
Para cambiar el estado del usuario mediante [Azure AD PowerShell](/powershell/azure/overview), debe cambiar `$st.State`. Hay tres estados posibles:

* habilitado
* Aplicado
* Disabled  

No mueva a los usuarios directamente a estado *Aplicado*. Si lo hace, las aplicaciones que no son de explorador dejan de funcionar porque el usuario no ha pasado a través del proceso de registro de Azure MFA y obtenido una [contraseña de aplicación](multi-factor-authentication-whats-next.md#app-passwords). 

El uso de PowerShell es una buena opción cuando necesite habilitar usuarios de forma masiva. Cree un script de PowerShell que recorre en iteración una lista de usuarios y los habilita:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

El script siguiente es un ejemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Habilitar Azure MFA con una directiva de acceso condicional

El _acceso condicional_ es una característica de pago de Azure Active Directory, con muchas opciones de configuración. Estos pasos describen cómo crear una directiva. Para más información, consulte [Acceso condicional en Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Acceso condicional**.
3. Seleccione **Nueva directiva**.
4. En **Asignaciones**, seleccione **Usuarios y grupos**. Use las pestañas **Incluir** y **Excluir** para especificar qué usuarios y grupos se administran con la directiva.
5. En **Asignaciones**, seleccione **Aplicaciones en la nube**. Elija incluir **Todas las aplicaciones en la nube**.
6. En **Controles de acceso**, seleccione **Conceder**. Seleccione **Requerir autenticación multifactor**.
7. Cambie **Habilitar directiva** a **Activar** y, a continuación, seleccione **Guardar**.

Las demás opciones de la directiva de acceso condicional permiten especificar exactamente cuándo debe exigirse la verificación en dos pasos. Por ejemplo, puede crear una directiva como la siguiente: cuando los contratistas intenten acceder a nuestra aplicación de compras desde redes en las que no se confía en dispositivos que no están unidos a un dominio, requerir la verificación en dos pasos. 

## <a name="next-steps"></a>pasos siguientes

- Obtener sugerencias sobre los [procedimientos recomendados para el acceso condicional](../active-directory/active-directory-conditional-access-best-practices.md).

- Administrar la configuración de Azure Multi-Factor Authentication de [los usuarios y sus dispositivos](multi-factor-authentication-manage-users-and-devices.md)
