---
title: Directivas de recursos de Azure | Microsoft Docs
description: "Describe cómo usar las directivas de Azure Resource Manager para asegurarse de que se establecen propiedades de recursos coherentes durante la implementación. Las directivas pueden aplicarse a la suscripción o a los grupos de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d75088bd83b0b70c889388c95331bb56fe9ba15b
ms.lasthandoff: 04/03/2017


---
# <a name="resource-policy-overview"></a>Información general sobre las directivas de recursos
Las directivas de recursos permiten establecer convenciones para los recursos de una organización. La definición de convenciones permite controlar los costes y administrar los recursos más fácilmente. Por ejemplo, puede especificar que solo se permiten determinados tipos de máquinas virtuales, o puede requerir que todos los recursos tengan una etiqueta concreta. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

Hay que comprender dos conceptos acerca de las directivas:

* definición de la directiva: describe cuándo se aplica la directiva y qué acción realizar
* asignación de la directiva: la definición de la directiva se aplica a un ámbito (suscripción o grupo de recursos)

Este tema se centra en la definición de la directiva. Para obtener información acerca de la asignación de directivas, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos) o [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).

Azure proporciona algunas definiciones de directiva predefinidas que pueden reducir el número de directivas que tiene que definir. Si una definición de directiva integrada funciona para su escenario, úsela para la asignación a un ámbito.

Las directivas se evalúan al crear y actualizar los recursos (operaciones PUT y PATCH).

> [!NOTE]
> Actualmente, la directiva no evalúa los tipos de recursos que no se admiten etiquetas, variante y ubicación, como el tipo de recurso Microsoft.Resources/deployments. Esta compatibilidad se agregará en el futuro. Para evitar problemas de compatibilidad con versiones anteriores, debe especificar explícitamente el tipo al crear directivas. Por ejemplo, una directiva de etiqueta que no especifique tipos se aplicará a todos los tipos. En ese caso, una implementación de plantilla puede dar error si hay un recurso anidado que no admite etiquetas y el tipo de recurso de implementación se ha agregado a la evaluación de directivas. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>¿En qué se diferencia de RBAC?
Hay algunas diferencias importantes entre directiva y control de acceso basado en roles (RBAC). RBAC se centra en las acciones del **usuario** en ámbitos diferentes. Por ejemplo, se agrega un usuario al rol de colaborador para un grupo de recursos en el ámbito deseado, para que pueda realizar cambios en ese grupo de recursos. La directiva se centra en las propiedades de los **recursos** durante la implementación. Por ejemplo, mediante directivas, puede controlar los tipos de recursos que se pueden aprovisionar o restringir las ubicaciones en las que se pueden aprovisionar los recursos. A diferencia de RBAC, la directiva es un sistema que permite de manera predeterminada y niega explícitamente. 

Para usar las directivas, debe autenticarse a través de RBAC. En concreto, la cuenta necesita:

* El permiso `Microsoft.Authorization/policydefinitions/write` para definir una directiva.
* El permiso `Microsoft.Authorization/policyassignments/write` para asignar una directiva. 

Estos permisos no se incluyen en el rol **Colaborador**.

## <a name="policy-definition-structure"></a>Estructura de definición de directiva
Para crear una definición de directiva se utiliza JSON. La definición de directiva contiene elementos para:

* parameters
* nombre para mostrar
* description
* regla de directiva
  * evaluación lógica
  * efecto

En el ejemplo siguiente se muestra una directiva que limita las ubicaciones donde se implementan los recursos:

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

## <a name="parameters"></a>Parámetros
Al usar parámetros, podrá simplificar la administración de directivas reduciendo el número de definiciones de directiva. Definimos una directiva para una propiedad de recurso (por ejemplo, para limitar las ubicaciones donde se pueden implementar los recursos) e incluimos parámetros en la definición. A continuación, reutilizamos esa definición de directiva en diferentes escenarios y establecemos valores diferentes (por ejemplo, especificamos un conjunto de ubicaciones para una suscripción) al asignar la directiva.

Declarará parámetros al crear las definiciones de directiva.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

