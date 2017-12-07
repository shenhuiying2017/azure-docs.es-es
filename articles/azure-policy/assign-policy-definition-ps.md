---
title: "Uso de PowerShell para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure | Microsoft Docs"
description: "Use PowerShell para crear una asignación de Azure Policy para identificar recursos no compatibles."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6a9b7cff1341bd898b76a226ca413b8135eec408
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Creación de una asignación de directiva para identificar recursos no compatibles en el entorno de Azure usando PowerShell

El primer paso para comprender cómo funciona el cumplimiento en Azure consiste en determinar cuál es la situación de los recursos con los que ya cuenta. Este inicio rápido lo guiará por el proceso de creación de una asignación de directiva para identificar máquinas virtuales que no están usando discos administrados.

Al final de este proceso, habrá identificado correctamente qué máquinas virtuales no utilizan discos administrados y, por tanto, *no son compatibles*.


PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se explica detalladamente cómo usar PowerShell para crear una asignación de directiva para identificar recursos no compatibles en el entorno de Azure.

Esta guía requiere la versión 4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute  ```Get-Module -ListAvailable AzureRM``` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

Antes de empezar, asegúrese de tener instalada la versión más reciente de PowerShell. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.


## <a name="create-a-policy-assignment"></a>Creación de una asignación de directiva

En este tutorial rápido, se crea una asignación de directiva y se le asigna la definición de la *Auditoría de máquinas virtuales sin discos administrados*. Esta definición de directiva identificará los recursos que no son compatibles con las condiciones establecidas en la definición de directiva.

Siga los pasos siguientes para crear una nueva asignación de directiva.

Ejecute el siguiente comando para ver todas las definiciones de directiva y encontrar la que desea asignar:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure Policy viene ya con definiciones de directiva integradas para usarlas. Encontrará definiciones de directiva integradas como:

- Enforce tag and its value (Forzar una etiqueta y su valor)
- Apply tag and its value (Aplicar una etiqueta y su valor)
- Require SQL Server Version 12.0 (Requerir SQL Server 12.0)

A continuación asigne la definición cd directiva al ámbito deseado usando el cmdlet `New-AzureRmPolicyAssignment`.

Para este tutorial, proporcionamos la siguiente información para el comando:
- **Nombre** para mostrar para la asignación de directiva. En este caso, vamos a usar la Auditoría de máquinas virtuales sin discos administrados.
- **Directiva**: se trata de la definición de directiva, según la opción utilizada para crear la asignación. En este caso, es la definición de la directiva: *Auditoría de máquinas virtuales sin discos administrados*.
- Un **ámbito**: un ámbito determina en qué recursos o agrupación de recursos se aplica la asignación de directiva. Puede abarcar desde una suscripción hasta grupos de recursos. En este ejemplo, se va a asignar la definición de directiva para al grupo de recursos **FabrikamOMS**.
- **$definition**: tiene que proporcionar el identificador de recurso de la definición de directiva; en este caso, estamos usando el identificador para la definición de directiva: *Auditoría de máquinas virtuales sin discos administrados*.

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

   Si hay algún recurso existente no compatible con esta nueva asignación, aparece en la pestaña **Non-compliant resources** (Recursos no compatibles), como se mostró anteriormente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de esta colección se basan en esta guía de inicio rápido. Si tiene previsto seguir trabajando con los tutoriales siguientes, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, elimine la asignación que creó mediante la ejecución de este comando:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, se asigna una definición de directiva para identificar los recursos no compatibles en el entorno de Azure.

Para más información sobre la asignación de directivas para garantizar que los **futuros** recursos creados sean compatibles, continúe con el tutorial para:

> [!div class="nextstepaction"]
> [Crear y administrar directivas](./create-manage-policy.md)
