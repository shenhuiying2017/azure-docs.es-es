---
title: "Administración de usuarios y dispositivos por parte de los administradores (Azure MFA) | Microsoft Docs"
description: "Describe cómo cambiar opciones de la configuración del usuario, como por ejemplo forzar a los usuarios realizar nuevamente el proceso proofup."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: e9b8504d4a59cf0fae69a4e975d6f834028066d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Administrar la configuración de usuario con Azure Multi-Factor Authentication en la nube
Como administrador, puede administrar las siguientes opciones de configuración de usuario y de dispositivo:

* Requerir a los usuarios que vuelvan a proporcionar métodos de contacto
* Eliminar contraseñas de aplicación
* Requerir MFA en todos los dispositivos de confianza 

## <a name="require-users-to-provide-contact-methods-again"></a>Requerir a los usuarios que vuelvan a proporcionar métodos de contacto
Esta configuración obliga al usuario a volver a realizar el proceso de registro. Las aplicaciones sin explorador continúan funcionando si el usuario tiene las contraseñas de aplicación para ellas.  Puede eliminar las contraseñas de aplicación de los usuarios seleccionando también **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Cómo requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**. Se abre la página de Multi-Factor Authentication. 
4. Active la casilla junto al usuario o usuarios que desea administrar. Aparecerá una lista de opciones de paso rápido a la derecha. 
5. Seleccione **Administrar configuración de usuario**.
6. Active la casilla **Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto**.
   ![Proporcionar métodos de contacto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
7. Haga clic en **guardar**.
8. Haga clic en **Cerrar**.

## <a name="delete-users-existing-app-passwords"></a>Eliminar las contraseñas de aplicación existentes de los usuarios
Esta configuración elimina todas las contraseñas de aplicación que ha creado un usuario. Las aplicaciones sin explorador asociadas con estas contraseñas de aplicación dejan de funcionar hasta que se cree una nueva contraseña de aplicación.

### <a name="how-to-delete-users-existing-app-passwords"></a>Cómo eliminar las contraseñas de aplicación existentes de los usuarios
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**. Se abre la página de Multi-Factor Authentication. 
6. Active la casilla junto al usuario o usuarios que desea administrar. Aparecerá una lista de opciones de paso rápido a la derecha. 
7. Seleccione **Administrar configuración de usuario**.
8. Active la casilla **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.
   ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Haga clic en **guardar**.
10. Haga clic en **Cerrar**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restauración de MFA en todos los dispositivos recordados de un usuario
Una de las características configurables de Azure Multi-Factor Authentication es dar la opción a los usuarios de marcar los dispositivos como de confianza. Para más información, vea [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Los usuarios pueden optar por no usar la verificación en dos pasos durante un número de días configurable en sus dispositivos habituales. Si se compromete la seguridad de la cuenta o se pierde un dispositivo de confianza, necesita tener la posibilidad de quitar el estado de confianza y volver a solicitar la verificación en dos pasos.

La opción **Restaurar autenticación multifactor en todos los dispositivos recordados** supone que el usuario tendrá que realizar la verificación en dos pasos la próxima vez que inicie sesión, con independencia de que decida o no marcar su dispositivo como de confianza. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Cómo restaurar MFA en todos los dispositivos suspendidos para un usuario
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la izquierda, seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**. Se abre la página de Multi-Factor Authentication. 
6. Active la casilla junto al usuario o usuarios que desea administrar. Aparecerá una lista de opciones de paso rápido a la derecha. 
7. Seleccione **Administrar configuración de usuario**.
8. Active la casilla **Restaurar autenticación multifactor en todos los dispositivos recordados**
   ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Haga clic en **guardar**.
10. Haga clic en **Cerrar**.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

- Si los usuarios necesitan ayuda, diríjalos a la [Guía de usuario para la verificación en dos pasos](./end-user/multi-factor-authentication-end-user.md)
