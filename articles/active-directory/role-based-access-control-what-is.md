---
title: "Administración de acceso y permisos con RBAC: Azure RBAC | Microsoft Docs"
description: "Introducción al control de acceso basado en roles de Azure en el Portal de Azure. Use las asignaciones de roles para asignar permisos en el directorio."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 0462fe8ff75bdda397decb301c459795886e9e58
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Introducción al control de acceso basado en roles en Azure Portal
Las empresas de seguridad deben centrarse en conceder a los empleados los permisos exactos que necesiten. Un número elevado de permisos puede provocar que la cuenta esté expuesta a los atacantes. Si se conceden muy pocos, los empleados no podrán realizar su trabajo de manera eficaz. Gracias al control de acceso basado en roles (RBAC) de Azure, podrá abordar este problema, ya que es posible realizar una administración avanzada del acceso para Azure.

También podrá repartir las tareas entre el equipo y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. En lugar de proporcionar a todos los empleados permisos no restringidos en los recursos o la suscripción de Azure, puede permitir solo determinadas acciones. Por ejemplo, utilice RBAC para dejar que un empleado administre máquinas virtuales en una suscripción, y que otro pueda administrar bases de datos SQL en la misma suscripción.

## <a name="basics-of-access-management-in-azure"></a>Aspectos básicos de la administración de acceso en Azure
Cada suscripción de Azure está asociada a un directorio de Azure Active Directory (AD). Los usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Asigne estos derechos de acceso con el Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

Conceda acceso asignando el rol RBAC adecuado a usuarios, grupos y aplicaciones de un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso. Un rol asignado en un ámbito principal también concede acceso a los elementos secundarios dentro del mismo. Por ejemplo, un usuario con acceso a un grupo de recursos puede administrar todos los recursos que contiene, como sitios web, máquinas virtuales y subredes.

![Relación entre elementos de Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

El rol RBAC que se asigna determina qué recursos puede administrar el usuario, el grupo o la aplicación dentro de ese ámbito.

## <a name="built-in-roles"></a>Roles integrados
RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso:

* **propietario** tiene acceso completo a todos los recursos y cuenta con el derecho a delegar este acceso a otros.
* **colaborador** puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros.
* **lector** solo puede ver los recursos existentes de Azure.

El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite al usuario crear y administrar máquinas virtuales. No otorga acceso a la red virtual ni a la subred a las que se conecta la máquina virtual. 

[Roles RBAC integrados](role-based-access-built-in-roles.md) muestra los roles disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrado que se concede a los usuarios. Si quiere definir sus propios roles para tener un mayor control, consulte [Custom Roles in Azure RBAC](role-based-access-control-custom-roles.md)(Roles personalizados en RBAC de Azure).

## <a name="resource-hierarchy-and-access-inheritance"></a>Jerarquía de recursos de Azure y herencia de acceso
* Cada **suscripción** de Azure pertenece a un único directorio. (Pero cada directorio puede tener más de una suscripción).
* Cada **grupo de recursos** pertenece a una única suscripción.
* Cada **recurso** pertenece a un único grupo de recursos.

El acceso que se concede en el nivel principal se hereda en los ámbitos secundarios. Por ejemplo:

* Asigne el rol de lector a un grupo de Azure AD en el ámbito de la suscripción. Los miembros de ese grupo pueden ver todos los grupos de recursos y los recursos de la suscripción.
* Asigne el rol de colaborador a una aplicación en el ámbito del grupo de recursos. Puede administrar todos los tipos de recursos de ese grupo de recursos, pero no otros grupos de la suscripción.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>RBAC de Azure frente a administradores de la suscripción clásica
Los administradores y coadministradores de la suscripción clásica tienen acceso completo a la suscripción de Azure. Pueden administrar recursos con [Azure Portal](https://portal.azure.com) con las API de Azure Resource Manager, o bien con el [Portal de Azure clásico](https://manage.windowsazure.com) y el modelo de implementación clásica de Azure. En el modelo RBAC, a los administradores clásicos se les asigna el rol de propietario en el ámbito de suscripción.

RBAC de Azure solo es compatible con el Portal de Azure y las nuevas API de Azure Resource Manager. Los usuarios y las aplicaciones a los que se asignan roles RBAC no pueden usar el Portal de administración clásico ni el modelo de implementación clásica de Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorización para administración frente a operaciones de datos
RBAC de Azure solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y de las API de Azure Resource Manager. No todas las operaciones de nivel de datos para recursos de Azure pueden autorizarse. Por ejemplo, puede autorizar a un empleado que administre las cuentas de almacenamiento, pero no los blobs o las tablas de una cuenta de almacenamiento. De igual forma, una base de datos SQL se puede administrar, pero no las tablas que contiene.

## <a name="next-steps"></a>Pasos siguientes
* Introducción a [Control de acceso basado en roles en el Portal de Azure](role-based-access-control-configure.md).
* Consulte los [Roles RBAC integrados](role-based-access-built-in-roles.md)
* Defina sus propios [Custom Roles in Azure RBAC](role-based-access-control-custom-roles.md)
