---
title: "¿Cómo se utilizan las contraseñas de aplicación en Azure MFA? | Microsoft Docs"
description: "Esta página ayudará a los usuarios a entender qué son las contraseñas de aplicación y para qué se utilizan con respecto a Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 0812719ddee8c0ff0c2fa9256c2819611692dfe5
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>¿Qué son las contraseñas de aplicación en Azure Multi-Factor Authentication?
Determinadas aplicaciones sin explorador, como el cliente de correo electrónico nativo de Apple que usa Exchange Active Sync, actualmente no admiten la autenticación multifactor. La autenticación multifactor se habilita por usuario.  Esto significa que un usuario no puede usar la autenticación multifactor si:

- El usuario se ha habilitado para la autenticación multifactor.
- El usuario está intentando usar una aplicación sin explorador.

Una contraseña de aplicación permite al usuario usar la aplicación.

Una vez que tenga una contraseña de aplicación, utilícela en lugar de la contraseña original para estas aplicaciones sin explorador. Cuando se registra para la verificación en dos pasos, le está indicando a Microsoft que no permita que ningún usuario inicie sesión con su contraseña, si no pueden realizar también la segunda comprobación. El cliente de correo electrónico nativo de Apple en el teléfono no puede iniciar sesión en su lugar ya que no puede solicitar la verificación en dos pasos. La solución a este problema consiste en crear una contraseña de aplicación más segura que no use diariamente. Las contraseñas de aplicación solo son para las aplicaciones que no admiten la verificación en dos pasos. Utilice la contraseña de aplicación para que las aplicaciones puedan omitir la autenticación multifactor y poder seguir trabajando.


> [!NOTE]
> Los clientes de Office 2013 (incluido Outlook) admiten nuevos protocolos de autenticación que se pueden usar con la verificación en dos pasos. Las contraseñas de aplicación no son necesarias para los clientes de Office 2013.  Para más información, consulte [Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Cómo utilizar las contraseñas de aplicación
Aquí se mencionan algunos aspectos importantes acerca de las contraseñas de aplicación:

* El usuario no crea sus propias contraseñas de aplicación. Se generan automáticamente.
* Actualmente hay un límite de 40 contraseñas por usuario.
* Si intenta crear una contraseña de aplicación una vez que haya alcanzado el límite, tendrá que eliminar una de las existentes para poder crear una nueva.
* Utilice una contraseña de aplicación por dispositivo, no por aplicación. Por ejemplo, puede crear una contraseña de aplicación para su equipo portátil y utilizar esa contraseña de aplicación para todas las aplicaciones de ese equipo portátil. A continuación, cree una segunda contraseña de aplicación que se usará para todas las aplicaciones del escritorio.
* La primera vez que se registre para la verificación en dos pasos, se le proporcionará una contraseña de aplicación.  Si necesita otras adicionales, puede crearlas.



## <a name="creating-and-deleting-app-passwords"></a>Creación y eliminación de contraseñas de aplicación
Durante el inicio de sesión inicial, se le ofrece una contraseña de aplicación que puede usar.  También puede crear y eliminar las contraseñas de aplicación más adelante. La forma de eliminar las contraseñas depende de cómo use la autenticación multifactor. Responda a las siguientes preguntas para determinar dónde debe ir para administrar las contraseñas de aplicación:

1. ¿Utiliza la verificación en dos pasos para su cuenta Microsoft personal? Si es así, consulte el artículo [Contraseñas de aplicación y verificación en dos pasos](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) para obtener ayuda. En caso contrario, continúe con la pregunta dos.

2. En ese caso, significa que usa la verificación en dos pasos para la cuenta profesional o educativa. ¿La usa para iniciar sesión en aplicaciones de Office 365? Si es así, consulte [Crear una contraseña de aplicación para Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) para obtener ayuda. En caso contrario, pase a la pregunta tres.

3. ¿Utiliza la verificación en dos pasos con Microsoft Azure? En caso afirmativo, continúe en la sección [Administración de contraseñas de aplicación en Azure Portal](#manage-app-passwords-in-the-Azure-portal) de este artículo. En caso contrario, continúe con la pregunta cuatro.

4. ¿No está seguro de donde utiliza la verificación en dos pasos? Continúe en la sección [Administración de contraseñas de aplicación con el portal de MyApps](#manage-app-passwords-with-the-myapps-portal) de este artículo.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Administración de contraseñas de aplicación en Azure Portal
Si utiliza la verificación en dos pasos con Azure, le interesará crear contraseñas de aplicación a través de Azure Portal.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Creación de contraseñas de aplicación en el Portal de Azure
1. Inicie sesión en el Portal de Azure.
2. En la parte superior, haga clic en el nombre de usuario y seleccione **Cambiar contraseña**.
3. En la página de proofup, en la parte superior, seleccione las **Contraseñas de aplicación**.
4. Seleccione **Crear**.
5. Escriba un nombre para la contraseña de aplicación y seleccione **Siguiente**.
6. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación.

   ![Nube](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


## <a name="manage-app-passwords-with-the-myapps-portal"></a>Administración de contraseñas de aplicación con el portal de MyApps.
Si no está seguro de cómo usar la autenticación multifactor, siempre puede cambiar la configuración mediante el portal de Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Creación de una contraseña de aplicación mediante el portal de MyApps
1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Haga clic en su nombre en la parte superior derecha y elija **Perfil**.
3. Seleccione **Comprobación de seguridad adicional**.
   ![Seleccionar comprobación de seguridad adicional: captura de pantalla](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Seleccione **contraseñas de aplicación**.
   ![Seleccionar contraseñas de aplicación: captura de pantalla](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Haga clic en **Create**(Crear).
6. Escriba un nombre para la contraseña de aplicación y haga clic en **Siguiente**.
7. Copie la contraseña de aplicación en el Portapapeles y péguela en la aplicación.
   ![Crear una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Eliminación de una contraseña de aplicación mediante el portal de MyApps
1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En la parte superior, seleccione el perfil.
3. Seleccione **Comprobación de seguridad adicional**.

   ![Seleccionar comprobación de seguridad adicional: captura de pantalla](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Seleccione **contraseñas de aplicación**.

   ![Seleccionar contraseñas de aplicación: captura de pantalla](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Junto a la contraseña de la aplicación que desea quitar, seleccione **Eliminar**.

   ![Eliminar una contraseña de aplicación](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Confirme que desea eliminar esa contraseña haciendo clic en **Sí**.
7. Una vez que la contraseña se elimina, puede hacer clic en **cerrar**.

## <a name="next-steps"></a>pasos siguientes

- [Administrar la configuración de la verificación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)

- Pruebe la [aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para verificar los inicios de sesión con las notificaciones de aplicación, en lugar de recibir llamadas o textos.
