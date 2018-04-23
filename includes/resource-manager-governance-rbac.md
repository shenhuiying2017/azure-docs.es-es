---
title: archivo de inclusión
description: archivo de inclusión
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b4b06119b9d46781b967fc8d98808c60d2b41ccb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
Desea asegurarse de que los usuarios de una organización tengan el nivel adecuado de acceso a estos recursos. No desea conceder acceso ilimitado a los usuarios, pero también es necesario garantizar que puedan realizar su trabajo. El control de acceso basado en rol (RBAC) le permite administrar los usuarios con permiso para completar acciones específicas en un ámbito. Un rol define un conjunto de acciones permitidas. Asigne el rol a un ámbito y especifique los usuarios que pertenecen a ese rol para el ámbito.

Al planear su estrategia de control de acceso, conceda a los usuarios los privilegios mínimos para que realicen su trabajo. La siguiente imagen muestra un patrón sugerido para la asignación de RBAC.

![Scope](./media/resource-manager-governance-rbac/role-examples.png)

Hay tres roles que se aplican a todos los recursos: Propietario, Colaborador y Lector. Todas las cuentas asignadas al rol Propietario deben estar estrechamente controladas y usarse con poca frecuencia. Los usuarios que solo tienen que observar el estado de las soluciones deben concederse el rol Lector.

A la mayoría de usuarios se les concede [roles específicos de recursos](../articles/role-based-access-control/built-in-roles.md) o [roles personalizados](../articles/role-based-access-control/custom-roles.md), ya sea en el nivel de suscripción o de grupo de recursos. Estos roles definen estrechamente las acciones permitidas. Al asignar usuarios a estos roles, concede el acceso necesario a los usuarios sin permitir demasiado control. Puede asignar una cuenta a más de un rol y que el usuario obtenga los permisos combinados de los roles. Conceder acceso en el nivel de recursos suele ser demasiado restrictivo para los usuarios, pero puede funcionar para un proceso automatizado diseñado para tareas específicas.

### <a name="who-can-assign-roles"></a>Quién puede asignar roles

Para crear y quitar las asignaciones de rol, los usuarios deben tener acceso a `Microsoft.Authorization/roleAssignments/*`. Esta acción se concede mediante los roles Propietario o Administrador de acceso de usuario.