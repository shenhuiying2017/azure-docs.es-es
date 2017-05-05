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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 0e9fa1b1397c60985de9d2e60b3f01146036801f
ms.lasthandoff: 04/26/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificación de una conexión de VPN Gateway

En este artículo se muestra cómo comprobar la conexión de VPN Gateway para los modelos de implementación de Resource Manager y clásica.

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

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Comprobar mediante CLI de Azure

Para comprobar mediante la CLI de Azure, instale la versión más reciente de los comandos CLI (2.0 o posterior). Para más información sobre la instalación de los comandos CLI, vea [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalar CLI de Azure 2.0).

### <a name="log-in-to-your-azure-account"></a>Inicie sesión en la cuenta de Azure.

1. Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

  ```azurecli
  az login
  ```
2. Si tiene más de una suscripción de Azure, enumere las suscripciones de la cuenta.

  ```azurecli
  Az account list --all
  ```
3. Especifique la suscripción que desea usar.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>Comprobación de la conexión

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

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
* Puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver los pasos.


