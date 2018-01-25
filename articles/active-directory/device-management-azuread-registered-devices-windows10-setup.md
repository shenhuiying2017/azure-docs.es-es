---
title: "Configuración de dispositivos registrados en Azure Active Directory | Microsoft Docs"
description: Aprenda a configurar dispositivos registrados en Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2560e51d61506389e84288bf983b0ebcb5776ff2
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configuración de dispositivos Windows 10 registrados en Azure Active Directory

Con la administración de dispositivos en Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen acceso a los recursos desde dispositivos que cumplen los estándares de seguridad y cumplimiento. Para más información, vea [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md).

Si quiere habilitar el escenario **Bring Your Own Device (BYOD)**, configure dispositivos registrados en Azure AD. Puede configurar dispositivos registrados en Azure AD para Windows 10, iOS, Android y macOS. En este tema, se indican los pasos relacionados para dispositivos Windows 10. 


## <a name="before-you-begin"></a>Antes de empezar

Para poder registrar un dispositivo Windows 10, debe configurarse el servicio de registro de dispositivos de modo que pueda registrar dispositivos. Además de tener permiso para registrar dispositivos en su inquilino de Azure AD, debe tener menos dispositivos registrados que el máximo configurado. Para obtener más información, vea [Configure device settings](device-management-azure-portal.md#configure-device-settings) (Configuración del dispositivo).

## <a name="what-you-should-know"></a>Qué debería saber

Al registrar un dispositivo, debería tener en cuenta lo siguiente:

- Windows registra el dispositivo en el directorio de la organización en Azure AD

- Es posible que se le pida que pase el desafío de autenticación multifactor. Este desafío puede configurarlo el administrador de TI.

- Azure AD comprueba si el dispositivo requiere la inscripción de administración de dispositivos móviles y lo inscribe si es necesario.

- Si es un usuario administrado, Windows le llevará al escritorio mediante el inicio de sesión automático.

- Si es un usuario federado, verá la pantalla de inicio de sesión de Windows y deberá escribir sus credenciales.


## <a name="registering-a-device"></a>Registro de un dispositivo

En esta sección, se proporcionan los pasos para registrar su dispositivo Windows 10 en Azure AD. Si ha registrado correctamente el dispositivo en Azure AD, el cuadro de diálogo **Obtener acceso a trabajo o escuela** lo indica con la entrada **Cuenta profesional o educativa**.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Para registrar su dispositivo Windows 10:**

1. En el menú **Inicio**, haga clic en **Configuración**.

    ![Configuración](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Haga clic en **Cuentas**.

    ![Cuentas](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Haga clic en **Obtener acceso a trabajo o escuela**.

    ![Obtener acceso a trabajo o escuela](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. En el cuadro de diálogo **Obtener acceso a trabajo o escuela**, haga clic en **Conectar**.

    ![Conectar](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. En el cuadro de diálogo **Configurar una cuenta profesional o educativa**, escriba el nombre de la cuenta (p. ej.: someone@example.com) y después haga clic en **Siguiente**.

    ![Conectar](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. En el cuadro de diálogo **Escribir contraseña**, escriba la contraseña y después haga clic en **Siguiente**.

    ![Conectar](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. En el cuadro de diálogo **Está todo listo**, haga clic en **Listo**.

    ![Conectar](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Comprobación

Para comprobar si un dispositivo está unido a Azure AD, puede consultar el cuadro de diálogo **Obtener acceso a trabajo o escuela** en el dispositivo.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Como alternativa, también puede consultar la configuración del dispositivo en el portal de Azure AD.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>pasos siguientes

- Para obtener más información, vea [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md).

- Para obtener más información sobre cómo administrar dispositivos en el portal de Azure AD, consulte [Managing devices using the Azure portal](device-management-azure-portal.md) (Administración de dispositivos con Azure Portal).




