---
title: 'Inicio rápido: Uso de PowerShell para crear una asignación de directiva que identifique recursos no compatibles en el entorno de Azure'
description: En esta guía de inicio rápido, se usa PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 770b3b1b52953f6fd3bd1874c28b2de45251ed17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195648"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Inicio rápido: Creación de una asignación de directiva para identificar recursos no compatibles con el módulo Azure RM PowerShell

El primer paso para entender el cumplimiento en Azure es identificar el estado de sus recursos. En esta guía de inicio rápido, se crea una asignación de directiva para identificar máquinas virtuales que no usan discos administrados. Cuando haya finalizado, podrá identificar máquinas virtuales que *no cumplan* con la asignación de directiva.

El módulo AzureRM PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Esta guía explica cómo usar AzureRM para crear una asignación de directiva. La directiva identifica recursos no compatibles en el entorno de Azure.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

- Antes de empezar, asegúrese de tener instalada la versión más reciente de PowerShell. Consulte el artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para información detallada.
- Actualice el módulo AzureRM de PowerShell a la versión más reciente. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).
- Registre el proveedor de recursos de Policy Insights mediante Azure PowerShell. El registro del proveedor de recursos garantiza que la suscripción funcionará con él. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación de registro para este. Esta operación está incluida en los roles Colaborador y Propietario. Para registrar el proveedor de recursos, ejecute el siguiente comando:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para más información acerca del registro y la visualización de los proveedores de recursos, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En esta guía de inicio rápido se crea una asignación de directiva y se le asigna la definición de *Auditoría de máquinas virtuales sin discos administrados*. Esta definición de directiva identifica los recursos que no cumplen las condiciones establecidas en la definición de directiva.

Ejecute los comandos siguientes para crear una nueva asignación de directiva:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'Audit Virtual Machines without Managed Disks' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Los comandos anteriores usan la siguiente información:

- **Name**: nombre para mostrar para la asignación de directiva. En este caso, se usa la *auditoría de máquinas virtuales sin discos administrados*.
- **Definition**: definición de la directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*.
- **Scope**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos. Asegúrese de sustituir &lt;scope&gt; por el nombre del grupo de recursos.

Ahora ya está listo para identificar los recursos no compatibles para saber el estado de cumplimiento de su entorno.

## <a name="identify-non-compliant-resources"></a>Identificación de recursos no compatibles

Utilice la siguiente información para identificar los recursos que no son compatibles con la asignación de directiva que ha creado. Ejecute los comandos siguientes:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit Virtual Machines without Managed Disks' }
$policyAssignment.PolicyAssignmentId
```

Para obtener más información sobre los identificadores de asignación de directiva, vea [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

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

Las siguientes guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con otros tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no piensa continuar, puede ejecutar este comando para eliminar la asignación que creó:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'Audit Virtual Machines without Managed Disks Assignment' -Scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos incompatibles en el entorno de Azure.

Para obtener más información sobre la asignación de directivas y asegurarse de que los recursos creados en el **futuro** sean compatibles, continúe con el tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](create-manage-policy.md)