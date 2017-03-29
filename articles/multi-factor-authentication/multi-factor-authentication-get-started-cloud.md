---
title: "Introducción a Azure MFA en la nube | Microsoft Docs"
description: "En esta página de Microsoft Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA en la nube."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 79c6ef627d20be52b6067d1f6dfea2a53505c590
ms.lasthandoff: 03/18/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introducción a Azure Multi-Factor Authentication en la nube
En este artículo aprenderá a usar Azure Multi-Factor Authentication en la nube.

> [!NOTE]
> La siguiente documentación proporciona información sobre cómo habilitar usuarios mediante el **Portal de Azure clásico**. Si busca información sobre cómo configurar Azure Multi-Factor Authentication para usuarios de Office 365, consulte [Configurar la autenticación multifactor para usuarios de Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA en la nube](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Requisito previo
[Registrarse para obtener una suscripción a Azure](https://azure.microsoft.com/pricing/free-trial/) : si todavía no dispone de una suscripción de Azure, es necesario que se registre para obtener una. Si simplemente está comenzando a utilizar Azure MFA, puede usar una suscripción de prueba.

## <a name="enable-azure-multi-factor-authentication"></a>Habilitación de Azure Multi-Factor Authentication
Siempre y cuando los usuarios tengan licencias que incluyen Azure Multi-Factor Authentication, no hay nada que se pueda hacer para activar Azure MFA. Puede iniciar la solicitud de la verificación en dos pasos en cada usuario individual. Las licencias que habilitan Azure MFA son:
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Si no dispone una de estas tres licencias o no tiene suficientes licencias para cubrir a todos los usuarios, es correcto también. Solo tiene que realizar un paso adicional y [crear un proveedor de Muti-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) en el directorio.

## <a name="turn-on-two-step-verification-for-users"></a>Activación de la verificación en dos pasos para los usuarios
Para solicitar la verificación en dos pasos de un usuario, cambie el estado del usuario de deshabilitado a habilitado.  Para más información sobre los estados de usuario, consulte [Estados de usuario en Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Utilice el procedimiento siguiente para habilitar MFA para los usuarios.

### <a name="to-turn-on-multi-factor-authentication"></a>Activación de Azure Multi-Factor Authentication
1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, haga clic en **Active Directory**.
3. En Directorio, seleccione el directorio para el usuario que desea habilitar.
   ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. En la parte superior, haga clic en **Usuarios**.
5. En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth**. Se abre una nueva pestaña del explorador.
   ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Busque el usuario que desea habilitar para la verificación en dos pasos. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado**.
   ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. Coloque una **marca de comprobación** en el cuadro junto a su nombre.
8. A la derecha, haga clic en **Habilitar**.
   ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Haga clic en **Habilitar Multi-Factor Auth**.
   ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Observe que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**.
    ![Habilitar usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)

Una vez habilitados los usuarios, envíeles una notificación por correo electrónico. La próxima vez que intenten iniciar sesión, se les pedirá que inscriban su cuenta para la verificación en dos pasos. Una vez que comiencen a utilizar la verificación en dos pasos, también deberán configurar las contraseñas de aplicación para evitar que se bloqueen aplicaciones que no son de explorador.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Use PowerShell para automatizar la activación de la verificación en dos pasos
Para cambiar el [estado](multi-factor-authentication-whats-next.md) con [Azure AD PowerShell](/powershell/azureps-cmdlets-docs), puede utilizar lo siguiente.  Puede cambiar `$st.State` para que sea igual a uno de los estados siguientes:

* habilitado
* Aplicado
* Disabled  

> [!IMPORTANT]
> No es aconsejable mover usuarios directamente desde el estado deshabilitado al estado forzado. Las aplicaciones que no son de explorador dejarán de funcionar porque el usuario no ha pasado a través del proceso de registro de MFA y obtenido una [contraseña de aplicación](multi-factor-authentication-whats-next.md#app-passwords). Si tiene aplicaciones que no son de explorador y requiere contraseñas de aplicación, se recomienda que pase del estado deshabilitado al habilitado. Esto permitirá a los usuarios registrar y obtener sus contraseñas de aplicación. Después, podrá pasar al estado forzado.

El uso de PowerShell sería una opción para habilitar usuarios de forma masiva. Actualmente no hay ninguna característica de habilitación de forma masiva en el Portal de Azure y debe seleccionar cada usuario individualmente. Si tiene muchos usuarios, esto puede suponer bastante trabajo. Con la creación de un script de PowerShell mediante las opciones que aparecen a continuación, puede recorrer en iteración una lista de usuarios y habilitarlos.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Aquí tiene un ejemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para más información, consulte [Estados de usuario en Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha configurado Azure Multi-Factor Authentication en la nube, puede configurar la implementación. Consulte [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) para conocer más detalles.


