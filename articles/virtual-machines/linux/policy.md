---
title: "Aplicación de seguridad con directivas en máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Aplicación de una directiva a una máquina virtual Linux de Azure Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: singhkay
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c1ad80a56627695f3594f4d9b60cd623fa9bcce3
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Aplicación de directivas a máquinas virtuales con Linux con Azure Resource Manager
Mediante las directivas, una organización puede aplicar varias convenciones y reglas en toda la empresa. La aplicación del comportamiento deseado puede ayudar a reducir el riesgo a la vez que se contribuye al éxito de la organización. En este artículo, describimos cómo puede usar las directivas de Azure Resource Manager para definir el comportamiento deseado para las máquinas virtuales de su organización.

Para obtener una introducción a las directivas, vea [Uso de directivas para administrar los recursos y controlar el acceso](../../azure-resource-manager/resource-manager-policy.md).

## <a name="define-policy-for-permitted-virtual-machines"></a>Definición de directivas para las máquinas virtuales permitidas
Para asegurarse de que las máquinas virtuales de la organización son compatibles con una aplicación, puede restringir los sistemas operativos permitidos. En este ejemplo de directiva siguiente, permitirá que se creen solo máquinas virtuales Ubuntu 14.04.2-LTS.

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
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
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

Use un carácter comodín para modificar la directiva anterior para permitir cualquier imagen de Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Para obtener información sobre los campos de directiva, vea [Alias de directiva](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="define-policy-for-using-managed-disks"></a>Definición de directivas para el uso de discos administrados

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

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](../../azure-resource-manager/resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](../../azure-resource-manager/resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts).
* Para obtener una introducción a las directivas de recursos, consulte [Uso de directivas para administrar los recursos y controlar el acceso](../../azure-resource-manager/resource-manager-policy.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](../../azure-resource-manager/resource-manager-subscription-governance.md).

