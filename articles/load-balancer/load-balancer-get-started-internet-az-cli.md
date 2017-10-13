---
title: "Creación de un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante la CLI de Azure | Microsoft Docs"
description: "Aprenda a crear un estándar de equilibrador de carga público con redundancia de zona en el front-end para la dirección IP pública mediante la CLI de Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 769eb86af3e0506ddf03d1ec616d5a17b7e5f714
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Cree un estándar de equilibrador de carga público con redundancia de zona en el front-end mediante la CLI de Azure

Este artículo le ayudará a crear un servicio [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con front-end con redundancia de zona mediante una dirección IP pública estándar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registro para la versión preliminar de las zonas de disponibilidad, Load Balancer Estándar y dirección IP pública Estándar

Si decide instalar y usar la CLI en el sitio local, para este tutorial es preciso que ejecute la versión 2.0.17 o posterior de la CLI de Azure.  Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
La [SKU estándar de Load Balancer](https://aka.ms/azureloadbalancerstandard) se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use la [SKU básica de Load Balancer](load-balancer-overview.md) disponible con carácter general con sus servicios de producción. 

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obtener soporte técnico, puede consultar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [abrir un vale de soporte de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de seleccionar una zona o una opción con redundancia de zona para la dirección IP pública de front-end de Load Balancer, primero debe completar los pasos descritos en el [registro para la versión preliminar de zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview).

Asegúrese de que ha instalado la versión más reciente de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) y de que ha iniciado sesión en una cuenta de Azure con [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#login).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el siguiente comando:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Crear una dirección IP pública estándar

Cree una dirección IP pública estándar con el siguiente comando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Crear un equilibrador de carga

Cree un estándar de equilibrador de carga público con la dirección IP pública estándar que creó en el paso anterior mediante el comando siguiente:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Crear un sondeo del equilibrador de carga en el puerto 80

Cree un sondeo de estado del equilibrador de carga con el comando siguiente:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Crear una regla de equilibrador de carga para el puerto 80

Cree una regla del equilibrador de carga con el comando siguiente:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [crear una dirección IP pública en una zona de disponibilidad](../virtual-network/create-public-ip-availability-zone-cli.md).



