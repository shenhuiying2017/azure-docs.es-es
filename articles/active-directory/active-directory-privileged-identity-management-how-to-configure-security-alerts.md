---
title: "Configuración de alertas de seguridad | Microsoft Docs"
description: "Aprenda cómo configurar alertas de seguridad para la extensión de Privileged Identity Management de Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: e057120e31eeebc3da274536c09d6d9972854338
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Configuración de alertas de seguridad en Privileged Identity Management de Azure AD
## <a name="security-alerts"></a>Alertas de seguridad
Privileged Identity Management (PIM) de Azure genera alertas cuando existen actividades sospechosas o no seguras en su entorno. Cuando se desencadena una alerta, se muestra en el panel de PIM. Seleccione la alerta para ver un informe en el que se enumeren los usuarios o roles que activaron la alerta.

![Alertas de seguridad del panel de PIM: captura de pantalla][1]

| Alerta | Desencadenador | Recomendación |
| --- | --- | --- |
| **Se están asignando roles fuera de PIM** |Se ha asignado a un administrador a un rol de forma permanente, fuera de la interfaz de PIM. |Revise la nueva asignación de roles. Puesto que otros servicios solo pueden asignar administradores permanentes, cámbiela por una asignación apta en caso necesario. |
| **Se están activando roles con demasiada frecuencia** |Había demasiadas reactivaciones del mismo rol en el tiempo permitido en la configuración. |Póngase en contacto con el usuario para ver por qué activó el rol tantas veces. Puede que el límite de tiempo sea demasiado corto para que complete sus tareas, o quizá utilice scripts para activar automáticamente un rol. |
| **No se necesita la autenticación multifactor para la activación de los roles** |Hay roles sin MFA habilitado en la configuración. |MFA se requiere para los roles con los privilegios más elevados, pero se recomienda encarecidamente habilitar MFA para la activación de todos los roles. |
| **Los administradores no están usando sus roles con privilegios** |Hay administradores aptos que no han activado sus roles recientemente. |Inicie una revisión de acceso para determinar los usuarios que ya no necesitan acceso. |
| **Demasiados administradores globales** |Hay más administradores globales de lo que se recomienda. |Si tiene un gran número de administradores globales, es probable que los usuarios estén obteniendo más permisos de los que necesitan. Mueva usuarios a roles con menos privilegios, o establezca algunos de ellos como aptos para el rol en lugar de asignarlos de forma permanente. |

## <a name="configure-security-alert-settings"></a>Configuración de alertas de seguridad
Puede personalizar algunas de las alertas de seguridad de PIM para que funcionen con su entorno sus objetivos de entorno y seguridad. Siga estos pasos para llegar a la hoja de configuración:

1. Vaya al [Portal de Azure](https://portal.azure.com/) y seleccione el icono **Privileged Identity Management de Azure AD** en el panel.
2. Seleccione **Roles con privilegios administrados** > **Configuración** > **Configuración de alertas**.
   
    ![Navegación a la configuración de alertas de seguridad][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerta "Los roles se están activando con demasiada frecuencia"
Esta alerta se desencadena si un usuario activa el mismo rol con privilegios varias veces dentro de un período especificado. Puede configurar el período de tiempo y el número de activaciones.

* **Período de tiempo de renovación de activaciones**: especifique en días, horas, minutos y segundos, el período de tiempo que quiere usar para realizar el seguimiento de renovaciones sospechosas.
* **Number of activation renewals**(Número de renovaciones de activación): especifique el número de activaciones, de 2 a 100, que considere dignas de tener en cuenta, en el período de tiempo que eligió. Puede cambiar este valor moviendo el control deslizante o escribiendo un número en el cuadro de texto.

### <a name="there-are-too-many-global-administrators-alert"></a>Alerta "Demasiados administradores globales"
El PIM desencadena esta alerta si se cumplen dos criterios diferentes, y puede configurar ambos. En primer lugar, debe alcanzar un determinado umbral de administradores globales. En segundo lugar, un porcentaje determinado de las asignaciones de roles totales deben ser administradores globales. Si solo se cumple uno de estos criterios, la alerta no aparece.  

* **Número mínimo de administradores globales**: especifique el número de administradores globales, de 2 a 100, que considera una cantidad poco segura.
* **Porcentaje de administradores globales**: especifique el porcentaje de los administradores que son administradores globales, de 0 % a 100 %, que es poco seguro en su entorno.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alerta "Los administradores no están usando sus roles con privilegios"
Esta alerta se desencadena si un usuario pasa un cierto tiempo sin activar un rol.

* **Número de días**: especifique el número de días, de 0 a 100, que un usuario puede pasar sin activar un rol.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
