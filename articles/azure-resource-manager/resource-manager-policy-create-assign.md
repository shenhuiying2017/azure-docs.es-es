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
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5560b22f3f92a8e0a7cb8b973ef2e4c66bc32c06
ms.lasthandoff: 03/16/2017


---
# <a name="assign-and-manage-resource-policies"></a>Asignación y administración de directivas de recursos

Para implementar una directiva, debe realizar tres pasos:

1. Definir la regla de directiva con JSON.
2. Crear una definición de directiva en su suscripción a partir del código JSON que creó en el paso anterior. Este paso hace que la directiva esté disponible para la asignación, pero no aplica las reglas a la suscripción.
3. Asigne la directiva a un ámbito (por ejemplo, una suscripción o un grupo de recursos). Ahora se exige el cumplimiento de las reglas de la directiva.

Azure proporciona algunas directivas predefinidas que pueden reducir el número de directivas que tiene que definir. Si una directiva definida previamente funciona para su escenario, omita los dos primeros pasos y asigne dicha directiva a un ámbito.

Este tema se centra en los pasos para crear una definición de directiva y asignar esa definición a un ámbito. No se centra en la sintaxis para crear la definición de la directiva. Para información sobre la sintaxis de directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

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

### <a name="create-policy-definition"></a>Creación de definición de directiva
Puede crear una definición de directiva con el cmdlet `New-AzureRmPolicyDefinition`. En los ejemplos siguientes se crea una definición de directiva para permitir recursos solo en Europa del Norte y Europa Occidental.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{
   "if": {
     "not": {
       "field": "location",
       "in": "[parameters(''allowedLocations'')]"
     }
   },
   "then": {
     "effect": "deny"
   }
 }' -Parameter '{
     "allowedLocations": {
       "type": "array",
       "metadata": {
         "description": "An array of permitted locations for resources.",
         "strongType": "location",
         "displayName": "List of locations"
       }
     }
 }'
```            

La salida se almacena en un objeto `$policy`, que se usa durante la asignación de directivas. 

En vez de especificar JSON como un parámetro, puede proporcionar la ruta de acceso a un archivo .json que contiene la regla de directiva.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>Asignación de directiva

Aplique la directiva al ámbito que desee mediante el cmdlet `New-AzureRmPolicyAssignment`:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$array = @("West US", "West US 2")
$param = @{"allowedLocations"=$array}
New-AzureRMPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policies"></a>Visualización de directivas

Para obtener todas las asignaciones de directivas, use:

```powershell
Get-AzureRmPolicyAssignment
```

Para obtener una directiva específica, use:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId
```

Para ver la regla de directiva de una definición de directiva, use:

```powershell
(Get-AzureRmPolicyDefinition -Name regionPolicyDefinition).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Eliminación de la asignación de directiva 

Para quitar una asignación de directiva, use lo siguiente:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>CLI de Azure 2.0

### <a name="create-policy-definition"></a>Creación de definición de directiva

Puede crear una definición de directiva mediante la CLI de Azure 2.0 con el comando de definición de directiva. En los ejemplos siguientes se crea una directiva para permitir los recursos solo en Europa del Norte y Europa occidental.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>Asignación de directiva

Puede aplicar la directiva en el ámbito que quiera mediante el comando de asignación de directiva:

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>Visualización de definición de directiva
Para obtener una definición de directiva, use el siguiente comando:

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>Eliminación de la asignación de directiva 

Para quitar una asignación de directiva, use lo siguiente:

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>CLI de Azure 1.0

### <a name="create-policy-definition"></a>Creación de definición de directiva

Puede crear una definición de directiva mediante la CLI de Azure con el comando de definición de directiva. En los ejemplos siguientes se crea una directiva para permitir los recursos solo en Europa del Norte y Europa occidental.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

Se puede proporcionar la ruta de acceso a un archivo .json que contenga la directiva, en lugar de especificarla en línea.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>Asignación de directiva

Puede aplicar la directiva en el ámbito que quiera mediante el comando de asignación de directiva:

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

En este caso, el ámbito es el nombre del grupo de recursos que especifique. Si no se conoce el valor del parámetro policy-definition-id, se puede obtener con la CLI de Azure. 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>Visualización de directiva
Para obtener una directiva, use el siguiente comando:

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>Eliminación de la asignación de directiva 

Para quitar una asignación de directiva, use lo siguiente:

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).


