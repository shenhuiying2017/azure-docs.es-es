---
title: Cuotas de vCPU de Azure | Microsoft Docs
description: "Obtenga información acerca de las cuotas de vCPU de Azure."
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Cuotas de vCPU de máquinas virtuales

Las cuotas de vCPU para máquinas virtuales y conjuntos de escalado de máquinas virtuales se organizan en dos niveles para cada suscripción en cada región. El primer nivel es el número de vCPU regionales totales y el segundo es el número de núcleos de varias familias de tamaños de máquina virtual, como las vCPU de la Familia D estándar. Siempre que se implemente una máquina virtual nueva, las vCPU de dicha máquina virtual no pueden exceder la cuota de vCPU para la familia de tamaños de máquina virtual específica o el total de la cuota de vCPU regional. Si se supera cualquiera de esas dos cuotas, no se permitirá la implementación de la máquina virtual. También hay una cuota para el número total de máquinas virtuales en la región. Se pueden ver los detalles de cada una de estas cuotas en la sección **Uso y cuotas** de la página **Suscripción** en [Azure Portal](https://portal.azure.com), o bien puede consultar los valores mediante la CLI de Azure.


## <a name="check-usage"></a>Comprobación del uso

Puede comprobar el uso de la cuota mediante el comando [az vm list-usage](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Instancias reservadas de máquina virtual
Las instancias reservadas de máquina virtual, cuyo ámbito es una sola suscripción, agregarán un aspecto nuevo a las cuotas de vCPU. Estos valores describen el número de instancias del tamaño indicado que deben poderse implementar en la suscripción. Actúan como un marcador de posición en el sistema de cuotas para asegurarse de que esa cuota esté reservada y que las instancias reservadas puedan implementarse en la suscripción. Por ejemplo, si una suscripción específica tiene 10 instancias reservadas Standard_D1, el límite de uso para las instancias reservadas Standard_D1 será 10. Esto hará que Azure se asegure de que siempre hay al menos 10 vCPU disponibles en el total de la cuota de vCPU regional que se utilizará para instancias de Standard_D1 y que hay al menos 10 vCPU disponibles en la cuota de vCPU de la Familia D estándar que se utilizará para las instancias de Standard_D1.

Si es necesario aumentar la cuota para adquirir una instancia reservada de suscripción única, puede [solicitar un aumento de la cuota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) en su suscripción.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre facturación y cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
