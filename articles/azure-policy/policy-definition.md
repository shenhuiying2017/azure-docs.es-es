---
title: "Estructura de definición de Azure Policy | Microsoft Docs"
description: "Describe cómo Azure Policy usa la definición de directiva de recursos para establecer convenciones para los recursos de su organización al describir cuándo se aplica la directiva y qué acción realizar."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: af373e2770ad020b3a3eb669424c001670ec9204
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-policy-definition-structure"></a>Estructura de definición de Azure Policy

La definición de directiva de recursos que Azure Policy usa le permite establecer convenciones para los recursos de su organización al describir cuándo se aplica la directiva y qué acción realizar. La definición de convenciones permite controlar los costes y administrar los recursos más fácilmente. Por ejemplo, puede especificar que se permitan solo determinados tipos de máquinas virtuales. O puede obligar a que todos los recursos tengan una etiqueta concreta. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

Para crear una definición de directiva se utiliza JSON. La definición de directiva contiene elementos para:

* modo
* parameters
* nombre para mostrar
* Descripción
* regla de directiva
  * evaluación lógica
  * efecto

Por ejemplo, el siguiente JSON muestra una directiva que limita las ubicaciones donde se implementan los recursos:

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

Todos los ejemplos de plantillas de Azure Policy están en [Plantillas para Azure Policy](json-samples.md).

## <a name="mode"></a>Mode

Se recomienda establecer `mode` en `all` para hacer que una asignación de directiva evalúe todos los tipos y grupos de recursos. Puede ver un ejemplo de una definición de directiva que aplica etiquetas en un grupo de recursos en el artículo sobre cómo [autorizar la imagen de máquina virtual personalizada desde un grupo de recursos](scripts/allow-custom-vm-image.md).

Cuando se establece en **all**, los grupos de recursos y todos los tipos de recursos se evalúan para la directiva. El portal usa **all** para todas las directivas. Si usa PowerShell o CLI de Azure, tiene que especificar el parámetro `mode`, establézcalo en **all**.

Todas las definiciones de directiva que se crean con el portal usan un modo `all`, pero si desea usar PowerShell o la CLI de Azure, debe especificar el parámetro `mode` y establecerlo en `all`.

Si establece el modo en `indexed`, la asignación de directivas solo se evaluará en los tipos de recurso que admiten etiquetas y ubicaciones.


## <a name="parameters"></a>Parámetros

Los parámetros ayudan a simplificar la administración de directivas mediante la reducción del número de definiciones de directiva. Piense en los parámetros como si fueran los campos de un formulario: `name`, `address`, `city`, `state`. Estos parámetros no cambian, pero sí sus valores en función del individuo que rellena el formulario. Los parámetros funcionan del mismo modo al crear las directivas. Con la inclusión de parámetros en una definición de directiva, puede volver a usar esa directiva en distintos escenarios con valores diferentes.

Por ejemplo, podría definir una directiva de una propiedad de recurso para limitar las ubicaciones en las que se pueden implementar los recursos. En este caso, podría declarar los parámetros siguientes en el momento de crear la directiva:


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

El tipo de un parámetro puede ser cadena o matriz. La propiedad de metadatos se usa para herramientas como Azure Portal para mostrar información intuitiva.

Dentro de la propiedad de metadatos puede usar **strongType** para proporcionar una lista de opciones de selección múltiple en Azure Portal.  Los valores permitidos para **strongType** actualmente incluyen:

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

En la regla de directiva, se hace referencia a los parámetros con la sintaxis siguiente:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nombre para mostrar y descripción

Puede usar los valores **displayName** y **description** para identificar la definición de directiva y proporcionar el contexto para su uso.

## <a name="policy-rule"></a>Regla de directiva

La regla de directiva se compone de los bloques **If** y **Then**. En el bloque **If**, defina una o varias condiciones que especifican cuándo se aplica la directiva. Puede aplicar operadores lógicos a estas condiciones para definir con precisión el escenario de una directiva.

En el bloque **Then**, defina el efecto que se produce cuando se cumplen las condiciones de **If**.

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

Cuando se usa la condición **match**, proporcione `#` para representar un dígito, `?` para una letra y cualquier otro carácter para representar ese carácter en sí. Por ejemplo, consulte las [imágenes de máquina virtual aprobadas](scripts/allowed-custom-images.md).

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
La directiva admite tres tipos de efectos:

