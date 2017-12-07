---
title: 'Restablecimiento de un circuito de Azure ExpressRoute con errores: PowerShell | Microsoft Docs'
description: "Este artículo le ayudará a restablecer un circuito de ExpressRoute que se encuentra en un estado con errores."
documentationcenter: na
services: expressroute
author: anzaman
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 0e017200193de3e4a02275cec3b09c32f1fa5c31
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="reset-a-failed-expressroute-circuit"></a>Restablecimiento de un circuito de ExpressRoute con errores

Cuando una operación de un circuito de ExpressRoute no se completa correctamente, el circuito puede entrar en un estado "con errores". Este artículo le ayudará a restablecer un circuito de Azure ExpressRoute con errores.

## <a name="reset-a-circuit"></a>Restablecimiento de un circuito

1. Instale la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Para obtener más información, vea [Install and Configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

2. Abra la consola de PowerShell con privilegios elevados y conéctese a su cuenta. Use el siguiente ejemplo para conectarse:

  ```powershell
  Login-AzureRmAccount
  ```
3. Si tiene varias suscripciones de Azure, compruebe las suscripciones de la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Especifique la suscripción que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Ejecute los comandos siguientes para restablecer un circuito que se encuentra en un estado con errores:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

El circuito ahora debería funcionar correctamente. Abra una incidencia de soporte técnico con [Soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si el circuito sigue en un estado con errores.

## <a name="next-steps"></a>Pasos siguientes

Si sigue teniendo problemas, abra una incidencia de soporte técnico dirigida al [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
