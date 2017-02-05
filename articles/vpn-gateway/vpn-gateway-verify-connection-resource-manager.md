---
title: "Comprobación de una conexión de puerta de enlace | Microsoft Docs"
description: "En este artículo se muestra cómo comprobar una conexión de puerta de enlace mediante el modelo de implementación de Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: d1b6d12b1976b317e9ed496857439c86e99253f9


---
# <a name="verify-a-gateway-connection"></a>Comprobación de una conexión de puerta de enlace
Puede comprobar la conexión de la puerta de enlace de varias maneras diferentes. En este artículo se explica cómo comprobar el estado de una conexión de puerta de enlace de Resource Manager mediante el Portal de Azure y PowerShell.

## <a name="verify-using-powershell"></a>Comprobar mediante PowerShell
Necesitará instalar la versión más reciente de los cmdlets de PowerShell del Administrador de recursos de Azure. Para obtener información sobre la instalación de cmdlets de PowerShell, vea [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). Para obtener más información sobre los cmdlets de Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Paso 1: Inicie sesión en su cuenta de Azure
1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta.
   
        Login-AzureRmAccount
2. Compruebe las suscripciones para la cuenta.
   
        Get-AzureRmSubscription 
3. Especifique la suscripción que desea usar.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Paso 2: Comprobación de la conexión
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Comprobación con Azure Portal
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes
* Puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver los pasos.




<!--HONumber=Dec16_HO1-->


