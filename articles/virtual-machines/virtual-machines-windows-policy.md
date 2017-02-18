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
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: 83470aaf607a666a63bae5b9897d0b273e327377


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicación de seguridad y directivas a máquinas virtuales Windows con Azure Resource Manager
Mediante las directivas, una organización puede aplicar varias convenciones y reglas en toda la empresa. La aplicación del comportamiento deseado puede ayudar a reducir el riesgo a la vez que se contribuye al éxito de la organización. En este artículo, describiremos cómo puede utilizar las directivas de Azure Resource Manager para definir el comportamiento deseado para las máquinas virtuales de su organización.

Los pasos para lograr esto se describen a continuación:

1. Directiva 101 de Azure Resource Manager
2. Definición de una directiva para la máquina Virtual
3. Creación de la directiva
4. Aplicación de la directiva

## <a name="azure-resource-manager-policy-101"></a>Directiva 101 de Azure Resource Manager
Para obtener una introducción a las directivas de Azure Resource Manager, se recomienda leer el artículo siguiente y, a continuación, continuar con los pasos de este artículo. El siguiente artículo describe la definición y la estructura básicas de una directiva, describe cómo se evalúan las directivas y proporciona varios ejemplos de definiciones de directiva.

* [Uso de directivas para administrar los recursos y controlar el acceso](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definición de una directiva para la máquina Virtual
Uno de los escenarios frecuentes para una empresa puede ser el de permitir solo a los usuarios crear máquinas virtuales desde sistemas operativos específicos que se han probado para que sean compatibles con una aplicación LOB. Mediante una directiva de Azure Resource Manager, esta tarea puede realizarse en unos pocos pasos.
En este ejemplo de directiva, vamos a permitir que se creen solo máquinas virtuales del Centro de datos de Windows Server 2012 R2. La definición de directiva es similar a la siguiente

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
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
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

La directiva anterior se puede modificar fácilmente para un escenario en el que posiblemente quiera permitir que cualquier imagen del Centro de datos de Windows Server se use para una implementación de máquina virtual con el siguiente cambio

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de propiedades de la máquina virtual
La siguiente tabla describe las propiedades de la máquina Virtual que se pueden utilizar como campos en la definición de la directiva. Para obtener más información sobre los campos de la directiva, consulte el artículo siguiente:

* [Campos y orígenes](../azure-resource-manager/resource-manager-policy.md#conditions)

| Nombre del campo | Description |
| --- | --- |
| imagePublisher |Especifica el publicador de la imagen |
| imageOffer |Especifica la oferta para el publicador de la imagen seleccionada |
| imageSku |Especifica la SKU de la oferta elegida |
| imageVersion |Especifica la versión de la imagen para la SKU elegida |

## <a name="create-the-policy"></a>Creación de la directiva
Una directiva se puede crear fácilmente mediante la API de REST directamente o los cmdlets de PowerShell. Para crear la directiva, consulte el artículo siguiente:

* [Creación de una directiva](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)

## <a name="apply-the-policy"></a>Aplicación de la directiva
Después de crear la directiva, debe aplicarla en un ámbito definido. El ámbito puede ser una suscripción, un grupo de recursos o incluso un recurso. Para aplicar la directiva, consulte el artículo siguiente:

* [Creación de una directiva](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)



<!--HONumber=Jan17_HO4-->


