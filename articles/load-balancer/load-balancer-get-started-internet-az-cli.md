---
title: Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante la CLI de Azure | Microsoft Docs
description: Aprenda a crear un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante la CLI de Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: f3f479de8bc3975f4da07a7761ffc99f976db20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Cree un estándar de equilibrador de carga público con redundancia de zona en el front-end mediante la CLI de Azure

Este artículo le ayudará a crear un servicio [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con front-end con redundancia de zona mediante una dirección IP pública estándar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si elige instalar y usar la CLI de manera local, asegúrese de haber instalado la versión más reciente de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) y que ha iniciado sesión en una cuenta de Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 El soporte técnico para las zonas de disponibilidad está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para soporte técnico, eche un vistazo a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [Creación de una solicitud de soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el siguiente comando:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Crear una dirección IP pública estándar

Cree una dirección IP pública estándar con el siguiente comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Crear un equilibrador de carga

Cree un estándar de equilibrador de carga público con la dirección IP pública estándar que creó en el paso anterior mediante el comando siguiente:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Crear un sondeo del equilibrador de carga en el puerto 80

Cree un sondeo de estado del equilibrador de carga con el comando siguiente:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Crear una regla de equilibrador de carga para el puerto 80

Cree una regla del equilibrador de carga con el comando siguiente:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Load Balancer Estándar y zonas de disponibilidad](load-balancer-standard-availability-zones.md).



