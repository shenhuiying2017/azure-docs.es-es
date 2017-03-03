---
title: Estados de usuario de Microsoft Azure Multi-Factor Authentication
description: "Obtenga información sobre los estados de usuario en Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Estados de usuario en Azure Multi-Factor Authentication
Las cuentas de usuario de Azure Multi-Factor Authentication tienen los siguientes tres estados:

| Estado | Description | Aplicaciones que no son de explorador afectadas | 
|:---:|:---:|:---:|
| Disabled |Estado predeterminado de un nuevo usuario no inscrito en Azure Multi-Factor Authentication (MFA). |No |
| Enabled |El usuario se ha inscrito en Azure MFA, pero no se ha registrado. Se le pedirá a dicho usuario que se registre la próxima vez que inicie sesión. |No.  Continúa funcionando hasta que se complete el proceso de registro. |
| Aplicado |El usuario se ha inscrito y ha completado el proceso de registro de Azure MFA. |Sí.  Las aplicaciones requieren contraseñas de aplicación. |

## <a name="changing-a-user-state"></a>Cambio del estado del usuario
El estado de un usuario refleja si un administrador lo ha inscrito en Azure MFA, y si ha completado el proceso de registro.

Todos los usuarios comienzan con el estado *Deshabilitado*. Cuando inscribe usuarios en Azure MFA, cambia a *Habilitado*. Cuando los usuarios habilitados inician sesión y completan el proceso de registro, el estado cambia a *Aplicado*.  

### <a name="view-user-states"></a>Visualización de los estados de usuario

Para acceder a la página donde puede ver y administrar los estados de usuario, siga estos pasos:

1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En la parte izquierda, seleccione **Active Directory**.
3. Seleccione el directorio del usuario que desee ver.
   ![Selección del directorio (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Seleccione **Usuarios**.
5. En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth**. 
   ![Selección de Admin. Multi-Factor Auth (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Se abrirá una nueva pestaña que muestra los estados de usuario.
   ![Estado de usuario de Multi-Factor Authentication (captura de pantalla)](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>Cambio del estado de Deshabilitado a Habilitado

1. Use los pasos anteriores para acceder a la página de usuarios de Multi-Factor Authentication. 
2. Busque el usuario que desea habilitar para Azure MFA. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **Deshabilitado**.
   ![Búsqueda de usuario (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Active la casilla situada junto a su nombre.
4. En la derecha, debajo de los pasos rápidos, haga clic en **Habilitar**.
   ![Habilitación del usuario seleccionado (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. Haga clic en **Habilitar Multi-Factor Auth**.
   ![Habilitar Multi-Factor Auth (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. Observe que el estado del usuario ha cambiado de **Deshabilitado** a **Habilitado**.
   ![El usuario ahora está habilitado (captura de pantalla)](./media/multi-factor-authentication-get-started-cloud/user.png)

Después de habilitar los usuarios, debe notificarlos por correo electrónico. Comente que se les pedirá que se registren la próxima vez que inicien sesión, y que puede que algunas aplicaciones que no son de explorador no sean compatibles con la verificación en dos pasos. También puede incluir un vínculo a nuestra [Guía del usuario final de Azure MFA](./end-user/multi-factor-authentication-end-user.md), que les ayudará a empezar a trabajar. 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Para cambiar el estado de habilitado/exigido a deshabilitado

1. Siga los pasos de [Visualización de los estados de usuario](#view-user-states) para acceder a la página de usuarios de Multi-Factor Authentication.
6. Busque el usuario que desee deshabilitar. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **Habilitado** o **Aplicado**.
7. Active la casilla situada junto a su nombre.
8. En la derecha, debajo de los pasos rápidos, haga clic en **Deshabilitar**.
   ![Deshabilitación de usuario (captura de pantalla)](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. Se le pedirá que confirme la acción. Haga clic en **Sí**.
10. Si el usuario se deshabilitó correctamente, recibirá un mensaje de confirmación. Haga clic en **Cerrar**.


