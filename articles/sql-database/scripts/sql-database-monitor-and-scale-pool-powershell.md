---
title: "Script de Azure PowerShell: supervisión y escalado de un grupo elástico de SQL | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: supervisión y escalado de un grupo elástico de SQL Database mediante PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 0be15d7a90e6201516bcf01eef9bc219ee211e47
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-and-scale-a-sql-database-elastic-pool-using-powershell"></a>Supervisión y escalado de un grupo elástico de SQL Database mediante PowerShell

Este script de PowerShell de ejemplo supervisa las métricas de rendimiento de un grupo elástico, lo escala a un nivel de rendimiento superior y crea una regla de alerta en una de las métricas de rendimiento. 

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el cmdlet `Add-AzureRmAccount`.

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1 "Supervisión y escalado de una instancia única de SQL Database")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Crea un servidor lógico que hospeda una base de datos o un grupo elástico. |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | Crea un grupo elástico en un servidor lógico. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Crea una base de datos en un servidor lógico como una base de datos única o agrupada. |
| [Get-AzureRmMetric](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/get-azurermmetric) | Muestra la información de uso del tamaño de la base de datos.|
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Agrega o actualiza una regla de alerta basada en métricas. |
| [Set-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqlelasticpool) | Actualiza las propiedades del grupo elástico |
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | Establece una regla de alerta para supervisar automáticamente las DTU en el futuro. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de scripts de PowerShell de SQL Database en los [scripts de PowerShell de Azure SQL Database](../sql-database-powershell-samples.md).

