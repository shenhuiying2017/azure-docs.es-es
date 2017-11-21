---
title: 'Script de PowerShell: copia masiva de datos mediante el uso de Azure Data Factory | Microsoft Docs'
description: "Este script de PowerShell le muestra cómo usar Azure Data Factory para copiar datos desde un almacén de datos de origen a un almacén de datos de destino de forma masiva."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: c8044d70a7c793d865884223081433e49f014f7b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Script de PowerShell: copia masiva de varias tablas mediante Azure Data Factory

Este script de PowerShell de ejemplo copia los datos de varias tablas en una Azure SQL Database en un Azure SQL Data Warehouse.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Vea [tutorial: copia masiva](../tutorial-bulk-copy.md#prerequisites) para consultar los requisitos previos de ejecución de este ejemplo.

## <a name="sample-script"></a>Script de ejemplo

> [!IMPORTANT]
> Este script crea archivos JSON que definen entidades de Data Factory (servicio vinculado, conjunto de datos y canalización) en la carpeta c:\ del disco duro.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Para eliminar la factoría de datos del grupo de recursos, ejecute el siguiente comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos: 

| Comando | Notas |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Creación de una factoría de datos. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Crea un servicio vinculado en la factoría de datos. Un servicio vinculado enlaza un almacén de datos o proceso a una factoría de datos. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Crea un conjunto de datos en la factoría de datos. Un conjunto de datos representa la entrada/salida para una actividad en una canalización. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Crea una canalización en la factoría de datos. Una canalización contiene una o varias actividades que realizan una operación determinada. En esta canalización, una actividad de copia realiza una copia de los datos de una ubicación en otra ubicación en una instancia de Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Crea una ejecución para la canalización. En otras palabras, ejecuta la canalización. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtiene información detallada sobre la ejecución de la actividad (actividad ejecutar) en la canalización. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de scripts de PowerShell de Azure Data Factory en los [scripts de PowerShell de Azure Data Factory](../samples-powershell.md).