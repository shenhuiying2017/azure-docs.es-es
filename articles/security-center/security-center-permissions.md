---
title: Permisos en Azure Security Center | Microsoft Docs
description: "En este artículo se explica cómo Azure Security Center usa el control de acceso basado en roles para asignar permisos a los usuarios e identifica las acciones permitidas para cada rol."
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: 0aaa99dda44d2020afd3e841e84020eb4ff87a85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="permissions-in-azure-security-center"></a>Permisos en Azure Security Center

Azure Security Center usa el [control de acceso basado en roles (RBAC)](../active-directory/role-based-access-control-configure.md), que proporciona [roles integrados](../active-directory/role-based-access-built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios de Azure.

Security Center evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso.

Además de estos roles, hay dos roles específicos de Security Center:

* **Lector de seguridad**: un usuario que pertenece a este rol tiene derechos de visualización en Security Center. El usuario puede ver las recomendaciones, las alertas, una directiva de seguridad y los estados de seguridad, pero no puede realizar cambios.
* **Administrador de seguridad**: un usuario que pertenece a este rol tiene los mismos derechos que el lector de seguridad, y puede actualizar la directiva de seguridad y descartar las alertas y las recomendaciones.

> [!NOTE]
> Los roles de seguridad Lector de seguridad y Administrador de seguridad solo tienen acceso a Security Center. Los roles de seguridad descritos no tienen acceso a otras áreas de servicio de Azure, como Storage, Web y móvil o Internet de las cosas.
>
>

## <a name="roles-and-allowed-actions"></a>Roles y acciones permitidas

En la siguiente tabla se muestran los roles y las acciones permitidas en Security Center. Una X indica que la acción se permite para ese rol.

| Rol | Editar directivas de seguridad | Aplicar recomendaciones de seguridad en un recurso | Descartar alertas y recomendaciones | Ver alertas y recomendaciones |
|:--- |:---:|:---:|:---:|:---:|
| Propietario de la suscripción | X | X | X | X |
| Colaborador de la suscripción | X | X | X | X |
| Propietario del grupo de recursos | -- | X | -- | X |
| Colaborador del grupo de recursos | -- | X | -- | X |
| Lector | -- | -- | -- | X |
| Administrador de seguridad | X | -- | X | X |
| Lector de seguridad | -- | -- | -- | X |

> [!NOTE]
> Es recomendable que asigne el rol de menos permisos que los usuarios necesiten para realizar sus tareas. Por ejemplo, asigne el rol Lector a los usuarios que solo necesiten ver información sobre el estado de seguridad de los recursos, pero no llevar a cabo acciones como aplicar recomendaciones o editar directivas.
>
>

## <a name="next-steps"></a>Pasos siguientes
En este artículo se ha explicado cómo Security Center usa RBAC para asignar permisos a los usuarios e identifica las acciones permitidas para cada rol. Ahora que ya está familiarizado con las asignaciones de roles necesarios para supervisar el estado de seguridad de su suscripción, editar directivas de seguridad y aplicar recomendaciones, aprenderá los siguientes conceptos:

- [Establecer directivas de seguridad en Security Center](security-center-policies.md)
- [Administrar recomendaciones de seguridad en Security Center](security-center-recommendations.md)
- [Supervisar el estado de seguridad de los recursos de Azure](security-center-monitoring.md)
- [Responder a alertas de seguridad en Security Center](security-center-managing-and-responding-alerts.md)
- [Supervisar soluciones de seguridad de asociados](security-center-partner-solutions.md)
