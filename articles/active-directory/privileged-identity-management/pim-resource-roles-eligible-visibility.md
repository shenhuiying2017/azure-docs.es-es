---
title: 'Privileged Identity Management para Azure Resources: asignaciones aptas y visibilidad de recursos | Microsoft Docs'
description: Se describe cómo asignar los miembros como aptos a los roles de recursos.
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
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Asignaciones aptas y visibilidad de recursos

Los roles de PIM para recursos de Azure proporcionan una mayor seguridad para las organizaciones con recursos de Azure críticos. PIM ofrece a los administradores de recursos la posibilidad de asignar miembros como aptos a roles de recursos. A continuación puede encontrar más información sobre los distintos tipos y estados de asignación para roles de recursos de Azure. 

## <a name="assignment-types"></a>Tipos de asignación

PIM para Azure Resources ofrece dos tipos de asignación distintos:

- Apto
- Active

Las asignaciones aptas requieren que el miembro del rol realice una acción para usarlo. Estas acciones pueden incluir pasar correctamente una comprobación de Multi-Factor Authentication, proporcionar una justificación y solicitar la aprobación de aprobadores designados.

Las asignaciones activas no requieren que el miembro realice ninguna acción para usar el rol. Los miembros asignados como activos tienen siempre los privilegios que proporciona el rol.

## <a name="assignment-duration"></a>Duración de la asignación

Los administradores de recursos pueden elegir entre dos opciones para cada tipo de asignación al configurar PIM en un rol. Estas opciones se convierten en la duración máxima predeterminada cuando se asigna un rol a un miembro en PIM.

- Allow permanent eligible assignment (Permitir la asignación apta permanente)
- Allow permanent active assignment (Permitir la asignación activa permanente)

o

- Expire eligible assignments after (Expirar las asignaciones aptas después de)
- Expire active assignments after (Expirar las asignaciones activas después de)

Si un administrador de recursos elige permitir la asignación apta permanente o elegir la asignación activa permanente, todos los administradores que asignan miembros al recurso tienen la posibilidad de asignar pertenencia permanente.

La decisión de expirar las asignaciones aptas o activas después de un número de días permite controlar la duración de la asignación al exigir que todas las asignaciones tengan una fecha de inicio y de finalización especificada.

>[!NOTE] 
>Los administradores de recursos pueden renovar todas las asignaciones con una fecha de finalización especificada y los miembros pueden iniciar solicitudes de autoservicio para [ampliar o renovar las asignaciones](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Estados de asignación

PIM para Azure Resources tiene dos estados de asignación distintos que aparecen en la pestaña "Roles activos" en las vistas Mis roles, Roles y Miembros de PIM. Estos estados son:

- Asignado
- Activado

Al ver una pertenencia que aparece en "Roles activos", la columna "ESTADO" le permite diferenciar entre los usuarios asignados como activos frente a los usuarios que activaron una asignación apta y ahora están activos.

## <a name="next-steps"></a>Pasos siguientes

[Asignar roles en PIM](pim-resource-roles-assign-roles.md)

