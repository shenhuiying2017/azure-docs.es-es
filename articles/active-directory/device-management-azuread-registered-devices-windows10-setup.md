---
title: "Configuración de dispositivos Windows 10 registrados en Azure Active Directory | Microsoft Docs"
description: Aprenda a configurar los dispositivos Windows 10 registrados en Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configuración de dispositivos Windows 10 registrados en Azure Active Directory

La administración de dispositivos en Azure Active Directory (Azure AD) puede ayudar a asegurarse de que los usuarios tienen acceso a los recursos desde dispositivos que cumplen los estándares de seguridad y cumplimiento. Para obtener más información, vea [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md).

Con los [dispositivos registrados de Azure AD](device-management-introduction.md#azure-ad-registered-devices), puede habilitar el escenario **Bring Your Own Device (BYOD)**, que permite a los usuarios acceder a los recursos de su organización con dispositivos personales.  

En Azure AD, puede configurar dispositivos registrados para Azure AD para:

- Windows 10
- iOS
- Android
- macOS.  

En este tema se proporcionan instrucciones sobre cómo registrar dispositivos Windows 10 con Azure AD. 


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, debe comprobar que:

- Tiene los permisos para registrar dispositivos. 

    ![Registro](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- Aún no ha superado aún el número máximo de dispositivos por usuario. 

    ![Registro](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

Para más información, consulte la sección [Configuración del dispositivo](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Qué debería saber

Al registrar un dispositivo, tenga en cuenta lo siguiente:

- Windows registra el dispositivo en el directorio de la organización en Azure AD.

- Es posible que se le pida que pase el desafío de autenticación multifactor. El administrador de TI puede configurar este desafío.

- Azure AD comprueba si el dispositivo requiere una inscripción en la administración de dispositivos móviles. Inscribe el dispositivo, si procede.

- Windows redirige a los usuarios administrados en el escritorio mediante el proceso de inicio de sesión automático.

- Los usuarios federados se redirigen a una página de inicio de sesión de Windows para que introduzcan las credenciales.


## <a name="register-a-device"></a>Registrar un dispositivo

Para registrar su dispositivo Windows 10 con Azure AD, complete los pasos siguientes. Si ha registrado correctamente el dispositivo con Azure AD, la pagina **Obtener acceso a trabajo o escuela** lo indica con la entrada **Cuenta profesional o educativa**.

![Registro](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Para registrar su dispositivo Windows 10:

1. En el menú **Inicio**, haga clic en **Configuración**.

    ![Seleccionar Configuración](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Haga clic en **Cuentas**.

    ![Seleccionar Cuentas](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Haga clic en **Obtener acceso a trabajo o escuela**.

    ![Seleccionar Obtener acceso a trabajo o escuela](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. En la página **Obtener acceso a trabajo o escuela**, haga clic en **Conectar**.

    ![Pagina Obtener acceso a trabajo o escuela](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. En la página **Configurar una cuenta profesional o educativa**, escriba el nombre de la cuenta (por ejemplo: someone@example.com) y después haga clic en **Siguiente**.

    ![Página Configurar una cuenta profesional o educativa](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. En la página **Escribir contraseña**, escríbala y después haga clic en **Siguiente**.

    ![Escribir contraseña](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. En la página **Está todo listo**, haga clic en **Listo**.

    ![Página Está todo listo](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Comprobación

Para comprobar si un dispositivo está unido a Azure AD, consulte la página **Obtener acceso a trabajo o escuela** en el dispositivo.

![Estado de la cuenta profesional o educativa](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

También puede consultar la configuración del dispositivo en el portal de Azure AD.

![Dispositivos registrados en Azure AD](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte: 

- [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)

- [Managing devices using the Azure portal](device-management-azure-portal.md) (Administración de dispositivos con Azure Portal)





