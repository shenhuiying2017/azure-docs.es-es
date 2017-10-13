---
title: "Creación de una dirección IP pública con zonas mediante PowerShell | Microsoft Docs"
description: "Cree una dirección IP pública en una zona de disponibilidad con PowerShell."
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
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Creación de una dirección IP pública en una zona de disponibilidad con PowerShell

Puede implementar una dirección IP pública en una zona de disponibilidad de Azure (versión preliminar). Una zona de disponibilidad es una zona separada físicamente en una región de Azure. Obtenga información sobre cómo:

> * Creación de una dirección IP pública en una zona de disponibilidad
> * Identificación de recursos relacionados creados en la zona de disponibilidad
  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para seguir los pasos de este artículo es necesario tener instalado el módulo AzureRM versión 4.4.0 o posterior. Para encontrar la versión, ejecute `Get-Module -ListAvailable AzureRM`. Si necesita instalar o actualizar una versión, instale la más reciente del módulo AzureRM desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Las zonas de disponibilidad están en versión preliminar y están listas para escenarios de desarrollo y pruebas. El soporte técnico está disponible para recursos y regiones de Azure, y familias de tamaños de máquina virtual seleccionados. Para más información sobre cómo empezar a trabajar y qué recursos, regiones y familias de tamaños de máquina virtual de Azure puede probar con las zonas de disponibilidad, consulte la [introducción a las zonas de disponibilidad](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Para obtener soporte técnico, puede consultar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) o [abrir un vale de soporte de Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En este ejemplo se crea un grupo de recursos denominado *myResourceGroup* en la región *westeurope*. *Europa Occidental* es una de las regiones de Azure que admite zonas de disponibilidad en la versión preliminar.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Crear una dirección IP pública zonal

Cree una dirección IP pública en una zona de disponibilidad con el siguiente comando:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Cuando asigna una dirección IP pública de SKU estándar a una interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](security-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obtener información de zonas sobre una dirección IP pública

Obtenga la información de zonas de una dirección IP pública con el comando siguiente:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [zonas de disponibilidad](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Más información sobre [direcciones IP públicas](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 