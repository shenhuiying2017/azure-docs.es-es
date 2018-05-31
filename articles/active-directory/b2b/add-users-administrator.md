---
title: Incorporación de usuarios de colaboración B2B en Azure Portal | Microsoft Docs
description: Muestra cómo un administrador puede agregar usuarios invitados a su directorio desde una organización asociada con la colaboración B2B de Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7dddc41d35ae466f7c1392450fbda3c86a72a538
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267236"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Incorporación de usuarios de colaboración B2B de Azure Active Directory en Azure Portal

Como administrador global, o como un usuario al que se le asignan cualquiera de los roles limitados de directorio de administradores, puede usar Azure Portal para invitar a los usuarios de colaboración B2B. Puede invitar a usuarios invitados al directorio, a un grupo o a una aplicación. Después de invitar a un usuario mediante cualquiera de estos métodos, la cuenta del usuario invitado se agrega a Azure Active Directory (Azure AD), con *invitado* como tipo de usuario. El usuario invitado deberá canjear después su invitación para acceder a los recursos.

Después de agregar un usuario invitado al directorio, puede enviarle un vínculo directo a una aplicación compartida o bien, el propio usuario invitado puede hacer clic en la dirección URL de canje del correo electrónico de invitación. Para más información sobre el proceso de canje, consulte [Canje de invitación de colaboración B2B](redemption-experience.md).

> [!IMPORTANT]
> Debe seguir los pasos descritos en [Instrucciones: Agregar información de privacidad de su organización en Azure Active Directory](https://aka.ms/adprivacystatement) para agregar la dirección URL de la declaración de privacidad de su organización. Como parte del proceso de canje de invitación por primera vez, el usuario invitado debe indicar su consentimiento con los términos de privacidad para poder continuar. 

## <a name="add-guest-users-to-the-directory"></a>Adición de usuarios invitados al directorio

Para agregar usuarios de colaboración B2B al directorio, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de Azure AD.
2. En el panel de navegación, seleccione **Azure Active Directory**.
3. En **Administrar**, seleccione **Usuarios y grupos** > **Todos los usuarios**.
4. Seleccione **Nuevo usuario invitado**.

   ![Muestra dónde se encuentra el nuevo usuario invitado en la interfaz de usuario.](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
7. En **Invitar a un invitado**, escriba la dirección de correo electrónico del usuario externo. Opcionalmente, puede incluir un mensaje de bienvenida. Por ejemplo: 

   ![Muestra dónde se encuentra el nuevo usuario invitado en la interfaz de usuario.](./media/add-users-administrator/InviteGuest.png) 

8. Seleccione **Invitar** para enviar automáticamente la invitación al usuario invitado. En el área **Notificación**, busque el mensaje **Usuario invitado correctamente**. 
 
Después de enviar la invitación, la cuenta de usuario se agrega automáticamente al directorio como invitado.


![Muestra el usuario de B2B con el tipo de usuario de invitado.](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adición de usuarios invitados a un grupo
Si necesita agregar manualmente usuarios de colaboración B2B a un grupo como un administrador de Azure AD, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de Azure AD.
2. En el panel de navegación, seleccione **Azure Active Directory**.
3. En **Administrar**, seleccione **Usuarios y grupos** > **Todos los grupos**.
4. Seleccione un grupo (o haga clic en **Nuevo grupo** para crear uno). Es una buena idea incluir en la descripción del grupo que el grupo contiene los usuarios invitados B2B.
5. Seleccione **Miembros** > **Agregar miembros**. 
6. Realice una de las operaciones siguientes:
   - Si el usuario invitado ya existe en el directorio, busque el usuario B2B. Seleccione el usuario > haga clic en **Seleccionar** para agregar el usuario al grupo.
   - Si el usuario invitado ya no existe en el directorio, seleccione **Invitar**.
   ![Adición del botón Invitar para agregar miembros invitados](./media/add-users-administrator/GroupInvite.png)
   
      En **Invitar a un invitado**, escriba la dirección de correo electrónico y un mensaje personal opcional > seleccione **Invitar**. Haga clic en **Seleccionar** para agregar el usuario al grupo.

      La invitación se dirige automáticamente al usuario invitado. En el área **Notificación**, busque el mensaje de **usuario invitado** correctamente. 

También puede utilizar grupos dinámicos con colaboración B2B de Azure AD. Para más información, consulte [Grupos dinámicos y colaboración B2B de Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adición de usuarios invitados a una aplicación

Para agregar usuarios de colaboración B2B a una aplicación como administradores de Azure AD, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de Azure AD.
2. En el panel de navegación, seleccione **Azure Active Directory**.
3. En **Administrar**, seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.
4. Seleccione la aplicación a la que desea agregar usuarios invitados.
5. En **Administrar**, seleccione **Usuarios y grupos**.
6. Seleccione **Agregar usuario**.
7. En **Agregar asignación**, seleccione **Usuarios y grupos**.
8. Realice una de las operaciones siguientes:
   - Si el usuario invitado ya existe en el directorio, busque el usuario B2B. Seleccione el usuario y haga clic en **Seleccionar** para agregar el usuario a la aplicación.
   - Si el usuario invitado ya no existe en el directorio, seleccione **Invitar**.
   ![Adición del botón Invitar para agregar miembros invitados](./media/add-users-administrator/AppInviteUsers.png)
   
      En **Invitar a un invitado**, escriba la dirección de correo electrónico y un mensaje personal opcional > seleccione **Invitar**. Haga clic en **Seleccionar** para agregar el usuario a la aplicación.

      La invitación se dirige automáticamente al usuario invitado. En el área **Notificación**, busque el mensaje de **usuario invitado** correctamente.

9. En **Agregar asignación**, haga clic en **Seleccionar rol** > seleccione un rol que se aplicará al usuario seleccionado (si procede) > seleccione **Aceptar**.
10. Haga clic en **Asignar**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenvío de invitaciones a usuarios invitados

Si un usuario invitado todavía no ha canjeado su invitación, puede volver a enviarle el correo electrónico de invitación.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador de Azure AD.
2. En el panel de navegación, seleccione **Azure Active Directory**.
3. En **Administrar**, seleccione **Usuarios y grupos**.
4. Seleccione **Todos los usuarios**.
5. Seleccione la cuenta del usuario.
6. En **Administrar**, seleccione **Perfil**.
7. Si el usuario todavía no ha aceptado la invitación, la opción **Volver a enviar la invitación** está disponible. Seleccione este botón para volver a enviarla.

   ![Opción Volver a enviar la invitación en el perfil de usuario](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Si vuelve a enviar una invitación que originalmente dirigía al usuario a una aplicación específica, debe entender que el vínculo de la nueva invitación lleva por el contrario al usuario al panel de acceso de nivel superior.

## <a name="next-steps"></a>Pasos siguientes

- Para aprender cómo los administradores que no son de Azure AD pueden agregar usuarios invitados B2B, consulte [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B a Azure Active Directory?](add-users-information-worker.md)
- Para más información sobre el correo electrónico de invitación, consulte [Los elementos del correo electrónico de invitación de colaboración B2B](invitation-email-elements.md).

