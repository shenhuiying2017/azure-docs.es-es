---
title: "Aplicación de directivas en máquinas virtuales de Azure Resource Manager | Microsoft Docs"
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
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: f06747c35f12802f54b44774dff7f92280ba0dc9
ms.openlocfilehash: b1c66c14fbf5b0361831bb987df3dc01131abc88


---
# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Aplicación de directivas en máquinas virtuales de Azure Resource Manager
Mediante las directivas, una organización puede aplicar varias convenciones y reglas en toda la empresa. La aplicación del comportamiento deseado puede ayudar a reducir el riesgo a la vez que se contribuye al éxito de la organización. En este artículo, describiremos cómo puede utilizar las directivas de Azure Resource Manager para definir el comportamiento deseado para las máquinas virtuales de su organización.

Los pasos para lograr esto se describen a continuación:

1. Directiva 101 de Azure Resource Manager
2. Definición de una directiva para la máquina Virtual
3. Creación de la directiva
4. Aplicación de la directiva

## <a name="azure-resource-manager-policy-101"></a>Directiva 101 de Azure Resource Manager
Para obtener una introducción a las directivas de Azure Resource Manager, se recomienda leer el artículo siguiente y, a continuación, continuar con los pasos de este artículo. El siguiente artículo describe la definición y la estructura básicas de una directiva, describe cómo se evalúan las directivas y proporciona varios ejemplos de definiciones de directiva.

* [Uso de directivas para administrar los recursos y controlar el acceso](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definición de una directiva para la máquina Virtual
Uno de los escenarios frecuentes para una empresa puede ser el de permitir solo a los usuarios crear máquinas virtuales desde sistemas operativos específicos que se han probado para que sean compatibles con una aplicación LOB. Mediante una directiva de Azure Resource Manager, esta tarea puede realizarse en unos pocos pasos.
En este ejemplo de directiva, vamos a permitir que se creen solo máquinas virtuales Ubuntu 14.04.2-LTS. La definición de directiva es similar a la siguiente

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

La directiva anterior se puede modificar fácilmente para un escenario en el que posiblemente permita que cualquier imagen de Ubuntu LTS se use para una implementación de máquina Virtual con el siguiente cambio

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de propiedades de la máquina virtual
La siguiente tabla describe las propiedades de la máquina Virtual que se pueden utilizar como campos en la definición de la directiva. Para más información sobre los campos de directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](../resource-manager-policy.md#fields).

| Nombre del campo | Description |
| --- | --- |
| imagePublisher |Especifica el publicador de la imagen |
| imageOffer |Especifica la oferta para el publicador de la imagen seleccionada |
| imageSku |Especifica la SKU de la oferta elegida |
| imageVersion |Especifica la versión de la imagen para la SKU elegida |

## <a name="create-the-policy"></a>Creación de la directiva
Una directiva se puede crear fácilmente mediante la API de REST directamente o los cmdlets de PowerShell. Más información sobre la [creación y la asignación de una directiva](../resource-manager-policy.md#create-and-assign-a-policy).

## <a name="apply-the-policy"></a>Aplicación de la directiva
Después de crear la directiva, debe aplicarla en un ámbito definido. El ámbito puede ser una suscripción, un grupo de recursos o incluso un recurso. Más información sobre la [creación y la asignación de una directiva](../resource-manager-policy.md#create-and-assign-a-policy).



<!--HONumber=Nov16_HO3-->


