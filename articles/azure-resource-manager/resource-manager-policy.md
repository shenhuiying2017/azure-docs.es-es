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
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: cfdbf35b76b6a7f3cddb2deb35dfc475e0fc600f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resource-policy-overview"></a>Información general sobre las directivas de recursos
Las directivas de recursos permiten establecer convenciones para los recursos de una organización. La definición de convenciones permite controlar los costes y administrar los recursos más fácilmente. Por ejemplo, puede especificar que se permitan solo determinados tipos de máquinas virtuales. O puede obligar a que todos los recursos tengan una etiqueta concreta. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

Hay que comprender dos conceptos acerca de las directivas:

* definición de la directiva: describe cuándo se aplica la directiva y qué acción realizar
* asignación de la directiva: la definición de la directiva se aplica a un ámbito (suscripción o grupo de recursos)

Este tema se centra en la definición de la directiva. Para obtener información acerca de la asignación de directivas, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos) o [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).

Las directivas se evalúan al crear y actualizar los recursos (operaciones PUT y PATCH).

## <a name="how-is-it-different-from-rbac"></a>¿En qué se diferencia de RBAC?
Hay algunas diferencias importantes entre directiva y control de acceso basado en roles (RBAC). RBAC se centra en las acciones del **usuario** en ámbitos diferentes. Por ejemplo, se agrega un usuario al rol de colaborador para un grupo de recursos en el ámbito deseado, para que pueda realizar cambios en ese grupo de recursos. La directiva se centra en las propiedades de los **recursos** durante la implementación. Por ejemplo, a través de directivas, puede controlar los tipos de recursos que se pueden aprovisionar. O puede restringir las ubicaciones en las que se pueden aprovisionar los recursos. A diferencia de RBAC, la directiva es un sistema que permite de manera predeterminada y niega explícitamente. 

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

* modo
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
    "mode": "all",
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

## <a name="mode"></a>Mode

Se recomienda que establezca `mode` en `all`. Cuando se establece en **all**, los grupos de recursos y todos los tipos de recursos se evalúan para la directiva. El portal usa **all** para todas las directivas. Si usa PowerShell o CLI de Azure, tiene que especificar el parámetro `mode`, establézcalo en **all**.
 
Anteriormente, se evalúa la directiva solo en tipos de recursos que admiten etiquetas y ubicación. El modo `indexed` sigue este comportamiento. Si usa el modo **all**, las directivas también se evalúan en los tipos de recursos que no son compatibles con etiquetas y ubicación. [Subred de red virtual](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) es un ejemplo de un tipo recién agregado. Además, los grupos de recursos se evalúan cuando el modo está establecido en **all**. Por ejemplo, puede [aplicar etiquetas en un grupo de recursos](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>parameters
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

Cuando se usa la condición **match**, proporcione `#` para representar un dígito, `?` para una letra y cualquier otro carácter para representar ese carácter en sí. Para obtener ejemplos, vea [Aplicación de directivas de recursos para nombres y texto](resource-manager-policy-naming-convention.md).

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
La directiva admite tres tipos de efectos: `deny`, `audit`, `append`, `AuditIfNotExists` y `DeployIfNotExists`. 

* **Deny** genera un evento en el registro de auditoría y se produce un error en la solicitud.
* **Audit** genera un evento de advertencia en el registro de auditoría pero no producirá un error en la solicitud.
* **Append** agrega el conjunto de campos definido a la solicitud. 
* **AuditIfNotExists**:habilitar la auditoría si un recurso no existe
* **DeployIfNotExists**: implementar un recurso si aún no existe. Actualmente, este efecto solo se admite a través de directivas integradas.

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

Con **AuditIfNotExists** y **DeployIfNotExists**, puede evaluar la existencia de un recurso secundario y aplicar una regla si no existe ese recurso. Por ejemplo, puede requerir que un monitor de red se implemente para todas las redes virtuales.

Para obtener un ejemplo de auditoría cuando no se implementa una extensión de máquina virtual, consulte [Auditoría de extensiones de máquina virtual](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

## <a name="aliases"></a>Alias

Los alias de propiedad se usan para tener acceso a propiedades específicas de un tipo de recurso. Los alias le permiten restringir los valores o condiciones que se permiten para una propiedad en un recurso. Cada alias se asigna a las rutas de acceso en las diferentes versiones de la API para un tipo de recurso determinado. Durante la evaluación de directivas, el motor de directiva obtiene la ruta de acceso de propiedad para esa versión de la API.

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
| Microsoft.Compute/imageId | Establezca el identificador de la imagen utilizada para crear la máquina virtual. |
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
| Microsoft.Compute/imageId | Establezca el identificador de la imagen utilizada para crear la máquina virtual. |
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

## <a name="policy-sets"></a>Conjuntos de directivas

Los conjuntos de directivas le permiten agrupar varias definiciones de directiva relacionadas. El conjunto de directivas simplifica la asignación y administración porque se trabaja con el grupo como un solo elemento. Por ejemplo, puede agrupar todas las directivas de etiquetado relacionadas en un conjunto de directiva único. En lugar de asignar individualmente cada directiva, puede aplicar el conjunto de directivas.
 
En el ejemplo siguiente se muestra cómo crear un conjunto de directiva para el tratamiento de dos etiquetas (costCenter y productName). Utiliza dos directivas integradas para aplicar el valor de etiqueta predeterminada y exigir el valor de etiqueta. El conjunto de directivas declara dos parámetros, costCenterValue y productNameValue para ser reutilizados. Hace referencia a las dos definiciones de directiva integrada varias veces con parámetros diferentes. Para cada parámetro, se puede proporcionar un valor fijo, como se muestra para tagName o establecer un parámetro del conjunto de directivas, como se muestra para tagValue.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

Agregue un conjunto de directivas con el comando de PowerShell **AzureRMPolicySetDefinition New**.

Para operaciones de REST, use la **versión preliminar-2017-06-01** versión de API, como se muestra en el ejemplo siguiente:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva, asígnela a un ámbito. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para ejemplos de las directivas, consulte el [repositorio de GitHub de directivas de recursos de Azure](https://github.com/Azure/azure-policy-samples).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* El esquema de la directiva está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

