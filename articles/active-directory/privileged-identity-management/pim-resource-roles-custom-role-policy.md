---
title: 'Privileged Identity Management para Azure Resources: uso de roles personalizados para seleccionar el destino de la configuración de PIM| Microsoft Docs'
description: Se describe cómo usar roles personalizados en PIM para Azure Resources.
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
ms.openlocfilehash: 9ac14125b837f45634870941e72ea63e9921ca70
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Uso de roles personalizados para seleccionar el destino de la configuración de PIM

Puede que sea necesario aplicar una configuración de PIM estricta a algunos miembros de un rol y, al mismo tiempo, proporcionar una mayor autonomía a otros. Considere un escenario donde su organización contrata varios asociados de contrato para ayudarle en el desarrollo de una aplicación que se ejecutará en una suscripción de Azure. 

Como administrador de recursos, quiere que los empleados de su organización tengan acceso apto sin necesidad de aprobación, pero todos los asociados de contrato deben buscar la aprobación cuando soliciten la activación. Siga los pasos que se describen a continuación para habilitar la selección del destino de la configuración de PIM para los roles de recursos de Azure.

## <a name="create-the-custom-role"></a>Creación del rol personalizado

[Use esta guía para crear un rol personalizado para un recurso](../role-based-access-control-custom-roles.md).

Incluya un nombre descriptivo para que pueda recordar fácilmente qué rol integrado pretende duplicar.

>[!NOTE]
>Asegúrese de que el rol personalizado sea un duplicado del rol deseado y que su ámbito coincida con el rol integrado.

## <a name="apply-pim-settings"></a>Aplicación de la configuración de PIM

Una vez creado el rol en el inquilino, vaya a PIM y seleccione el recurso al que va enfocado el rol.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Configure los valores de rol de PIM](pim-resource-roles-configure-role-settings.md) que se deberían aplicar a estos miembros.

Por último, [asigne roles](pim-resource-roles-assign-roles.md) al grupo distinto de miembros que quiere que sea el destino de esta configuración.

## <a name="next-steps"></a>Pasos siguientes

[Revisar los propietarios de la suscripción](pim-resource-roles-perform-access-review.md)
