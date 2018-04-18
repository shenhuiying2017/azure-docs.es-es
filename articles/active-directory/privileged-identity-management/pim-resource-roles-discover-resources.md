---
title: 'Privileged Identity Management para Azure Resources: detección y administración de recursos de Azure| Microsoft Docs'
description: Se describe cómo proteger los recursos de Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Búsqueda y administración de recursos de Azure

Si su organización ya usa PIM de Azure AD para proteger a los administradores del directorio o es el propietario de una suscripción que busca proteger los recursos de producción, está en el lugar adecuado.

La primera vez que habilita PIM para Azure Resources, debe detectar y seleccionar los recursos que quiere proteger con PIM. No hay ningún límite en cuanto al número de recursos que se pueden administrar con PIM, pero se recomienda empezar primero por los recursos más críticos (producción).

> [!Note]
> Solo los recursos de suscripción se pueden buscar y seleccionar para administración. Decidir administrar una suscripción en PIM también permitirá la administración de todos los recursos secundarios.

## <a name="discover-resources"></a>Detección de recursos

Vaya a PIM de Azure AD y seleccione los recursos de Azure en la sección Administrar del menú de navegación izquierdo.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Si es la primera vez que usa PIM para Azure Resources, deberá ejecutar la detección para encontrar recursos para administrar.
Haga clic en el botón "Discover resources" (Detectar recursos) en el centro de la pantalla para iniciar la experiencia de detección.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Si otro administrador de directorio o de recursos de su organización ya administra un recurso de Azure con PIM, o tiene una asignación de roles válida para un recurso, la vista de lista contendrá el mensaje: "Discover resources or activate an eligible role assignment to continue" (Detectar recursos o activar una asignación de roles válida para continuar). 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Cuando se seleccione el botón, en la barra de acciones, o en el medio de la pantalla para detectar recursos, verá una lista de las suscripciones disponibles para administración. En este momento, si ve suscripciones resaltadas significa que están protegidas por PIM.

> [!Note]
> Para evitar que otro administrador de recursos quite la configuración de PIM, una vez que se administra una suscripción no se puede dejar de administrar.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Mantenga el mouse sobre una suscripción que le gustaría proteger con PIM y active la casilla del extremo izquierdo de la fila. Puede seleccionar varias suscripciones a la vez.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Para iniciar el proceso de incorporación, seleccione el botón "Administrar recurso" en la barra de la parte superior de la pantalla.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Los recursos seleccionados se administran ahora con PIM. Cierre la pantalla de detección mediante la "X" de la esquina superior derecha de la página y haga clic en Actualizar en la barra de la parte superior de la pantalla de administración de recursos de Azure para empezar a administrar la configuración de PIM y asignar miembros.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Pasos siguientes

[Configurar los roles](pim-resource-roles-configure-role-settings.md)

[Asignar roles en PIM](pim-resource-roles-assign-roles.md)
