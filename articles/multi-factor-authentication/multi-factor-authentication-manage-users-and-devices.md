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
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube
Como administrador, puede administrar las siguientes opciones de configuración de usuario y de dispositivo:

* Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto
* Eliminar las contraseñas de aplicación existentes de los usuarios
* Restauración de MFA en todos los dispositivos suspendidos para un usuario

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
Una de las características configurables de Azure Multi-Factor Authentication es dar la opción a los usuarios de marcar los dispositivos como de confianza. Para más información, vea [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Los usuarios pueden optar por no utilizar la verificación en dos pasos durante un número de días configurable en sus dispositivos habituales. Si se compromete la seguridad de la cuenta o se pierde un dispositivo de confianza, necesita tener la posibilidad de quitar el estado de confianza y volver a solicitar la verificación en dos pasos.

La opción **Restaurar autenticación multifactor en todos los dispositivos recordados** supone que el usuario tendrá que realizar la verificación en dos pasos la próxima vez que inicie sesión, con independencia de que decida o no marcar su dispositivo como de confianza. 

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

