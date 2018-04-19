---
title: 'Privileged Identity Management para recursos de Azure: MFA | Microsoft Docs'
description: En este documento se describe cómo habilitar la autenticación multifactor para los recursos de PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management: roles de recursos: MFA

Los roles de PIM para recursos de Azure permiten que los administradores de recursos y los administradores de identidad protejan la infraestructura crítica de Azure con pertenencia de tiempo limitado y acceso Just-In-Time. Además, PIM proporciona la aplicación opcional de Azure Multi-Factor Authentication (MFA) para dos escenarios distintos.

## <a name="require-mfa-to-activate"></a>Requerir MFA para activación

Los administradores de recursos pueden requerir que los miembros válidos de un rol ejecuten correctamente Azure MFA antes de poder realizar la activación. Este proceso garantiza que el usuario que solicita la activación sea quien dice ser con una exactitud razonable. Aplicar esta opción permite proteger los recursos críticos en situaciones en las que es posible que la cuenta del usuario se haya puesto en peligro. 

Para cumplir con este requisito, seleccione un recurso en la lista de los recursos administrados. En el [panel de información general](pim-resource-roles-overview-dashboards.md), seleccione un rol en la lista de roles que aparece en la esquina inferior derecha de la pantalla.

Además, puede obtener la configuración de los roles desde las pestañas "Roles" o "Configuración de roles" en el menú de navegación de la izquierda.

>[!Note]
>Si las opciones del menú de navegación de la izquierda aparecen atenuadas y en la parte superior de la página ve un banner que indica que tiene roles elegibles que se pueden activar, significa que no es un administrador activo y que debe [activarse](pim-resource-roles-activate-your-roles.md) antes de continuar.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Si está consultando la pertenencia de un rol, seleccione "Configuración de roles" en la barra que aparece en la parte superior de la pantalla para abrir el detalle de la configuración de roles.

Haga clic en el botón **Editar** en la parte superior para modificar la configuración de roles.

En la sección bajo **Activar**, active la casilla junto a **Require Multi-Factor Authentication to active** (Requerir Multi-Factor Authentication para activación) y haga clic en Guardar.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Requerir MFA en la asignación

En algunos casos, es posible que un administrador de recursos desee asignar un miembro a un rol durante un breve período (por ejemplo, un día) y no necesita que los miembros asignados soliciten la activación. En este escenario, PIM no puede aplicar MFA cuando el miembro usa su asignación de roles porque ya está activo en el rol desde el momento de su asignación.

Para garantizar que el administrador de recursos que cumple la asignación sea quien dice ser, puede aplicar MFA en la asignación.

En la misma pantalla de detalles de la configuración de roles, active la casilla junto a "Require Multi-Factor Authentication on assignment" (Requerir Multi-Factor Authentication en la asignación).

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Pasos siguientes

[Solicitar aprobación para activar](pim-resource-roles-approval-workflow.md)

[Usar el registro de auditoría](pim-resource-roles-use-the-audit-log.md)



