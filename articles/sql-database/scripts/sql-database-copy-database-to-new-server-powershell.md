---
title: Copia mediante script de Azure PowerShell de una instancia de SQL Database en un nuevo servidor | Microsoft Docs
description: 'Ejemplo de script de Azure PowerShell: copie una instancia de SQL Database a un servidor nuevo con PowerShell'
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
ms.openlocfilehash: 0f750cb3021f04ade9d639e2de14e1b119cde36e
ms.lasthandoff: 03/10/2017

---

# <a name="copy-a-sql-database-to-a-new-server-using-powershell"></a>Copia de una instancia de SQL Database en un nuevo servidor mediante PowerShell

Este script de PowerShell de ejemplo crea una copia de una base de datos existente en un nuevo servidor. 

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el cmdlet `Add-AzureRmAccount`.

## <a name="copy-a-database-to-a-new-server"></a>Copia de una base de datos en un nuevo servidor

[!code-powershell[main](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1 "Copia de una base de datos en un nuevo servidor")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Crea un servidor lógico que hospeda una base de datos o un grupo elástico. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Crea una base de datos en un servidor lógico como una base de datos única o agrupada. |
| [New-AzureRmSqlDatabaseCopy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabasecopy) | Crea una copia de una base de datos que utiliza la instantánea en el momento actual. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de scripts de PowerShell de SQL Database en los [scripts de PowerShell de Azure SQL Database](../sql-database-powershell-samples.md).

