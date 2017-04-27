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
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6e71fd9eda822478fa0555aa44908a4094fe8de2
ms.lasthandoff: 03/31/2017


---
# <a name="apply-resource-policies-for-tags"></a>Aplicación de directivas de recursos para etiquetas

Este tema proporciona reglas de directiva comunes que se pueden aplicar para garantizar un uso coherente de las etiquetas en recursos.

La aplicación de una directiva de etiqueta a un grupo de recursos o a una suscripción con recursos existentes no aplica con carácter retroactivo la directiva a esos recursos. Para aplicar las directivas en esos recursos, desencadene una actualización en los recursos existentes, como se muestra en [Desencadenamiento de actualizaciones en recursos existentes](#trigger-updates-to-existing-resources).

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Garantizar que todos los recursos de un grupo de recursos tienen una etiqueta y un valor

Un requisito común es que todos los recursos de un grupo de recursos tengan una etiqueta y un valor determinados. Este requisito a menudo es necesario para realizar un seguimiento de los costos por departamento. Se deben cumplir las condiciones siguientes:

* La etiqueta y el valor necesarios se anexan a los recursos nuevos y actualizados que no tienen etiquetas existentes.
* La etiqueta y el valor necesarios se anexan a los recursos nuevos y actualizados que puede contener otras etiquetas, pero no la etiqueta y el valor necesarios.
* La etiqueta y el valor necesarios no se pueden quitar de los recursos existentes.

Este requisito se hace cumplir aplicando a un grupo de recursos las tres directivas siguientes:

* [Anexar la etiqueta](#append-tag) 
* [Anexar la etiqueta con otras etiquetas](#append-tag-with-other-tags)
* [Requerir la etiqueta y el valor](#require-tag-and-value)

### <a name="append-tag"></a>Anexar la etiqueta

La siguiente regla de directiva anexa la etiqueta costCenter con un valor predefinido si no hay ninguna etiqueta:

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>Anexar la etiqueta con otras etiqueta

La siguiente regla de directiva anexa la etiqueta costCenter con un valor predefinido si hay etiquetas, pero la etiqueta costCenter no está definida:

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>Requerir la etiqueta y el valor

La siguiente regla de directiva deniega la actualización o creación de recursos que no tienen la etiqueta costCenter asignada al valor predefinido.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
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

## <a name="trigger-updates-to-existing-resources"></a>Desencadenamiento de actualizaciones en recursos existentes

El siguiente script de PowerShell desencadena una actualización en los recursos existentes para aplicar directivas de etiqueta que usted ha agregado.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener una introducción a las directivas de recursos, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).


