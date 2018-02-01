---
title: "Uso de PowerShell para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure | Microsoft Docs"
description: "Use PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure usando PowerShell

El primer paso para entender el cumplimiento en Azure es identificar el estado de sus recursos. Este inicio rápido lo guiará por el proceso de creación de una asignación de directiva para identificar máquinas virtuales que no están usando discos administrados.

Al finalizar este proceso, habrá identificado correctamente máquinas virtuales que no utilizan discos administrados. No *son compatibles* con la asignación de directiva.

PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se explica detalladamente cómo usar PowerShell para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure.

Esta guía requiere la versión 4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute  `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

Antes de empezar, asegúrese de tener instalada la versión más reciente de PowerShell. Consulte el artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para información detallada.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.


## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En esta guía de inicio rápido se crea una asignación de directiva y se le asigna la definición de *Auditoría de máquinas virtuales sin discos administrados*. Esta definición de directiva identifica los recursos que no son compatibles con las condiciones establecidas en la definición de directiva.

Siga los pasos siguientes para crear una nueva asignación de directiva.

1. Para garantizar que la suscripción funciona con él, registre el proveedor de recursos de Policy Insights. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación de registro para este. Esta operación está incluida en los roles Colaborador y Propietario.

    Para registrar el proveedor de recursos, ejecute el siguiente comando:

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    No se puede anular el registro de un proveedor de recursos si dispone de tipos de recursos de ese proveedor de recursos en la suscripción.

    Para más detalles sobre el registro y la visualización de los proveedores de recursos, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md).

2. Después de registrar el proveedor de recursos, ejecute el siguiente comando para ver todas las definiciones de directiva y encontrar la que desea asignar:

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    Azure Policy viene ya con definiciones de directiva integradas para usarlas. Encontrará definiciones de directiva integradas como:

    - Enforce tag and its value (Forzar una etiqueta y su valor)
    - Apply tag and its value (Aplicar una etiqueta y su valor)
    - Require SQL Server Version 12.0 (Requerir SQL Server 12.0)

3. A continuación asigne la definición cd directiva al ámbito deseado usando el cmdlet `New-AzureRmPolicyAssignment`.

Para este tutorial se usa la siguiente información para el comando:

- **Nombre** para mostrar para la asignación de directiva. En este caso se usa la Auditoría de máquinas virtuales sin discos administrados.
- **Directiva**: definición de la directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*.
- Un **ámbito**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos. En este ejemplo se va a asignar la definición de directiva al grupo de recursos **FabrikamOMS**.
- **$definition**: tiene que proporcionar el identificador de recurso de la definición de directiva; en este caso, el identificador para la definición de directiva *Auditoría de máquinas virtuales sin discos administrados*.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Ahora ya está listo para identificar los recursos no compatibles para saber el estado de cumplimiento de su entorno.

## <a name="identify-non-compliant-resources"></a>Identificación de recursos no compatibles

1. Vuelva a la página de aterrizaje de Azure Policy.
2. Seleccione **Compliance** (Cumplimiento) en el panel izquierdo y busque la **Policy Assignment** (Asignación de directiva) que creó.

   ![Cumplimiento de directivas](media/assign-policy-definition/policy-compliance.png)

   Si hay algún recurso existente incompatible con esta nueva asignación, aparecerá en la pestaña **Recursos no compatibles**.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, elimine la asignación que creó mediante la ejecución de este comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos incompatibles en el entorno de Azure.

Para más información sobre la asignación de directivas para garantizar que los **futuros** recursos creados sean compatibles, continúe con el tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](./create-manage-policy.md)
