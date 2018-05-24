---
title: Asignaciones aptas y visibilidad de recursos para Azure en Privileged Identity Management | Microsoft Docs
description: Aquí se describe cómo asignar miembros aptos a los roles de recursos al usar PIM.
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
ms.openlocfilehash: 4804d930a98192d64245784058920eeba7d30212
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149993"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Asignaciones aptas y visibilidad de recursos con Privileged Identity Management

Privileged Identity Management (PIM) para los roles de recursos de Azure proporciona una mayor seguridad para las organizaciones con recursos de Azure críticos. Los administradores de recursos pueden usar PIM para asignar miembros aptos a los roles de recursos. Obtenga más información sobre los distintos tipos y estados de asignación para los roles de recursos de Azure de las siguientes secciones. 

## <a name="assignment-types"></a>Tipos de asignación

PIM para Azure Resources ofrece dos tipos de asignación distintos:

- Apto
- Active

Las asignaciones aptas requieren que el miembro del rol realice una acción para usarlo. Estas acciones pueden incluir pasar correctamente una comprobación de autenticación multifactor, proporcionar una justificación de negocios o solicitar la aprobación de los aprobadores designados.

Las asignaciones activas no requieren que el miembro realice ninguna acción para usar el rol. Los miembros asignados como activos tienen siempre los privilegios asignados al rol.

## <a name="assignment-duration"></a>Duración de la asignación

Los administradores de recursos pueden elegir entre dos opciones para cada tipo de asignación al configurar PIM en un rol. Estas opciones se convierten en la duración máxima predeterminada cuando se asigna un rol a un miembro en PIM. 

Un administrador puede elegir uno de estos tipos de asignación:

- Allow permanent eligible assignment (Permitir la asignación apta permanente)
- Allow permanent active assignment (Permitir la asignación activa permanente)

Por otro lado, un administrador puede elegir uno de estos tipos de asignación:

- Expire eligible assignments after (Expirar las asignaciones aptas después de)
- Expire active assignments after (Expirar las asignaciones activas después de)

Si un administrador de recursos elige **Permitir la asignación apta permanente** o **Permitir la asignación activa permanente**, todos los administradores que asignan miembros al recurso tienen la posibilidad de asignar pertenencias permanentes.

Si un administrador de recursos selecciona la opción **Expirar las asignaciones aptas después de** o **Expirar las asignaciones activas después de**, el administrador de recursos controla el ciclo de vida de la asignación al exigir que todas las asignaciones tengan una fecha de inicio y de finalización especificada.

> [!NOTE] 
> Los administradores de recursos pueden renovar todas las asignaciones que tienen una fecha de finalización específica. Asimismo, los miembros pueden iniciar solicitudes de autoservicio para [extender o renovar las asignaciones](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Estados de asignación

PIM para recursos de Azure tiene dos estados de asignación distintos que aparecen en la pestaña **Roles activos** en las vistas **Mis roles**, **Roles** y **Miembros** de PIM. Estos estados son:

- Asignado
- Activado

Cuando vea una pertenencia que aparezca en **Roles activos**, podrá usar el valor de la columna **ESTADO** para diferenciar entre los usuarios **asignados** como activos y los usuarios que **activaron** una asignación apta y que ahora están activos.

## <a name="next-steps"></a>Pasos siguientes

[Asignar roles en Privileged Identity Management](pim-resource-roles-assign-roles.md)
