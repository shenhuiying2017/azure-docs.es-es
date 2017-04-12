---
title: "Verificación de una conexión de VPN Gateway | Microsoft Docs"
description: "En este artículo se explica cómo verificar una conexión de VPN Gateway de red virtual."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3
ms.lasthandoff: 01/31/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificación de una conexión de VPN Gateway
Puede verificar la conexión de VPN Gateway de red virtual a través del portal o de PowerShell. Este artículo contiene pasos para el modelo de Resource Manager y el modelo de implementación clásica.

## <a name="verify-using-the-azure-portal"></a>Comprobación con Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Comprobar mediante PowerShell

Para realizar la verificación con PowerShell, instale la última versión de los cmdlets de PowerShell para Azure Resource Manager. Para obtener información sobre la instalación de cmdlets de PowerShell, vea [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). Para obtener más información sobre los cmdlets de Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure
1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta.

  ```powershell
  Login-AzureRmAccount
  ```
2. Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>Comprobación de la conexión

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Verificación con Azure Portal (clásico)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Verificación con PowerShell (clásico)
Para realizar la verificación con PowerShell, instale la última versión de los cmdlets de Azure PowerShell. Asegúrese de descargar e instalar las versiones de Resource Manager y Service Management (SM). Para obtener información sobre la instalación de cmdlets de PowerShell, vea [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs). 

### <a name="log-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure
1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta.

  ```powershell
  Login-AzureRmAccount
  ```
2. Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Inicie sesión para usar los cmdlets de Service Management para el modelo de implementación clásica.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>Comprobación de la conexión
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Pasos siguientes
* Puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver los pasos.


