---
title: 'Privileged Identity Management para Azure Resources: asignación de roles| Microsoft Docs'
description: Se describe cómo asignar roles en PIM.
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management: asignación de roles de recursos

## <a name="assign-roles"></a>Asignación de roles

Para asignar un rol a un usuario o un grupo, seleccione el rol (en la vista Roles). 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

También puede hacer clic en Agregar en la barra de acción (en la vista Miembros).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Si agrega un usuario o un grupo desde la pestaña Miembros, debe hacer lo siguiente: 

1. Elija un rol en el menú Agregar antes de seleccionar un usuario o un grupo.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Elija un usuario o grupo del directorio.

3. Elija el tipo de asignación correspondiente del menú desplegable. 

    - **Asignación Just-In-Time:** proporciona acceso apto pero no persistente al rol durante un período determinado de tiempo o indefinidamente (si se ha configurado en Roles) al usuario o los miembros del grupo. 
    - **Asignación directa:** no requiere que el usuario o los miembros del grupo activen la asignación del rol (esto se conoce como acceso persistente). Microsoft recomienda la asignación directa para usos breves, como los turnos de guardia o actividades temporales, que, una vez terminados, ya no necesitan acceso.

Una casilla debajo de la lista desplegable Tipo de asignación permite especificar si la asignación debe ser permanente (siempre apta para la activación Just-In-Time/siempre activa para la asignación directa).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Si otro administrador ha especificado la duración máxima para cada tipo de asignación en la configuración de los roles, la casilla no admite modificación.

 Para especificar la duración de una asignación específica, anule la selección de la casilla y modifique los campos de fecha y hora de inicio o finalización.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Administración de asignaciones de roles

Los administradores pueden administrar las asignaciones de roles mediante la selección de Roles o Miembros en el panel de navegación izquierdo. La selección de Roles permite a los administradores definir el ámbito de sus tareas de administración a un rol específico, mientras que en Miembros se muestran todas las asignaciones de roles de usuario y de grupo para el recurso.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Si tiene un rol pendiente de activación, se muestra un banner de notificación en la parte superior de la página al ver la pertenencia.


## <a name="modify-existing-assignments"></a>Modificación de las asignaciones existentes

Para modificar las asignaciones existentes desde la vista de detalles del usuario o grupo, seleccione Cambiar configuración en la barra de acción de la parte superior de la página. Cambie el tipo de asignación a Just-In-Time o directa.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