El tipo de un parámetro puede ser cadena o matriz. La propiedad de metadatos se utiliza para herramientas como Azure Portal para mostrar información intuitiva. 

En la regla de directiva, se hace referencia a los parámetros con la sintaxis siguiente: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nombre para mostrar y descripción

Use los valores de **displayName** y **description** para identificar la definición de directiva y proporcionar el contexto para su uso.

## <a name="policy-rule"></a>Regla de directiva

La regla de directiva se compone de los bloques **If** y **Then**. En el bloque **If**, defina una o varias condiciones que especifican cuándo se aplica la directiva. Puede aplicar operadores lógicos a estas condiciones para definir con precisión el escenario de una directiva. En el bloque **Then**, defina el efecto que se produce cuando se cumplen las condiciones de **If**.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Operadores lógicos
Los operadores lógicos admitidos son:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

La sintaxis **not** invierte el resultado de la condición. La sintaxis **allOf** (similar a la operación lógica **And**) requiere que se cumplan todas las condiciones. La sintaxis **anyOf** (similar a la operación lógica **Or**) requiere que se cumplan una o varias condiciones.

Puede anidar los operadores lógicos. El ejemplo siguiente muestra una operación **Not** que está anidada dentro de una operación **And**. 

```json
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
```

### <a name="conditions"></a>Condiciones
Una condición evalúa si un **campo** cumple determinados criterios. Estas son las condiciones que se admiten:

* `"equals": "value"`
* `"like": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Cuando se usa la condición **like**, puede incluir un carácter comodín (*) en el valor.

### <a name="fields"></a>Fields
Para crear condiciones se usan campos. Un campo representa las propiedades de la carga de solicitud de recursos que se usa para describir el estado del recurso.  

Se admiten los siguientes campos:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* alias de propiedad

Los alias de propiedad se usan para tener acceso a propiedades específicas de un tipo de recurso. Los alias admitidos son:

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>Efecto
La directiva admite tres tipos de efectos: `deny`, `audit` y `append`. 

* **Deny** genera un evento en el registro de auditoría y se produce un error en la solicitud.
* **Audit** genera un evento de advertencia en el registro de auditoría pero no producirá un error en la solicitud.
* **Append** agrega el conjunto de campos definido a la solicitud. 

En el caso de **append**, debe proporcionar los detalles tal y como se muestra a continuación:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

El valor puede ser una cadena o un objeto con formato JSON. 

## <a name="policy-examples"></a>Ejemplos de directivas

Los siguientes temas contienen ejemplos de directivas:

* Para ver ejemplos de directivas de etiqueta, consulte [Apply resource policies for tags](resource-manager-policy-tags.md) (Aplicación de directivas de recursos para etiquetas).
* Para ver ejemplos de directivas de almacenamiento, consulte [Aplicación de directivas de recursos a cuentas de almacenamiento](resource-manager-policy-storage.md).
* Para ver ejemplos de directivas de máquina virtual, consulte [Aplicación de directivas de recursos a máquinas virtuales Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) y [Aplicación de directivas de recursos a máquinas virtuales Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json).

### <a name="allowed-resource-locations"></a>Ubicaciones de recursos permitidas
Para especificar qué ubicaciones se permiten, vea el ejemplo de la sección [Estructura de la definición de directiva](#policy-definition-structure). Para asignar esta definición de directiva, use la directiva integrada con el identificador de recurso `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c`.

### <a name="not-allowed-resource-locations"></a>Ubicaciones de recursos no permitidas
Para especificar qué ubicaciones no se permiten, use la siguiente definición de directiva:

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>Tipos de recursos permitidos
En el siguiente ejemplo se muestra una directiva que solo permite implementaciones para los tipos de recurso Microsoft.Resources, Microsoft.Compute, Microsoft.Storage y Microsoft.Network. Todos los demás se deniegan:

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>Convención de nomenclatura
En el ejemplo siguiente se muestra el uso de caracteres comodín que admite la condición **like**. La condición indica que se denegará la solicitud si el nombre coincide con el patrón indicado (namePrefix\*nameSuffix):

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva, asígnela a un ámbito. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* El esquema de la directiva está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


