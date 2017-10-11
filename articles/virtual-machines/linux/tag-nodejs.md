---
title: "Procedimiento para etiquetar una máquina virtual Linux en Azure | Microsoft Docs"
description: "Infórmese sobre el etiquetado de una máquina virtual Linux de Azure creada con el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: f643001c85e127ae39e9869ffdc689bcac232ccb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Etiquetado de una máquina virtual Linux en Azure
En este artículo se describen diferentes maneras de etiquetar una máquina virtual en Azure el modelo de implementación de Resource Manager. Las etiquetas son pares clave-valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Actualmente, Azure admite un máximo de 15 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocar en un recurso en el momento de su creación, o bien se pueden agregar a un recurso existente. Tenga en cuenta que las etiquetas solo son compatibles con los recursos creados mediante el modelo de implementación de Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Etiquetado con la CLI de Azure
Para comenzar, [instale y configure la CLI de Azure](../../xplat-cli-azure-resource-manager.md) y asegúrese de que está en modo de Resource Manager (`azure config mode arm`).

Con este comando es posible ver todas las propiedades de una máquina virtual dada, incluidas las etiquetas:

        azure vm show -g MyResourceGroup -n MyTestVM

Para agregar una nueva etiqueta de máquina virtual a través de la CLI de Azure, puede usar el comando `azure vm set` junto con el parámetro de etiqueta **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para quitar todas las etiquetas, puede usar el parámetro **-T** en el comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Ahora que hemos aplicado etiquetas a nuestros recursos a través de PowerShell, la CLI de Azure y el portal, analicemos los datos de uso para ver las etiquetas del portal de facturación.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre el etiquetado de los recursos de Azure, consulte [Información general de Azure Resource Manager][Azure Resource Manager Overview] y [Uso de etiquetas para organizar los recursos de Azure][Using Tags to organize your Azure Resources].
* Para ver cómo las etiquetas pueden ayudarle a administrar el uso de los recursos de Azure, consulte [Comprender la factura de Microsoft Azure][Understanding your Azure Bill] y [Obtención de información sobre el consumo de recursos de Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
