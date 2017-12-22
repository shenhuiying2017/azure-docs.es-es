---
title: "Visualización de asignaciones de acceso a recursos de Azure | Microsoft Docs"
description: "Visualización y administración de todas las asignaciones de control de acceso basado en rol de cualquier usuario o grupo en Azure Portal"
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: andredm
ms.openlocfilehash: 51b158ebfeea52e726213edd042277779f52124b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Visualización de asignaciones de acceso para usuarios y grupos en el portal de Azure
> [!div class="op_single_selector"]
> * [Administración del acceso por usuario o grupo](role-based-access-control-manage-assignments.md)
> * [Administración del acceso por recurso](role-based-access-control-configure.md)

Gracias al control de acceso basado en rol (RBAC) de Azure Active Directory (Azure AD), puede administrar el acceso a los recursos de Azure. 

El acceso que se asigna con RBAC es específico porque se pueden restringir los permisos de dos formas:

* **Ámbito** : las asignaciones de roles RBAC se limitan a una suscripción, un grupo de recursos o un recurso específicos. Un usuario con acceso a un único recurso no puede tener acceso a otros recursos de la misma suscripción.
* **Rol** : dentro del ámbito de la asignación, se limita el acceso aún más mediante la asignación de un rol. Los roles pueden ser generales, como propietarios, o específicos, como lectores de máquina virtual.

Asimismo, solo pueden asignarse desde dentro de la suscripción, el grupo de recursos o el recurso que esté en el ámbito de la asignación. No obstante, puede ver todas las asignaciones de acceso de un usuario o grupo determinado en un solo lugar. Puede tener hasta 2000 asignaciones de roles en cada suscripción. 

Para administrar el acceso a los recursos, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Visualización de asignaciones de acceso
Para buscar las asignaciones de acceso de un único usuario o grupo, empiece en [Azure Portal](http://portal.azure.com)de Azure Active Directory.

1. Seleccione **Azure Active Directory**. Si esta opción no está visible en la lista de navegación, seleccione **More Services** (Más servicios) y luego desplácese hacia abajo hasta encontrar **Azure Active Directory**.
2. Seleccione **Usuarios y grupos** y luego **Todos los usuarios** o **Todos los grupos**. En este ejemplo, nos centramos en los usuarios individuales.
    ![Captura de pantalla de administración de usuarios y grupos en Azure Active Directory](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Busque el usuario por nombre o nombre de usuario.
4. Seleccione **Recursos de Azure** en la hoja de usuario. Aparecen todas las asignaciones de dicho usuario.

### <a name="read-permissions-to-view-assignments"></a>Permisos de lectura para ver las asignaciones
Esta página solo muestra las asignaciones de acceso que tiene permiso para leer. Por ejemplo, tiene acceso de lectura a la suscripción A y va a la hoja de recursos de Azure para comprobar las asignaciones de un usuario. Puede ver sus asignaciones de acceso de la suscripción A, pero no verá que también tiene asignaciones de acceso de la B.

## <a name="delete-access-assignments"></a>Eliminación de asignaciones de acceso
En esta hoja, puede eliminar las asignaciones de acceso que se asignaron directamente a un usuario o grupo. Si la asignación de acceso se hereda de un grupo primario, debe ir a la suscripción o al recurso y administrarla allí.

1. En la lista de todas las asignaciones de acceso de un usuario o grupo, seleccione la que quiera eliminar.
2. Seleccione **Quitar** y después **Sí** para confirmar.
    ![Captura de pantalla de eliminación de asignación de acceso](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Pasos siguientes

* Vea una introducción al control de acceso basado en rol en [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](role-based-access-control-configure.md)
* Consulte los [roles integrados en RBAC](role-based-access-built-in-roles.md)

