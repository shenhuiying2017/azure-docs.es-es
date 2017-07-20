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
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: f27bc3689f228809e9db8f61485ea0c8b4b302d1
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
# <a name="resource-policy-overview"></a>Información general sobre las directivas de recursos
Las directivas de recursos permiten establecer convenciones para los recursos de una organización. La definición de convenciones permite controlar los costes y administrar los recursos más fácilmente. Por ejemplo, puede especificar que solo se permiten determinados tipos de máquinas virtuales, o puede requerir que todos los recursos tengan una etiqueta concreta. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

Hay que comprender dos conceptos acerca de las directivas:

* definición de la directiva: describe cuándo se aplica la directiva y qué acción realizar
* asignación de la directiva: la definición de la directiva se aplica a un ámbito (suscripción o grupo de recursos)

Este tema se centra en la definición de la directiva. Para obtener información acerca de la asignación de directivas, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos) o [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).

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

## <a name="built-in-policies"></a>Directivas integradas

Azure proporciona algunas definiciones de directiva predefinidas que pueden reducir el número de directivas que tiene que definir. Para continuar con las definiciones de la directiva, debe tener en cuenta si una directiva integrada ya proporciona la definición que necesita. Las definiciones de directiva integrada son:

* Ubicaciones permitidas
* Tipos de recursos permitidos
* SKU de cuenta de almacenamiento permitida
* SKU de máquina virtual permitida
* Aplicación de etiqueta y valor predeterminado
* Aplicación de etiqueta y valor
* Tipos de recursos no permitidos
* Requisito de la versión 12.0 de SQL Server
* Requisito de cifrado de la cuenta de almacenamiento

Puede asignar cualquier de estas directivas a través del [portal](resource-manager-policy-portal.md), [PowerShell](resource-manager-policy-create-assign.md#powershell) o la [CLI de Azure](resource-manager-policy-create-assign.md#azure-cli).

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

Puede anidar los operadores lógicos. El ejemplo siguiente muestra una operación **not** que está anidada dentro de una operación **allOf**. 

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
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Cuando se usa la condición **like**, puede incluir un carácter comodín (*) en el valor.

Cuando se usa la condición **match**, proporcione `#` para representar un dígito, `?` para una letra y cualquier otro carácter para representar ese carácter en sí. Para ver ejemplos, consulte [Convención de nomenclatura](#set-naming-convention).

### <a name="fields"></a>Fields
Para crear condiciones se usan campos. Un campo representa las propiedades de la carga de solicitud de recursos que se usa para describir el estado del recurso.  

Se admiten los siguientes campos:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* alias de propiedad: para obtener una lista, vea [Alias](#aliases).

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

## <a name="aliases"></a>Alias

Los alias de propiedad se usan para tener acceso a propiedades específicas de un tipo de recurso. 

**Microsoft.Cache/Redis**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Establece si el puerto de servidor Redis que no es SSL (6379) está habilitado. |
| Microsoft.Cache/Redis/shardCount | Establezca el número de particiones que se va a crear en una caché de clúster premium.  |
| Microsoft.Cache/Redis/sku.capacity | Establezca el tamaño de la caché de Redis que implementar.  |
| Microsoft.Cache/Redis/sku.family | Establezca la familia de SKU que utilizar. |
| Microsoft.Cache/Redis/sku.name | Establezca el tipo de Redis Cache que implementar. |

**Microsoft.Cdn/profiles**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Establezca el nombre del plan de tarifa. |

**Microsoft.Compute/disks**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Establezca la oferta de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imagePublisher | Establezca el publicador de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageSku | Establezca la SKU de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageVersion | Establezca la versión de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |


**Microsoft.Compute/virtualMachines**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Establezca la oferta de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imagePublisher | Establezca el publicador de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageSku | Establezca la SKU de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageVersion | Establezca la versión de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/licenseType | Establezca que la imagen o el disco cuenten con una licencia local. Este valor solo se usa para imágenes que contienen el sistema operativo Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Establezca la oferta de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/virtualMachines/imagePublisher | Establezca el publicador de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/virtualMachines/imageSku | Establezca la SKU de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/virtualMachines/imageVersion | Establezca la versión de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Establezca el URI de VHD. |
| Microsoft.Compute/virtualMachines/sku.name | Establezca el tamaño de la máquina virtual. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Establezca el nombre del publicador de la extensión. |
| Microsoft.Compute/virtualMachines/extensions/type | Establezca el tipo de extensión. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Establezca la versión de la extensión. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Establezca la oferta de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imagePublisher | Establezca el publicador de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageSku | Establezca la SKU de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageVersion | Establezca la versión de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/licenseType | Establezca que la imagen o el disco cuenten con una licencia local. Este valor solo se usa para imágenes que contienen el sistema operativo Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Establezca el prefijo del nombre de equipo para todas las máquinas virtuales en el conjunto de escalado. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Establece el URI del blob para la imagen de usuario. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Establezca las direcciones URL de contenedor que se utilizan para almacenar los discos del sistema operativo para el conjunto de escalado. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Establezca el tamaño de las máquinas virtuales en un conjunto de escalado. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Establezca el nivel las máquinas virtuales en un conjunto de escalado. |
  
**Microsoft.Network/applicationGateways**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Establezca el tamaño de la puerta de enlace. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Establezca el tipo de esta puerta de enlace de red virtual. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Establezca el nombre de SKU de puerta de enlace. |

**Microsoft.Sql/servers**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Establezca la versión del servidor. |

**Microsoft.Sql/databases**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Establezca la edición de la base de datos. |
| Microsoft.Sql/servers/databases/elasticPoolName | Establezca el nombre del grupo elástico en el que está la base de datos. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Establezca el Id. de objetivo de nivel de servicio configurado de la base de datos. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Establezca el nombre del objetivo de nivel de servicio configurado de la base de datos.  |

**Microsoft.Sql/elasticpools**

| Alias | Descripción |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Establezca la DTU compartida total para el grupo elástico de base de datos. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Establezca la edición del grupo elástico. |

**Microsoft.Storage/storageAccounts**

| Alias | Descripción |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Establezca el nivel de acceso usado para la facturación. |
| Microsoft.Storage/storageAccounts/accountType | Establezca el nombre de SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Establezca si el servicio cifra los datos como si estuvieran almacenados en el servicio Blob Storage. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Establezca si el servicio cifra los datos como si estuvieran almacenados en el servicio File Storage. |
| Microsoft.Storage/storageAccounts/sku.name | Establezca el nombre de SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Establezca permitir solo el tráfico https en el servicio de almacenamiento. |


## <a name="policy-examples"></a>Ejemplos de directivas

Los siguientes temas contienen ejemplos de directivas:

* Para ver ejemplos de directivas de etiqueta, consulte [Apply resource policies for tags](resource-manager-policy-tags.md) (Aplicación de directivas de recursos para etiquetas).
* Para obtener ejemplos de patrones de texto y nomenclatura, vea [Aplicación de directivas de recursos para nombres y texto](resource-manager-policy-naming-convention.md).
* Para ver ejemplos de directivas de almacenamiento, consulte [Aplicación de directivas de recursos a cuentas de almacenamiento](resource-manager-policy-storage.md).
* Para ver ejemplos de directivas de máquina virtual, consulte [Aplicación de directivas de recursos a máquinas virtuales Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) y [Aplicación de directivas de recursos a máquinas virtuales Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json).


## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva, asígnela a un ámbito. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* El esquema de la directiva está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


