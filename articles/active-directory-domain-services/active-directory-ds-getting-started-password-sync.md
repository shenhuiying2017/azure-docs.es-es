---
title: 'Azure Active Directory Domain Services: habilitación de la sincronización de los valores hash de las contraseñas | Microsoft Docs'
description: Introducción a Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/02/2018
ms.author: maheshu
ms.openlocfilehash: 4122052e48e57a27492433d01ee93536a9bbcf35
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Habilitación de la sincronización de los valores hash de las contraseñas con Azure Active Directory Domain Services
En las tareas anteriores, habilitó Azure Active Directory Domain Services para su inquilino de Azure Active Directory (Azure AD). La siguiente tarea consiste en habilitar la sincronización de los valores hash de las contraseñas necesarios para la autenticación NT LAN Manager (NTLM) y Kerberos en Azure AD Domain Services. Una vez configurada la sincronización de los valores hash de las contraseñas, los usuarios pueden iniciar sesión en el dominio administrado mediante sus credenciales corporativas.

Los pasos necesarios son diferentes según se trate de cuentas de usuario solo de nube o de las cuentas de usuarios que se sincronizan desde el directorio local mediante Azure AD Connect. 

<br>
| **Tipo de cuenta de usuario.** | **Pasos que seguir** |
| --- |---|
| **Cuentas de usuario en la nube creadas en Azure AD** |**&#x2713;** [Siga las instrucciones de este artículo](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Cuentas de usuario sincronizadas desde un directorio local** |**&#x2713;**[Sincronización de los valores hash de las contraseñas de las cuentas de usuario sincronizadas desde la instancia local de AD con el dominio administrado](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Puede que tenga que seguir ambos conjuntos de pasos.**
> Si el inquilino de Azure AD tiene una combinación entre usuarios solo de nube y usuarios de la instalación local de AD, debe realizar ambos procedimientos.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Tarea 5: habilitar la sincronización de los valores hash de las contraseñas con el dominio administrado para las cuentas de usuario solo de nube
Azure Active Directory Domain Services necesita los valores hash de las contraseñas en un formato adecuado para la autenticación de NTLM y Kerberos para poder autenticar a los usuarios en el dominio administrado. A menos que habilite Azure Active Directory Domain Services para el inquilino, Azure AD no genera ni almacena los valores hash de las contraseñas en el formato necesario para la autenticación NTLM o Kerberos. Por motivos de seguridad obvios, Azure AD tampoco almacena las credenciales de contraseñas en forma de texto sin cifrar. Por consiguiente, Azure AD no tiene forma de generar automáticamente estos los valores hash de las contraseñas de NTLM o Kerberos basados en las credenciales de los usuarios existentes.

> [!NOTE]
> **Si su organización tiene una cuenta de usuario solo de nube, todos los usuarios que tengan que usar Azure Active Directory Domain Services deben cambiar sus contraseñas.** Una cuenta de usuario solo de nube es una cuenta creada en su directorio de Azure AD mediante Azure Portal, o bien mediante cmdlets de PowerShell de Azure AD. Estas cuentas de usuario no se sincronizan desde un directorio local.
>
>

Este proceso de cambio de contraseña hace que los valores hash de las contraseñas que necesita Azure Active Directory Domain Services para la autenticación Kerberos y NTLM se generen en Azure AD. Puede caducar las contraseñas de todos los usuarios en el inquilino que tiene que usar Azure Active Directory Domain Services o indicar a estos usuarios que cambien sus contraseñas.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Habilitación de la generación de hash de los valores hash de las contraseñas de NTLM y Kerberos para una cuenta de usuario solo de nube
Estas son las instrucciones que tiene que proporcionar a los usuarios finales para que puedan cambiar sus contraseñas:

1. Vaya a la página [Panel de acceso de Azure AD](http://myapps.microsoft.com) para su organización.

    ![Inicio del panel de acceso de Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. En la esquina superior derecha, haga clic en su nombre y seleccione **Perfil** en el menú.

    ![Seleccionar perfil](./media/active-directory-domain-services-getting-started/select-profile.png)

3. En la página **Perfil**, haga clic en **Cambiar contraseña**.

    ![Hacer clic en "Cambiar contraseña"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Si no aparece la opción **Cambiar contraseña** en la ventana Panel de acceso, asegúrese de que su organización ha configurado la [administración de contraseñas en Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. En la página **cambiar contraseña**, escriba la contraseña existente (anterior) y luego escriba una nueva contraseña y confírmela.

    ![El usuario cambia la contraseña](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Haga clic en **Enviar**.

Unos minutos después de haber cambiado su contraseña, la nueva contraseña se podrá usar en Azure Active Directory Domain Services. Después de unos veinte minutos, puede iniciar sesión en equipos unidos al dominio administrado con su contraseña recién cambiada.

## <a name="related-content"></a>Contenido relacionado
* [Actualización de la propia contraseña](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Introducción a la administración de contraseñas en Azure AD](../active-directory/authentication/quickstart-sspr.md)
* [Habilitación de la sincronización de los valores hash de las contraseñas con Azure Active Directory Domain Services para un inquilino de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administración de un dominio administrado con Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Join a Windows virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual Windows a un dominio administrado de Azure Active Directory Domain Services)](active-directory-ds-admin-guide-join-windows-vm.md)
* [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual con Red Hat Enterprise Linux a un dominio administrado de Azure Active Directory Domain Services)](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
