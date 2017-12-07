---
title: 'Ejemplo de script de Azure PowerShell : agregar una regla de grupo de seguridad de red | Microsoft Docs'
description: "Ejemplo de script de Azure PowerShell: agrega un grupo de seguridad de red para permitir el tráfico entrante en un puerto específico."
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
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adición de una regla de grupo de seguridad de red entrante

Este script de ejemplo crea una regla de grupo de seguridad de red para permitir el tráfico entrante en el puerto 8081.  El script obtiene el recurso `Microsoft.Network/networkSecurityGroups` en el que se encuentra el clúster, crea una nueva regla de configuración de seguridad de red y actualiza el grupo de seguridad de red. Personalice los parámetros según sea necesario.

Si es necesario, instale PowerShell con la instrucción que se encuentra en la [Guía de instalación de Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtiene el recurso `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Obtiene el grupo de seguridad de red por nombre.|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Agrega una configuración de regla de seguridad de red a un grupo de seguridad de red. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Establece el estado de objetivo para un grupo de seguridad de red.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).
