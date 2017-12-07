---
title: 'Ejemplo de script de Azure PowerShell: cambiar el intervalo de puertos RDP | Microsoft Docs'
description: "Ejemplo de script de Azure PowerShell: cambiar el intervalo de puertos RDP de un clúster implementado."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9089d071c69830323a94140a5ebb4e9e865499c6
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-rdp-port-range-values"></a>Actualización de los valores del intervalo de puertos RDP

Este script de ejemplo cambia los valores del intervalo de puertos RDP en las VM del nodo de clúster una vez implementado el clúster.  Azure PowerShell se utiliza para que las máquinas virtuales subyacentes no hagan el ciclo.  El script obtiene el recurso `Microsoft.Network/loadBalancers` del clúster grupo de recursos del clúster y actualiza los valores `inboundNatPools.frontendPortRangeStart` y `inboundNatPools.frontendPortRangeEnd`. Personalice los parámetros según sea necesario.

Si es necesario, instale PowerShell con la instrucción que se encuentra en la [Guía de instalación de Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtiene el recurso `Microsoft.Network/loadBalancers`. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Actualiza el recurso `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de Azure PowerShell para Azure Service Fabric en los [ejemplos de PowerShell](../service-fabric-powershell-samples.md).
