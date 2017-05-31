---
title: Uso del control de acceso basado en roles para administrar Azure Site Recovery | Microsoft Docs
description: "En este artículo se explica cómo aplicar y utilizar el control de acceso basado en roles (RBAC) para administrar las implementaciones de Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 08a4d0fa673a37c61e57daed66ab6768e0276ca8
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Uso del control de acceso basado en roles para administrar las implementaciones de Azure Site Recovery

El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Con el uso de RBAC, puede separar las responsabilidades entre los miembros del equipo y conceder únicamente permisos de acceso específicos a los usuarios según sea necesario para realizar tareas específicas.

Azure proporciona varios roles integrados para controlar las operaciones de administración de recursos. Más información sobre [roles integrados del control de acceso basado en rol de Azure](../active-directory/role-based-access-built-in-roles.md)

Si quiere definir sus propios roles para tener un mayor control, consulte cómo [crear roles personalizados](../active-directory/role-based-access-control-custom-roles.md) en Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permisos necesarios para habilitar la replicación de nuevas máquinas virtuales
Cuando una nueva máquina virtual se replica en Azure con Azure Site Recovery, los niveles de acceso del usuario asociado se validan para asegurarse de que el usuario tiene los permisos necesarios para usar los recursos de Azure proporcionados en Site Recovery.

Para habilitar la replicación de una nueva máquina virtual, el usuario debe tener:
* Permiso para crear una máquina virtual en el grupo de recursos seleccionado
* Permiso para crear una máquina virtual en la red virtual seleccionada
* Permiso para escribir en la cuenta de Storage seleccionada

Un usuario necesita los siguientes permisos para completar la replicación de una nueva máquina virtual.

> [!IMPORTANT]
>Asegúrese de que los permisos pertinentes se agregan según el modelo de implementación (Resource Manager o clásico) utilizado para la implementación de recursos.

| **Tipo de recurso** | **Modelo de implementación** | **Permiso** |
| --- | --- | --- |
| Proceso | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clásico | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Red | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clásico | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Almacenamiento | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clásico | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de recursos | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere el uso de los [roles integrados](../active-directory/role-based-access-built-in-roles.md) "Colaborador de la máquina virtual" y "Colaborador de la máquina virtual clásica" respectivamente para la implementación según el modelo de Resource Manager o el clásico.

## <a name="next-steps"></a>Pasos siguientes
* [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md): introducción a RBAC en el Portal de Azure.
* Aprenda a administrar el acceso con:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [API DE REST](../active-directory/role-based-access-control-manage-access-rest.md)
* [Solución de problemas del control de acceso basado en roles](../active-directory/role-based-access-control-troubleshooting.md): sugerencias para resolver problemas frecuentes.

