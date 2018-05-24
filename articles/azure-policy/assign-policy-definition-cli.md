---
title: Use la CLI de Azure para crear una asignación de directiva que identifique recursos no compatibles en el entorno de Azure
description: Use PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 27f00f24c1c644e340ff8a2843b56e863136c368
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194815"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure con la CLI de Azure

El primer paso para entender el cumplimiento en Azure es identificar el estado de sus recursos. Esta guía de inicio rápido lo guiará por el proceso de creación de una asignación de directiva para identificar las máquinas virtuales que no están usando discos administrados.

Al finalizar este proceso, habrá identificado correctamente máquinas virtuales que no utilizan discos administrados. *No son compatibles* con la asignación de directiva.

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se usa la CLI de Azure para crear una asignación de directiva e identificar recursos no compatibles en el entorno de Azure.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Esta guía de inicio rápido requiere la ejecución de la versión 2.0.4 de la CLI de Azure o una versión posterior para instalar y usar la CLI en un entorno local. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>requisitos previos

Registre el proveedor de recursos de Policy Insights mediante la CLI de Azure. El registro del proveedor de recursos garantiza que la suscripción funcionará con él. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación de registro para este. Esta operación está incluida en los roles Colaborador y Propietario. Para registrar el proveedor de recursos, ejecute el siguiente comando:

```azurecli-interactive
az provider register –-namespace 'Microsoft.PolicyInsights'
```

Para más información acerca del registro y la visualización de los proveedores de recursos, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En esta guía de inicio rápido se crea una asignación de directiva y se le asigna la definición de Auditoría de máquinas virtuales sin discos administrados. Esta definición de directiva identifica los recursos que no cumplen las condiciones establecidas en la definición de directiva.

Ejecute el siguiente comando para crear una asignación de directiva:

```azurecli-interactive
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

El comando anterior usa la siguiente información:

- **Name**: nombre para mostrar para la asignación de directiva. En este caso, se usa la *Auditoría de máquinas virtuales sin discos administrados*.
- **Policy**: identificador de definición de la directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*. Para obtener el identificador de definición de directiva, ejecute este comando: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Scope**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos. Asegúrese de sustituir &lt;scope&gt; por el nombre del grupo de recursos.

## <a name="identify-non-compliant-resources"></a>Identificación de recursos no compatibles

Para ver los recursos que no son compatibles con esta nueva asignación, ejecute los siguientes comandos para obtener el identificador de asignación de directiva:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks' }
$policyAssignment.PolicyAssignmentId
```

Para obtener más información acerca de la asignación de identificadores de directiva, consulte [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

A continuación, ejecute el siguiente comando para obtener los identificadores de los recursos no compatibles que se copian en un archivo JSON:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Los resultados deben tener una apariencia similar al ejemplo siguiente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Son comparables a lo que normalmente vería en **Recursos no compatibles**, en la vista de Azure Portal.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con otros tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no piensa continuar, ejecute este comando para eliminar la asignación que creó:

```azurecli-interactive
az policy assignment delete –name 'Audit Virtual Machines without Managed Disks Assignment' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos incompatibles en el entorno de Azure.

Para obtener información sobre la asignación de directivas y garantizar la compatibilidad de los recursos creados en el **futuro**, continúe con este tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](create-manage-policy.md)