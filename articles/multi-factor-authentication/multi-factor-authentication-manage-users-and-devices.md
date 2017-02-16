---
title: "Administración de usuarios y dispositivos por parte de los administradores (Azure MFA) | Microsoft Docs"
description: "Describe cómo cambiar opciones de la configuración del usuario, como por ejemplo forzar a los usuarios realizar nuevamente el proceso proofup."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 27ff53646992308d574dcc2e631cd63b8227f9c8
ms.openlocfilehash: 826fc2b2eaaf180d922c7a9a4c329ec4379c2ae0


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube
Como administrador, puede gestionar las siguientes opciones de configuración de usuario y de dispositivo.  

* [Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto](#require-selected-users-to-provide-contact-methods-again)
* [Eliminar las contraseñas de aplicación existentes de los usuarios](#delete-users-existing-app-passwords)
* [Restauración de MFA en todos los dispositivos suspendidos para un usuario](#restore-mfa-on-all-suspended-devices-for-a-user)

Esto resulta útil si un equipo o dispositivo se pierde o es objeto de un robo o si tiene que quitar el acceso de un usuario.

## <a name="require-selected-users-to-provide-contact-methods-again"></a>Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto
Esto obliga a un usuario a volver a realizar el proceso de registro cuando inicia sesión. Tenga en cuenta que las aplicaciones sin explorador continuarán funcionando si el usuario tiene las contraseñas de aplicación para ellas.  Puede eliminar las contraseñas de aplicación de los usuarios seleccionando también **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Cómo requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto
1. Inicie sesión en el Portal de Azure clásico.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio para el usuario al que desea requerir que vuelva a proporcionar su método de contacto.
4. En la parte superior, haga clic en Usuarios.
5. En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication.
6. Busque el usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.
7. Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.
8. Marque la opción **Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto**.
   ![Proporcionar métodos de contacto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. Haga clic en Guardar.
10. Haga clic en Cerrar

## <a name="delete-users-existing-app-passwords"></a>Eliminar las contraseñas de aplicación existentes de los usuarios
Con esto se eliminan todas las contraseñas de aplicación que ha creado un usuario. Las aplicaciones sin explorador asociadas con estas contraseñas de aplicación dejarán de funcionar hasta que se cree una nueva contraseña de aplicación.

### <a name="how-to-delete-users-existing-app-passwords"></a>Cómo eliminar las contraseñas de aplicación existentes de los usuarios
1. Inicie sesión en el Portal de Azure clásico.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio del usuario para el que desea eliminar las contraseñas de aplicación.
4. En la parte superior, haga clic en Usuarios.
5. En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication.
6. Busque el usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.
7. Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.
8. Active la casilla **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.
   ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Haga clic en Guardar.
10. Haga clic en Cerrar.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restauración de MFA en todos los dispositivos recordados de un usuario
Los administradores tienen la capacidad de restablecer Multi-Factor Authentication en los dispositivos y exploradores de los usuarios. Al hacerlo, se dejará de recordar MFA en todos los dispositivos y los exploradores del usuario, y el usuario deberá usar MFA al iniciar sesión la próxima vez.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Cómo restaurar MFA en todos los dispositivos suspendidos para un usuario
1. Inicie sesión en el Portal de Azure clásico.
2. En la parte izquierda, haga clic en Active Directory.
3. En Directorio, haga clic en el directorio del usuario para el que desea restaurar MFA.
4. En la parte superior, haga clic en Usuarios.
5. En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication.
6. Busque el usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.
7. Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.
8. Ponga una marca de verificación en **Restaurar autenticación multifactor en todos los dispositivos recordados**
   ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Haga clic en Guardar.
10. Haga clic en Cerrar.



<!--HONumber=Jan17_HO4-->


