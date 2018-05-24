---
title: Creación de directivas mediante programación y visualización de datos de cumplimiento con Azure Policy
description: Este artículo le guiará a través de la creación y administración de directivas para Azure Policy mediante programación.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 5405566b5254c553eac584acc1653449b51ddffc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195886"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Creación de directivas mediante programación y visualización de datos de cumplimiento

Este artículo le guiará a través de la creación y administración de directivas mediante programación. También describe cómo consultar los estados y las directivas de cumplimiento de recursos. Las definiciones de directivas aplican reglas y efectos diferentes sobre los recursos. Su aplicación garantiza que los recursos cumplan los estándares corporativos y los acuerdos de nivel de servicio.

## <a name="prerequisites"></a>requisitos previos

Asegúrese de que se cumplen los siguientes requisitos previos antes de empezar:

1. Si aún no lo ha hecho, instale [ARMClient](https://github.com/projectkudu/ARMClient). Esta es una herramienta que envía solicitudes HTTP a las API basadas en Azure Resource Manager.
2. Actualice el módulo AzureRM de PowerShell a la versión más reciente. Para más información acerca de la versión más reciente, consulte [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Registre el proveedor de recursos de Policy Insights con Azure PowerShell para asegurarse de que la suscripción funciona con el proveedor de recursos. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación de registro para este. Esta operación está incluida en los roles Colaborador y Propietario. Para registrar el proveedor de recursos, ejecute el siguiente comando:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para más información acerca del registro y la visualización de los proveedores de recursos, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md).
4. Si aún no lo ha hecho, instale la CLI de Azure. Puede obtener la versión más reciente en [Instalación de la CLI de Azure 2.0 en Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Creación y asignación de una definición de directiva

El primer paso hacia una mejor visibilidad de los recursos es crear y asignar directivas sobre los recursos. El siguiente paso es aprender a crear y asignar una directiva mediante programación. La directiva de ejemplo audita cuentas de almacenamiento abiertas a todas las redes públicas con PowerShell, la CLI de Azure y solicitudes HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Creación y asignación de una definición de directiva con PowerShell

1. Utilice el siguiente fragmento JSON para crear un archivo JSON con el nombre AuditStorageAccounts.json.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Para más información acerca de la creación de una definición de directiva, consulte [Estructura de definición de Azure Policy](policy-definition.md).
2. Ejecute el siguiente comando para crear una definición de directiva mediante el archivo AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  El comando crea una definición de directiva denominada _Audit Storage Accounts Open to Public Networks_ (Auditoría de cuentas de almacenamiento abiertas a las redes públicas). Para más información acerca de otros parámetros que puede utilizar, consulte [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Después de crear la definición de directiva, puede crear una asignación de directiva mediante la ejecución de los siguientes comandos:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Reemplace _ContosoRG_ por el nombre del grupo de recursos que desee.

Para más información acerca de cómo administrar las directivas de recursos mediante el módulo de PowerShell de Azure Resource Manager, consulte [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Creación y asignación de una definición de directiva con ARMClient

Use el procedimiento siguiente para crear una definición de directiva.

1. Copie el siguiente fragmento de código JSON para crear un archivo JSON. En el paso siguiente llamará al archivo.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Cree la definición de directiva con la llamada siguiente:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Reemplace el valor de &lt;subscriptionId&gt; anterior por el identificador de la suscripción prevista.

Para más información acerca de la estructura de la consulta, consulte [Definiciones de directiva: Creación o actualización](/rest/api/resources/policydefinitions/createorupdate).

Utilice el procedimiento siguiente para crear una asignación de directiva y asignar la definición de directiva en el nivel de grupo de recursos.

1. Copie el siguiente fragmento de código JSON para crear un archivo de asignación de directiva JSON. Reemplace la información de ejemplo de los símbolos &lt;&gt; por sus propios valores.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. Cree la asignación de directiva con la llamada siguiente:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Reemplace la información de ejemplo de los símbolos &lt;&gt; por sus propios valores.

  Para más información acerca de cómo realizar llamadas HTTP a la API de REST, consulte [Recursos de la API de REST de Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Creación y asignación de una definición de directiva con la CLI de Azure

Use el procedimiento siguiente para crear una definición de directiva:

1. Copie el siguiente fragmento de código JSON para crear un archivo de asignación de directiva JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. Para crear una definición de directiva, ejecute el siguiente comando:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Use el siguiente comando para crear una asignación de directiva. Reemplace la información de ejemplo de los símbolos &lt;&gt; por sus propios valores.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

Puede obtener el identificador de definición de directiva si usa PowerShell con el comando siguiente:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

El identificador de definición de directiva para la definición de directiva que ha creado debe parecerse al ejemplo siguiente:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Para más información acerca de cómo administrar las directivas de recursos con la CLI de Azure, consulte las [directivas de recursos de la CLI de Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identificación de recursos no compatibles

En una asignación, un recurso no es compatible si no cumple las reglas de iniciativa o directiva. En la tabla siguiente se muestra cómo funcionan los distintos efectos de directiva con la evaluación de condición para el estado de cumplimiento resultante:

| Estado del recurso | Efecto | Evaluación de directiva | Estado de cumplimiento |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | No compatible |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Compatible |
| Nuevo | Audit, AuditIfNotExist\* | True | No compatible |
| Nuevo | Audit, AuditIfNotExist\* | False | Compatible |

\* Los efectos Append, DeployIfNotExist y AuditIfNotExist requieren que la instrucción IF sea TRUE. Los efectos requieren también que la condición de existencia sea FALSE para ser no compatibles. Si es TRUE, la condición IF desencadena la evaluación de la condición de existencia de los recursos relacionados.

Para entender mejor cómo se marcan los recursos como no compatibles, se usará el ejemplo de asignación de directiva creado anteriormente.

Por ejemplo, suponga que tiene un grupo de recursos (ContosoRG) con varias cuentas de almacenamiento (resaltadas en rojo) expuestas a redes públicas.

![Cuentas de almacenamiento expuestas a redes públicas](media/policy-insights/resource-group01.png)

En este ejemplo, debe tener cuidado con los riesgos de seguridad. Ahora que ha creado una asignación de directiva, se evalúa para todas las cuentas de almacenamiento en el grupo de recursos ContosoRG. Se auditan las tres cuentas de almacenamiento no compatibles, con lo que sus estados cambian a **no compatible**.

![Cuentas de almacenamiento auditadas no compatibles](media/policy-insights/resource-group03.png)

Utilice el siguiente procedimiento para identificar los recursos de un grupo de recursos que no son compatibles con la asignación de directiva. En el ejemplo, los recursos son cuentas de almacenamiento del grupo de recursos ContosoRG.

1. Ejecute los comandos siguientes para obtener el identificador de asignación de directiva:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  Para más información acerca de la obtención de un identificador de asignación de directiva, consulte [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Ejecute el siguiente comando para copiar los identificadores de los recursos no compatibles en un archivo JSON:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Los resultados deben tener un aspecto similar al ejemplo siguiente:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Son equivalentes a lo que normalmente vería en **Recursos no compatibles**, en la [vista de Azure Portal](assign-policy-definition.md#identify-non-compliant-resources).

Actualmente, los recursos no compatibles solo se identifican mediante Azure Portal y solicitudes HTTP. Para más información sobre cómo consultar los estados de directiva, consulte el artículo de referencia de API de [estados de directiva](/rest/api/policy-insights/policystates).

## <a name="view-policy-events"></a>Visualización de eventos de directiva

Cuando se crea o actualiza un recurso, se genera un resultado de evaluación de directiva. Los resultados se denominan _eventos de directiva_. Ejecute la siguiente consulta para ver todos los eventos de directiva asociados a la asignación de directiva.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Los resultados deben tener una apariencia similar al ejemplo siguiente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Al igual que con los estados de directiva, solo puede ver los eventos de directiva con solicitudes HTTP. Para más información sobre cómo consultar los eventos de directiva, consulte el artículo de referencia de [estados de directiva](/rest/api/policy-insights/policyevents).

## <a name="next-steps"></a>Pasos siguientes

Revise los artículos siguientes para más información sobre los comandos y las consultas en este artículo.

- [Recursos de la API de REST de Azure](/rest/api/resources/)
- [Módulos de Azure RM PowerShell](/powershell/module/azurerm.resources/#policies)
- [Comandos de directiva de la CLI de Azure](/cli/azure/policy?view=azure-cli-latest)
- [Referencia de API de REST de proveedor de recursos de Policy Insights](/rest/api/policy-insights)