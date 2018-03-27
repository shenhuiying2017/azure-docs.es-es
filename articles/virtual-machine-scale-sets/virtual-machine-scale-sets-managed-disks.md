---
title: "Uso de Managed Disks con conjuntos de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Conozca cómo y por qué usar discos administrados con conjuntos de escalado de máquinas virtuales."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Conjuntos de escalado de máquinas virtuales y discos administrados

Los [conjuntos de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/) de Azure admiten máquinas virtuales con discos administrados. El uso de discos administrados con conjuntos de escalado resulta ventajoso de varias maneras, como por ejemplo:

* Ya no es necesario que cree anticipadamente cuentas de almacenamiento y las administre para almacenar los discos de SO de las máquinas virtuales del conjunto de escalado.

* Puede conectar discos de datos administrados al conjunto de escalado.

* Con discos administrados, un conjunto de escalado puede tener una capacidad tan alta como 1000 máquinas virtuales, si se basa en una imagen de plataforma, o 300 máquinas virtuales, si se basa en una imagen personalizada.

## <a name="get-started"></a>Introducción

Una manera sencilla de comenzar a trabajar con conjuntos de escalado de discos administrados es implementar uno desde Azure Portal. Para obtener más información, consulte [este artículo](./virtual-machine-scale-sets-portal-create.md). Otra manera sencilla de empezar es usar la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) para implementar un conjunto de escalado. En el ejemplo siguiente se muestra cómo crear un conjunto de escalado basado en Ubuntu con 10 máquinas virtuales, cada una con un disco de datos de 50 GB y 100 GB:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Como alternativa, también puede buscar en el [repositorio Azure Quickstart Templates de GitHub](https://github.com/Azure/azure-quickstart-templates) carpetas que contengan `vmss` para ver ejemplos pregenerados de plantillas que implementan conjuntos de escalado. Para saber qué plantillas ya están usando discos administrados, puede consultar [esta lista](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los discos administrados en general, consulte [este artículo](../virtual-machines/windows/managed-disks-overview.md).

Para ver cómo convertir una plantilla de Resource Manager para aprovisionar conjuntos de escalado con discos administrados, consulte [este artículo](./virtual-machine-scale-sets-convert-template-to-md.md). Las mismas modificaciones en las plantillas de Resource Manager se aplican también a la API de REST de Azure.

Para más información sobre el uso de discos de datos administrados con conjuntos de escalado, consulte [este artículo](./virtual-machine-scale-sets-attached-disks.md).

Para empezar a trabajar con conjuntos de escalado grandes, consulte [este artículo](./virtual-machine-scale-sets-placement-groups.md).


