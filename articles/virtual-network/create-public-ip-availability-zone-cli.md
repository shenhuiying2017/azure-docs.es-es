---
title: "Creación de una dirección IP pública con zonas con la CLI de Azure | Microsoft Docs"
description: "Cree una dirección IP pública en una zona de disponibilidad con la CLI de Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Creación de una dirección IP pública en una zona de disponibilidad con la CLI de Azure

Puede implementar una dirección IP pública en una zona de disponibilidad de Azure (versión preliminar). Una zona de disponibilidad es una zona separada físicamente en una región de Azure. Obtenga información sobre cómo:

> * Creación de una dirección IP pública en una zona de disponibilidad
> * Identificación de recursos relacionados creados en la zona de disponibilidad

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute una versión de la CLI de Azure posterior a la versión 2.0.17. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para soporte técnico, eche un vistazo a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [Creación de una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Crear una dirección IP pública zonal

Cree una dirección IP pública en una zona de disponibilidad con el siguiente comando:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Cuando asigna una dirección IP pública de SKU estándar a una interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](security-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obtener información de zonas sobre una dirección IP pública

Obtenga la información de zonas de una dirección IP pública con el comando siguiente:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Más información sobre [direcciones IP públicas](../virtual-network/virtual-network-public-ip-address.md) 
