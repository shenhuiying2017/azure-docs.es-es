---
title: "Aplicación de seguridad con directivas en máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Aplicación de una directiva a una máquina virtual Windows de Azure Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 3401c0af776691d22e51906eefaf895d684fdfd1
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar directivas a máquinas virtuales con Windows con Azure Resource Manager
Mediante las directivas, una organización puede aplicar varias convenciones y reglas en toda la empresa. La aplicación del comportamiento deseado puede ayudar a reducir el riesgo a la vez que se contribuye al éxito de la organización. En este artículo, describimos cómo puede usar las directivas de Azure Resource Manager para definir el comportamiento deseado para las máquinas virtuales de su organización.

Para obtener una introducción a las directivas, vea [Uso de directivas para administrar los recursos y controlar el acceso](../../azure-resource-manager/resource-manager-policy.md).

## <a name="permitted-virtual-machines"></a>Máquinas virtuales permitidas
Para asegurarse de que las máquinas virtuales de la organización son compatibles con una aplicación, puede restringir los sistemas operativos permitidos. En el ejemplo de directiva siguiente, solo se permite que se creen máquinas virtuales de Windows Server 2012 R2 Datacenter:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Use un carácter comodín para modificar la directiva anterior para permitir cualquier imagen de Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Para obtener información sobre los campos de directiva, vea [Alias de directiva](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="managed-disks"></a>Discos administrados

Para requerir el uso de discos administrados, use la siguiente directiva:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Imágenes para las máquinas virtuales

Por motivos de seguridad, puede obligar a que solo las imágenes personalizadas aprobadas se puedan implementar en su entorno. Puede especificar el grupo de recursos que contiene las imágenes aprobadas o las imágenes aprobadas específicas.

En el ejemplo siguiente se requieren imágenes de un grupo de recursos aprobado:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

En el ejemplo siguiente se especifican los identificadores de las imágenes aprobadas:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensiones de máquina virtual

Puede que desee prohibir el uso de ciertos tipos de extensiones. Por ejemplo, una extensión puede no ser compatible con determinadas imágenes personalizadas de máquina virtual. En el ejemplo siguiente se muestra cómo bloquear una extensión específica. Utiliza el anunciante y el tipo para determinar qué extensiones se van a bloquear.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Ventaja de uso híbrido de Azure

Cuando tenga una licencia local, puede guardar la cuota de licencia en sus máquinas virtuales. Si no dispone de la licencia, debe prohibir la opción. La siguiente directiva prohíbe el uso de la ventaja de uso híbrido de Azure (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](../../azure-resource-manager/resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](../../azure-resource-manager/resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener una introducción a las directivas de recursos, consulte [Uso de directivas para administrar los recursos y controlar el acceso](../../azure-resource-manager/resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](../../azure-resource-manager/resource-manager-subscription-governance.md).