* **Deny**: genera un evento en el registro de auditoría y se produce un error en la solicitud.
* **Audit**: genera un evento de advertencia en el registro de auditoría pero no producirá un error en la solicitud.
* **Append**: agrega el conjunto de campos definido a la solicitud.
* **AuditIfNotExists**: habilita la auditoría si un recurso no existe.
* **DeployIfNotExists**: implementa un recurso si todavía no existe. Actualmente, este efecto solo se admite a través de directivas integradas.

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

Con **AuditIfNotExists** y **DeployIfNotExists**, puede evaluar la existencia de un recurso secundario y aplicar una regla y un efecto correspondiente si no existe ese recurso. Por ejemplo, puede requerir que un monitor de red se implemente para todas las redes virtuales.
Para un ejemplo de auditoría cuando no se implementa una extensión de máquina virtual, consulte el artículo sobre la [auditoría si la extensión no existe](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Alias

Los alias de propiedad se usan para tener acceso a propiedades específicas de un tipo de recurso. Los alias le permiten restringir los valores o condiciones que se permiten para una propiedad en un recurso. Cada alias se asigna a las rutas de acceso en las diferentes versiones de la API para un tipo de recurso determinado. Durante la evaluación de directivas, el motor de directiva obtiene la ruta de acceso de propiedad para esa versión de la API.

**Microsoft.Cache/Redis**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Establece si el puerto de servidor Redis que no es SSL (6379) está habilitado. |
| Microsoft.Cache/Redis/shardCount | Establezca el número de particiones que se va a crear en una caché de clúster premium.  |
| Microsoft.Cache/Redis/sku.capacity | Establezca el tamaño de la caché de Redis que implementar.  |
| Microsoft.Cache/Redis/sku.family | Establezca la familia de SKU que utilizar. |
| Microsoft.Cache/Redis/sku.name | Establezca el tipo de Redis Cache que implementar. |

**Microsoft.Cdn/profiles**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Establezca el nombre del plan de tarifa. |

**Microsoft.Compute/disks**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Establezca la oferta de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imagePublisher | Establezca el publicador de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageSku | Establezca la SKU de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |
| Microsoft.Compute/imageVersion | Establezca la versión de la imagen de plataforma o la imagen de marketplace utilizada para crear la máquina virtual. |


**Microsoft.Compute/virtualMachines**

| Alias | DESCRIPCIÓN |
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

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Establezca el nombre del publicador de la extensión. |
| Microsoft.Compute/virtualMachines/extensions/type | Establezca el tipo de extensión. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Establezca la versión de la extensión. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | DESCRIPCIÓN |
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

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Establezca el tamaño de la puerta de enlace. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Establezca el tipo de esta puerta de enlace de red virtual. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Establezca el nombre de SKU de puerta de enlace. |

**Microsoft.Sql/servers**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Establezca la versión del servidor. |

**Microsoft.Sql/databases**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Establezca la edición de la base de datos. |
| Microsoft.Sql/servers/databases/elasticPoolName | Establezca el nombre del grupo elástico en el que está la base de datos. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Establezca el Id. de objetivo de nivel de servicio configurado de la base de datos. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Establezca el nombre del objetivo de nivel de servicio configurado de la base de datos.  |

**Microsoft.Sql/elasticpools**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Establezca la DTU compartida total para el grupo elástico de base de datos. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Establezca la edición del grupo elástico. |

**Microsoft.Storage/storageAccounts**

| Alias | DESCRIPCIÓN |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Establezca el nivel de acceso usado para la facturación. |
| Microsoft.Storage/storageAccounts/accountType | Establezca el nombre de SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Establezca si el servicio cifra los datos como si estuvieran almacenados en el servicio Blob Storage. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Establezca si el servicio cifra los datos como si estuvieran almacenados en el servicio File Storage. |
| Microsoft.Storage/storageAccounts/sku.name | Establezca el nombre de SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Establezca permitir solo el tráfico https en el servicio de almacenamiento. |

## <a name="initiatives"></a>Iniciativas

Las iniciativas le permiten agrupan muchas definiciones de directivas relacionadas para simplificar las asignaciones y la administración, porque se trabaja con un grupo como un elemento único. Por ejemplo, puede agrupar todas las definiciones de directivas de etiquetado relacionadas en una sola iniciativa. En lugar de asignar individualmente cada directiva, la aplica.

En el ejemplo siguiente se muestra cómo crear una iniciativa para controlar dos etiquetas: `costCenter` y `productName`. Usa dos directivas integradas para aplicar el valor de etiqueta predeterminado.


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

## <a name="next-steps"></a>pasos siguientes

- Revise los ejemplos de plantilla de Azure Policy en [Plantillas para Azure Policy](json-samples.md).
