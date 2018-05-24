---
title: Usar roles personalizados para seleccionar el destino de la configuración de Privileged Identity Management para los recursos de Azure | Microsoft Docs
description: Aquí se describe cómo usar roles personalizados para los recursos de Azure con PIM.
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
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32148947"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Usar roles personalizados para seleccionar el destino de la configuración de Privileged Identity Management

Puede que sea necesario aplicar una configuración de Privileged Identity Management (PIM) estricta a algunos miembros de un rol y, al mismo tiempo, proporcionar una mayor autonomía a otros. Imagine un escenario en el que su organización contrata varios a asociados para que le ayuden en el desarrollo de una aplicación que se ejecutará en una suscripción de Azure.

Como administrador de recursos, quiere que los empleados puedan obtener acceso sin necesidad de aprobación. Sin embargo, todos los asociados contratados deben obtener una aprobación cuando soliciten acceso a los recursos de la organización.

Siga los pasos que se describen en la siguiente sección para configurar las opciones de destino de PIM para los roles de recursos de Azure.

## <a name="create-the-custom-role"></a>Creación del rol personalizado

Para crear un rol personalizado para un recurso, siga los pasos descritos en [Create custom roles for Azure Role-Based Access Control](../role-based-access-control-custom-roles.md) (Crear roles personalizados para el control de acceso basado en rol de Azure).

Cuando cree un rol personalizado, incluya un nombre descriptivo para que pueda recordar fácilmente qué rol integrado pretende duplicar.

> [!NOTE]
> Asegúrese de que el rol personalizado sea un duplicado del rol integrado que quiere duplicar, y que su ámbito coincida con el rol integrado.

## <a name="apply-pim-settings"></a>Aplicación de la configuración de PIM

Una vez que se haya creado el rol en el inquilino, vaya a Azure Portal y acceda al panel **Privileged Identity Management - Recursos de Azure**. Seleccione el recurso al cual se aplica el rol.

![Panel "Privileged Identity Management - Recursos de Azure"](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configure los valores de rol de PIM](pim-resource-roles-configure-role-settings.md) que se deberían aplicar a estos miembros del rol.

Por último, [asigne roles](pim-resource-roles-assign-roles.md) al grupo distinto de miembros que quiere establecer como destino de esta configuración.

## <a name="next-steps"></a>Pasos siguientes

[Revisar los propietarios de las suscripciones y el acceso](pim-resource-roles-perform-access-review.md)
