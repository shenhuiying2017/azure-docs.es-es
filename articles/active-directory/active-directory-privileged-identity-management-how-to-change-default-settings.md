---
title: "Administración de la configuración de activación del rol | Microsoft Docs"
description: "Obtenga información sobre cómo cambiar la configuración predeterminada de identidades con privilegios con la extensión de Privileged Identity Management de Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: f6cbcb6a-8a89-4077-afd8-06c94a64f4aa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2cf361dc879915c316e7ee907b5c70b5907459c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Administración de la configuración de activación de rol en Privileged Identity Management de Azure AD
Un administrador de roles con privilegios puede personalizar Privileged Identity Management (PIM) de Azure AD en su organización, como cambiar la experiencia de un usuario que va a activar una asignación de rol apto.

## <a name="manage-the-role-activation-settings"></a>Administración de la configuración de activación del rol
1. Vaya al [Portal de Azure](https://portal.azure.com) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione **Administrar roles con privilegios** > **Configuración** > **Roles con privilegios**.
3. Seleccione el rol cuya configuración desea administrar.

En la página de configuración de cada rol, hay una serie de valores que puede configurar. Esta configuración solo afecta a los usuarios que son administradores aptos, no a los administradores permanentes.

**Activaciones**: el tiempo, en horas, que un rol permanece activo antes de que caduque. Este valor puede estar comprendido entre 1 y 72 horas.

**Notificaciones**: puede elegir si el sistema enviará o no correos electrónicos a los administradores para confirmar que han activado un rol. Puede ser útil para detectar activaciones no autorizadas o ilegales.

**Vale de incidente o solicitud**: puede elegir que los administradores incluyan o no un número de vale cuando activen su rol. Puede ser útil al realizar auditorías de acceso a rol.

**Multi-Factor Authentication**: puede elegir que los usuarios comprueben o no su identidad con MFA para poder activar sus roles. Solo tiene que realizar esta acción una vez por sesión, no cada vez que activen un rol. Al habilitar MFA, es conveniente tener en cuenta dos sugerencias:

* Los usuarios que tienen cuentas de Microsoft para sus direcciones de correo electrónico (normalmente @outlook.com, pero no siempre) no se pueden registrar en Azure MFA. Si quiere asignar roles a los usuarios con cuentas de Microsoft, debe establecerlos en administradores permanentes o deshabilitar MFA para ese rol.
* No se puede deshabilitar MFA para roles con privilegios elevados en Azure AD y Office 365. Esta es una característica de seguridad porque estos roles deben protegerse cuidadosamente:  
  
  * Administrador de aplicaciones
  * Administrador del servidor proxy de la aplicación
  * Administrador de facturación  
  * Administrador de cumplimiento  
  * Administrador de servicios de CRM
  * Aprobador del acceso a la Caja de seguridad del cliente
  * Escritor de directorio  
  * Administrador de Exchange  
  * Administrador global
  * Administrador de servicios de Intune
  * Administrador de buzón de correo  
  * Soporte para asociados de nivel 1  
  * Soporte para asociados de nivel 2  
  * Administrador de roles con privilegios   
  * Administrador de seguridad  
  * Administrador de SharePoint  
  * Administrador de Skype Empresarial  
  * Administrador de cuenta de usuario  

Para obtener más información sobre cómo usar MFA con PIM, vea [Exigencia de MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

