---
title: Activar roles para recursos de Azure mediante Privileged Identity Management | Microsoft Docs
description: Se describe cómo activar roles en PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a985e67cc566cc45b3ee6b8dc98e91a8f34abd1b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32151117"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Activar roles para recursos de Azure mediante Privileged Identity Management
Privileged Identity Management (PIM) presenta una nueva experiencia de activación de roles para los recursos de Azure. Los miembros del rol que sean aptos pueden programar la activación para una fecha y hora futuras. Asimismo, también pueden seleccionar una duración de activación específica dentro del valor máximo establecido (configurado por los administradores). Para obtener más información, consulte [How to activate or deactivate roles in Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md) (Cómo activar o desactivar roles en Privileged Identity Management para Azure AD).

## <a name="activate-roles"></a>Activación de roles
Vaya a la sección **Mis roles** en el panel izquierdo. Seleccione **Activar** para el rol que quiera activar.

![Pestaña "Roles aptos" del panel "Mis roles".](media/azure-pim-resource-rbac/rbac-roles.png)

En el menú **Activaciones**, escriba la fecha de inicio y la hora para activar el rol. Si lo desea, reduzca la duración de la activación (el período de tiempo durante el cual el rol permanece activo) y escriba una justificación si procede. A continuación seleccione **Activar**.

Si no se modifican la fecha de inicio y la hora, el rol se activa en unos segundos. En el panel **Mis roles**, se muestra un mensaje emergente que indica que el rol se puso en la cola para su activación. Seleccione el botón Actualizar para borrar este mensaje.

![Panel "Mis roles" con un mensaje emergente y una notificación acerca de un elemento pendiente de aprobar](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Si la activación está programada para una fecha y hora futuras, la solicitud pendiente aparecerá en la pestaña **Solicitudes pendientes** del panel izquierdo. Si la activación del rol ya no es necesaria, puede cancelar la solicitud si selecciona el botón **Cancelar** .

![Lista de solicitudes pendientes con botones "Cancelar"](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Aplicar prácticas de Just Enough Administration.

PIM para recursos de Azure facilita la aplicación de los procedimientos recomendados de JEA (Just Enough Administration) en las asignaciones de roles para recursos. Los usuarios y los miembros del grupo con asignaciones a suscripciones o grupos de recursos de Azure pueden activar su asignación de roles existente en un ámbito menor. 

En la página de búsqueda, busque el recurso subordinado que necesite administrar.

![Seleccionar un recurso](media/azure-pim-resource-rbac/azure-resources-02.png)

Seleccione **Mis roles** del panel izquierdo y elija el rol que desee activar. El tipo de asignación es **Heredado**, puesto que el rol se asignó a la suscripción, en lugar de al grupo de recursos.

![Lista de asignaciones de roles aptos, con el tipo de asignación resaltado](media/azure-pim-resource-rbac/my-roles-02.png)
