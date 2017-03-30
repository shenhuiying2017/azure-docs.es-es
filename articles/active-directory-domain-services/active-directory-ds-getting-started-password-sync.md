---
title: "Active Directory Domain Services: habilitación de la sincronización de contraseñas | Microsoft Docs"
description: "Introducción a los Servicios de dominio de Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f994edd71c6d8ca8a2c3f5de370115747f87da57
ms.lasthandoff: 03/28/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Servicios de dominio de Azure AD (versión preliminar): habilitación de la sincronización de contraseñas con Azure Active Directory Domain Services
En las tareas anteriores, ha habilitado Azure AD Domain Services para su inquilino de Azure AD. La siguiente tarea consiste en habilitar los hashes de credenciales necesarios para que la autenticación NTLM y Kerberos se sincronice en Azure AD Domain Services. Una vez configurada la sincronización de credenciales, los usuarios pueden iniciar sesión en el dominio administrado mediante sus credenciales corporativas.

Los pasos que se deben seguir son distintos en función de si su organización tiene un inquilino de Azure AD solo de nube o está configurado para sincronizarse con su directorio local mediante Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Inquilino de Azure AD solo de nube](active-directory-ds-getting-started-password-sync.md)
> * [Inquilino de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarea 5: Habilitación de la sincronización de contraseñas con Azure AD Domain Services para un inquilino de Azure AD solo de nube
Azure AD Domain Services necesita hashes de credenciales en un formato adecuado para la autenticación de NTLM y Kerberos para poder autenticar a los usuarios en el dominio administrado. A menos que habilite AAD Domain Services para el inquilino, Azure AD no genera ni almacena hashes de credenciales en el formato necesario para la autenticación NTLM o Kerberos. Por motivos de seguridad obvios, Azure AD tampoco almacena las credenciales en forma de texto sin cifrar. Por lo tanto, Azure AD no tiene forma de generar estos hashes de credenciales de NTLM o Kerberos basados en las credenciales de los usuarios existentes.

> [!NOTE]
> Si su organización tiene un inquilino de Azure AD solo de nube, los usuarios que tengan que usar Azure AD Domain Services deberán cambiar sus contraseñas.
>
>

Este proceso de cambio de contraseña hace que los valores de hash de credenciales que necesitan los Servicios de dominio de Azure AD para la autenticación Kerberos y NTLM se generen en Azure AD. Puede caducar las contraseñas de todos los usuarios en el inquilino que tiene que usar los Servicios de dominio de Azure AD o indicar a estos usuarios que cambien sus contraseñas.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Habilitación de la generación de hash de credenciales de NTLM y Kerberos en el inquilino de Azure AD solo de nube
Estas son las instrucciones que tiene que proporcionar a los usuarios finales para que puedan cambiar sus contraseñas:

1. Navegue a la página del panel de acceso de Azure AD de su organización en [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Seleccione la pestaña **Perfil** en esta página.
3. Haga clic en el icono **Cambiar contraseña** de esta página.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Si no ve la opción **Cambiar contraseña** en la página Panel de acceso, asegúrese de que su organización ha configurado la [administración de contraseñas en Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. En la página **Cambiar contraseña** , escriba la contraseña existente (anterior) y luego escriba una nueva contraseña y confírmela. Haga clic en **Enviar**.

    ![Creación de una red virtual para los Servicios de dominio de Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Después de haber cambiado su contraseña, la nueva contraseña se podrá usar en breve en Azure AD Domain Services. Después de unos minutos (por lo general, unos 20 minutos), los usuarios pueden iniciar sesión en equipos unidos al dominio administrado con su contraseña recién cambiada.

<br>

## <a name="related-content"></a>Contenido relacionado
* [Actualización de la propia contraseña](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [Introducción a la administración de contraseñas en Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Habilitación de la sincronización de contraseñas con Azure AD Domain Services para un inquilino de Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Join a Windows virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual con Windows a un dominio administrado de Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-join-windows-vm.md)
* [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain (Unión de una máquina virtual con Red Hat Enterprise Linux a un dominio administrado de Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

