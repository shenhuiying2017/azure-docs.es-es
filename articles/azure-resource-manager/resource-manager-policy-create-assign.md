---
title: "Asignación y administración de directivas de recursos de Azure | Microsoft Docs"
description: "Describe cómo aplicar directivas de recursos de Azure a las suscripciones y a los grupos de recursos y cómo ver directivas de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: f461efbc2a23f85e8b6d3fdec156a0df1636708a
ms.contentlocale: es-es
ms.lasthandoff: 07/27/2017

---
# <a name="assign-and-manage-resource-policies"></a>Asignación y administración de directivas de recursos

Para implementar una directiva, debe realizar estos pasos:

1. Compruebe las definiciones de directiva (incluidas las directivas integradas proporcionadas por Azure) para ver si ya existe una en la suscripción que cumpla sus requisitos.
2. Si existe una, obtenga el nombre.
3. Si no existe, defina la regla de directiva con JSON y agréguela como una definición de directiva en su suscripción. Este paso hace que la directiva esté disponible para la asignación, pero no aplica las reglas a la suscripción.
4. En cada caso, asigne la directiva a un ámbito (por ejemplo, una suscripción o un grupo de recursos). Ahora se exige el cumplimiento de las reglas de la directiva.

Este artículo se centra en los pasos necesarios para crear una definición de directiva y asignarla a un ámbito mediante la API de REST, PowerShell o la CLI de Azure. Si prefiere usar el portal para asignar directivas, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). El artículo no se centra en la sintaxis para crear la definición de la directiva. Para información sobre la sintaxis de directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

## <a name="rest-api"></a>API de REST

### <a name="create-policy-definition"></a>Creación de definición de directiva

Puede crear una directiva con la [API de REST para definiciones de directiva](/rest/api/resources/policydefinitions). La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.

Para crear una definición de directiva, ejecute:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluya un cuerpo de solicitud similar al ejemplo siguiente:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Asignación de directiva

Puede aplicar la definición de la directiva en el ámbito deseado a través de la [API de REST para asignaciones de directivas](/rest/api/resources/policyassignments). La API de REST permite crear y eliminar asignaciones de directiva, así como recuperar información sobre las asignaciones existentes.

Para crear una nueva asignación de directiva, ejecute lo siguiente:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} es el nombre de la asignación de directiva.

Incluya un cuerpo de solicitud similar al ejemplo siguiente:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Visualización de directiva
Para obtener una directiva, use la operación [Obtener definición de directiva](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Obtención de alias
Puede recuperar alias a través de la API de REST:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

En el ejemplo siguiente se muestra una definición de un alias. Como puede ver, un alias define rutas de acceso en distintas versiones de API, aunque se cambie el nombre de la propiedad. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

Antes de continuar con los ejemplos de PowerShell, asegúrese de que tiene [instalada la última versión](/powershell/azure/install-azurerm-ps) de Azure PowerShell. Se agregaron parámetros de directiva en la versión 3.6.0. Si tiene una versión anterior, los ejemplos devuelven un error que indica que no se encuentra el parámetro.

### <a name="view-policy-definitions"></a>Visualización de definiciones de directiva
Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```powershell
Get-AzureRmPolicyDefinition
```

Devuelve todas las definiciones de directiva disponibles, incluidas las directivas integradas. Cada directiva se devuelve con el formato siguiente:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Antes de continuar con la creación de la definición de la directiva, observe las directivas integradas. Si encuentra una directiva integrada que se aplica a los límites que necesita, puede omitir la creación de una definición de directiva. En su lugar, asigne la directiva integrada al ámbito deseado.

### <a name="create-policy-definition"></a>Creación de definición de directiva
Puede crear una definición de directiva con el cmdlet `New-AzureRmPolicyDefinition`.

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

La salida se almacena en un objeto `$definition`, que se usa durante la asignación de directivas. 

En vez de especificar JSON como un parámetro, puede proporcionar la ruta de acceso a un archivo .json que contiene la regla de directiva.

```powershell
$definition = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy "c:\policies\coolAccessTier.json"
```

En el ejemplo siguiente se crea una definición de directiva que incluye parámetros:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Asignación de directiva

Aplique la directiva al ámbito deseado mediante el cmdlet `New-AzureRmPolicyAssignment`. En el ejemplo siguiente se asigna la directiva a un grupo de recursos.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Para asignar una directiva que requiera parámetros, realice la creación y establezca el objeto con esos valores. En el ejemplo siguiente se recupera una directiva integrada y se pasan los valores de parámetros:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Visualización de la asignación de directiva

Para obtener una asignación de directiva específica, use:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Para ver la regla de directiva de una definición de directiva, use:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Eliminación de la asignación de directiva 

Para quitar una asignación de directiva, use lo siguiente:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>CLI de Azure

### <a name="view-policy-definitions"></a>Visualización de definiciones de directiva
Para ver todas las definiciones de directiva en su suscripción, utilice el siguiente comando:

```azurecli
az policy definition list
```

Devuelve todas las definiciones de directiva disponibles, incluidas las directivas integradas. Cada directiva se devuelve con el formato siguiente:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Antes de continuar con la creación de la definición de la directiva, observe las directivas integradas. Si encuentra una directiva integrada que se aplica a los límites que necesita, puede omitir la creación de una definición de directiva. En su lugar, asigne la directiva integrada al ámbito deseado.

### <a name="create-policy-definition"></a>Creación de definición de directiva

Puede crear una definición de directiva mediante la CLI de Azure con el comando de definición de directiva.

```azurecli
az policy definition create --name coolAccessTier --description "Policy to specify access tier." --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Asignación de directiva

Puede aplicar la directiva en el ámbito que quiera mediante el comando de asignación de directiva. En el ejemplo siguiente se asigna la directiva a un grupo de recursos.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Visualización de la asignación de directiva

Para ver una asignación de directiva, proporcione el nombre de la asignación de directiva y el ámbito:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Eliminación de la asignación de directiva 

Para quitar una asignación de directiva, use lo siguiente:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).


