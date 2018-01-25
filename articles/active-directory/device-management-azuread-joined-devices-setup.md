---
title: "Configuración de dispositivos unidos a Azure Active Directory | Microsoft Docs"
description: Aprenda a configurar dispositivos unidos a Azure Active Directory.
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
ms.openlocfilehash: ac6c9224925e5bfd3cb056c6c8d9cf2a96b0eb2b
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configuración de dispositivos unidos a Azure Active Directory

Con la administración de dispositivos en Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen acceso a los recursos desde dispositivos que cumplen los estándares de seguridad y cumplimiento. Para obtener más información, vea [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md).

Si quiere tener dispositivos Windows 10 de trabajo bajo el control de Azure AD, configure dispositivos unidos a Azure AD. En este tema, se indican los pasos relacionados. 


## <a name="prerequisites"></a>requisitos previos

Para unir un dispositivo Windows 10, el servicio de registro de dispositivos debe configurarse para que pueda registrar dispositivos. Además de tener permiso para unir dispositivos en su inquilino de Azure AD, debe tener menos dispositivos registrados que el máximo configurado. Para obtener más información, consulte [Configure device settings](device-management-azure-portal.md#configure-device-settings) (Configuración del dispositivo).



## <a name="what-you-should-know"></a>Qué debería saber


- Windows une el dispositivo al directorio de la organización en Azure AD.

- Es posible que se le pida que pase el desafío de autenticación multifactor. Este desafío puede configurarlo el administrador de TI.

- Azure AD comprueba si el dispositivo requiere la inscripción de administración de dispositivos móviles y lo inscribe si es necesario.

- Si es un usuario administrado, Windows le llevará al escritorio mediante el inicio de sesión automático.

- Si es un usuario federado, tendrá que iniciar sesión con sus credenciales.


## <a name="joining-a-device"></a>Unión de un dispositivo

En esta sección, se proporcionan los pasos para unir su dispositivo Windows 10 a Azure AD. Si ha unido correctamente el dispositivo a Azure AD, el cuadro de diálogo **Obtener acceso a trabajo o escuela** lo indica con la entrada **Conectado con \<su Azure AD\>**.

![Conectado](./media/device-management-azuread-joined-devices-setup/13.png)


**Para unir su dispositivo Windows 10:**

1. En el menú **Inicio**, haga clic en **Configuración**.

    ![Configuración](./media/device-management-azuread-joined-devices-setup/01.png)

2. Haga clic en **Cuentas**.

    ![Cuentas](./media/device-management-azuread-joined-devices-setup/02.png)


3. Haga clic en **Obtener acceso a trabajo o escuela**.

    ![Obtener acceso a trabajo o escuela](./media/device-management-azuread-joined-devices-setup/03.png)

4. En el cuadro de diálogo **Obtener acceso a trabajo o escuela**, haga clic en **Conectar**.

    ![Conectar](./media/device-management-azuread-joined-devices-setup/04.png)


5. En el cuadro de diálogo **Configurar una cuenta profesional o educativa**, haga clic en **Unir este dispositivo a Azure Active Directory**.

    ![Conectar](./media/device-management-azuread-joined-devices-setup/08.png)


6. En el cuadro de diálogo **Vamos a iniciar su sesión**, escriba el nombre de la cuenta (por ejemplo, someone@example.com) y después haga clic en **Siguiente**.

    ![Vamos a iniciar su sesión](./media/device-management-azuread-joined-devices-setup/10.png)


6. En el cuadro de diálogo **Escribir contraseña**, escriba la contraseña y después haga clic en **Iniciar sesión**.

    ![Escribir contraseña](./media/device-management-azuread-joined-devices-setup/05.png)


7. En el cuadro de diálogo **Asegúrate de que esta es tu organización**, haga clic en **Unir**.

    ![Asegúrate de que esta es tu organización](./media/device-management-azuread-joined-devices-setup/11.png)


8. En el cuadro de diálogo **Está todo listo**, haga clic en **Listo**.

    ![Está todo listo](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Comprobación

Para comprobar si un dispositivo está unido a Azure AD, puede consultar el cuadro de diálogo **Obtener acceso a trabajo o escuela** en el dispositivo.

![Conectado](./media/device-management-azuread-joined-devices-setup/13.png)

Como alternativa, puede ejecutar el comando siguiente: `dsregcmd /status`  
En un dispositivo que se ha unido correctamente, **AzureAdJoined** es **Yes**.

![Conectado](./media/device-management-azuread-joined-devices-setup/14.png)

También puede consultar la configuración del dispositivo en el portal de Azure AD.

![Conectado](./media/device-management-azuread-joined-devices-setup/15.png)

Para obtener más información, consulte [Locate devices](device-management-azure-portal.md#locate-devices) (Buscar dispositivos).


## <a name="next-steps"></a>pasos siguientes

Para obtener más información, consulte  

- [Introducción a la administración de dispositivos en Azure Active Directory](device-management-introduction.md)
- [Managing devices using the Azure portal](device-management-azure-portal.md) (Administración de dispositivos con Azure Portal)
- 



