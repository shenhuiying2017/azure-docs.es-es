---
title: "Creación de un recurso Load Balancer estándar público con un front-end de dirección IP pública con redundancia de zona mediante PowerShell | Microsoft Docs"
description: "Aprenda a crear un recurso Load Balancer estándar público con un front-end de dirección IP con redundancia de zona mediante PowerShell."
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
ms.openlocfilehash: 2945e04814f3d1e5b281391485def47f4b463d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Creación de un recurso Load Balancer estándar público con un front-end de dirección IP pública con redundancia de zona mediante PowerShell

Este artículo le ayudará a crear un servicio [Load Balancer Estándar](https://aka.ms/azureloadbalancerstandard) público con front-end con redundancia de zona mediante una dirección IP pública estándar.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registro para la versión preliminar de las zonas de disponibilidad, Load Balancer Estándar y dirección IP pública Estándar

Para seguir los pasos de este artículo es necesario tener instalado el módulo AzureRM versión 4.4.0 o posterior. Para encontrar la versión, ejecute `Get-Module -ListAvailable AzureRM`. Si necesita instalar o actualizar una versión, instale la más reciente del módulo AzureRM desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
La [SKU estándar de Load Balancer](https://aka.ms/azureloadbalancerstandard) se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Use la [SKU básica de Load Balancer](load-balancer-overview.md) disponible con carácter general con sus servicios de producción. 

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obtener soporte técnico, puede consultar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [abrir un vale de soporte de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Antes de seleccionar una zona o una opción con redundancia de zona para la dirección IP pública de front-end de Load Balancer, primero debe completar los pasos descritos en el [registro para la versión preliminar de zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos con el siguiente comando:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Crear una dirección IP pública estándar 
Cree una dirección IP pública estándar con el siguiente comando:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Creación de una configuración IP front-end para el sitio web

Cree una configuración IP de front-end con el siguiente comando:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Creación del grupo de direcciones de back-end

Cree un grupo de direcciones de back-end con el siguiente comando:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Creación de un sondeo de equilibrio de carga en el puerto 80

Cree un sondeo de estado en el puerto 80 para el equilibrador de carga con el siguiente comando:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer"></a>Crear un equilibrador de carga
Cree un recurso Load Balancer estándar con el siguiente comando:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [crear una dirección IP pública en una zona de disponibilidad](../virtual-network/create-public-ip-availability-zone-portal.md).



