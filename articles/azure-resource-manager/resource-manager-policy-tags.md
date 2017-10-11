---
title: Directivas de recursos de Azure para etiquetas | Microsoft Docs
description: Proporciona ejemplos de directivas de recursos para administrar etiquetas en recursos
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
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="apply-resource-policies-for-tags"></a>Aplicación de directivas de recursos para etiquetas

Este tema proporciona reglas de directiva comunes que se pueden aplicar para garantizar un uso coherente de las etiquetas en recursos.

La aplicación de una directiva de etiqueta a un grupo de recursos o a una suscripción con recursos existentes no aplica con carácter retroactivo la directiva a esos recursos. Para aplicar las directivas en esos recursos, desencadene una actualización en los recursos existentes. En este artículo se incluye un ejemplo de PowerShell para desencadenar una actualización.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Garantizar que todos los recursos de un grupo de recursos tienen una etiqueta y un valor

Un requisito común es que todos los recursos de un grupo de recursos tengan una etiqueta y un valor determinados. Este requisito a menudo es necesario para realizar un seguimiento de los costos por departamento. Se deben cumplir las condiciones siguientes:

* La etiqueta y el valor obligatorios se anexan a los recursos nuevos y actualizados que no tienen la etiqueta.
* La etiqueta y el valor necesarios no se pueden quitar de los recursos existentes.

Este requisito se cumple aplicando dos directivas integradas a un grupo de recursos.

| ID | Descripción |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Aplica una etiqueta obligatoria y su valor predeterminado cuando el usuario no la especifica. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Aplica una etiqueta obligatoria y su valor. |

### <a name="powershell"></a>PowerShell

En el siguiente script de PowerShell, se asignan las dos definiciones de directivas integradas a un grupo de recursos. Antes de ejecutar el script, asigne todas las etiquetas obligatorias al grupo de recursos. Se necesita cada etiqueta en el grupo de recursos para los recursos del grupo. Para asignarlas a todos los grupos de recursos en su suscripción, no proporcione el parámetro `-Name` cuando obtenga los grupos de recursos.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

Después de asignar las directivas, puede desencadenar una actualización a todos los recursos existentes para aplicar las directivas de etiqueta que ha agregado. En el siguiente script se conservan las demás etiquetas que existían en los recursos:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>Requerir etiquetas para un tipo de recurso
En el ejemplo siguiente se muestra cómo anidar operadores lógicos para requerir una etiqueta de aplicación solo para un tipo de recurso especificado (en este caso, cuentas de almacenamiento).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Requerir etiqueta
La siguiente directiva deniega todas las solicitudes que no tienen una etiqueta que contenga la clave "costCenter" (se puede aplicar cualquier valor):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener una introducción a las directivas de recursos, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

