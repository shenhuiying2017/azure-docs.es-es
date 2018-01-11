---
title: "Script de ejemplo de Azure PowerShell: apertura del puerto de la aplicación en el equilibrador de carga | Microsoft Docs"
description: "Script de ejemplo de Azure PowerShell: apertura de un puerto en el equilibrador de carga de Azure para una aplicación de Service Fabric."
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
ms.date: 12/08/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c643fc9e575a8e836a361893d78348bbd627a425
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Apertura de un puerto de aplicación en el equilibrador de carga de Azure

Las aplicaciones de Service Fabric que se ejecutan en Azure se ubican detrás del equilibrador de carga de Azure. Este script de ejemplo abre un puerto en un equilibrador de carga de Azure para que una aplicación de Service Fabric se comunique con los clientes externos. Personalice los parámetros según sea necesario. Si el clúster está en un grupo de seguridad de red, también [agregue una regla de grupo de seguridad de red de entrada](service-fabric-powershell-add-nsg-rule.md) para permitir el tráfico entrante.

Si es necesario, instale el módulo Service Fabric PowerShell con el [SDK de Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtiene un recurso de Azure.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Obtiene el equilibrador de carga de Azure. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Agrega una configuración de sondeo a un equilibrador de carga.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Obtiene una configuración de sondeo para un equilibrador de carga. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Agrega una configuración de regla a un equilibrador de carga. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Establece el estado del objetivo para un equilibrador de carga. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de PowerShell para Azure Service Fabric en los [ejemplos de Azure PowerShell](../service-fabric-powershell-samples.md).
