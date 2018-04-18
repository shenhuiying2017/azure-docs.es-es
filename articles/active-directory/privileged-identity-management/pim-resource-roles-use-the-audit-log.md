---
title: 'Privileged Identity Management para Azure Resources: auditoría de recursos| Microsoft Docs'
description: Se explica cómo obtener una vista de toda la actividad de rol para un recurso determinado.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management: auditoría de roles de recursos

Auditoría de recursos proporciona una vista de todas las actividades de rol del recurso. Puede filtrar la información con una fecha predefinida o un intervalo personalizado.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

La auditoría de recursos también proporciona acceso rápido para ver los detalles de la actividad de un usuario. En "Tipo de auditoría", seleccione "Activar". Haga clic en "(actividad)" para ver las acciones del usuario sobre los recursos de Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Mi auditoría

Mi auditoría proporciona una vista de la actividad personal de roles de un usuario. Puede filtrar la información con una fecha predefinida o un intervalo personalizado.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Consulta de la activación y la actividad de los recursos de Azure

Si necesita ver las acciones que realizó un usuario específico en varios recursos, puede revisar la actividad de los recursos de Azure asociada a un período de activación específico (para usuarios aptos). Primero, seleccione el usuario de la vista Miembros o de la lista de miembros de un rol específico. Como resultado se muestra una gráfica de las acciones del usuario en los recursos de Azure por fecha y las activaciones de rol recientes durante ese período.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Al seleccionar la activación de un rol específico se muestran los detalles de la activación del rol y la actividad de los recursos de Azure correspondiente al tiempo de actividad de ese usuario.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

