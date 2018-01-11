---
title: "Reconfiguración de Integration Runtime de SSIS de Azure | Microsoft Docs"
description: "Aprenda a reconfigurar una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory después de haberlo aprovisionado."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: 19a81917ade977a0d04934b77e8213ef6d9e0f12
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="reconfigure-an-azure-ssis-integration-runtime"></a>Reconfiguración de una instancia de Integration Runtime de SSIS de Azure
En el artículo [Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure) se muestra cómo crear una instancia de Integration Runtime de SSIS de Azure mediante Azure Data Factory. En este artículo se proporciona información acerca de cómo volver a configurar una instancia de Integration Runtime de SSIS de Azure.  

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

Después de aprovisionar e iniciar una instancia de Integration Runtime de SSIS de Azure, puede volver a configurarla mediante la ejecución de una secuencia de los cmdlets `Stop` - `Set` - `Start` de PowerShell de forma consecutiva. Por ejemplo, el siguiente script de PowerShell cambia el número de nodos asignados a la instancia de Integration Runtime de SSIS de Azure a 5.

## <a name="stop-azure-ssis-ir"></a>Detener una instancia de Integration Runtime de SSIS de Azure
En primer lugar, detenga la instancia de Integration Runtime de SSIS de Azure con el cmdlet [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Este comando libera todos sus nodos y detiene la facturación.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="reconfigure-azure-ssis-ir"></a>Reconfigurar una instancia de Integration Runtime de SSIS de Azure
Reconfigurar la instancia de Integration Runtime de SSIS de Azure con el cmdlet [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). El siguiente comando de ejemplo permite escalar una instancia de Integration Runtime de SSIS de Azure a cinco nodos.

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
```  

## <a name="start-azure-ssis-ir"></a>Iniciar una instancia de Integration Runtime de SSIS de Azure
A continuación, inicie la instancia de Integration Runtime de SSIS de Azure con el cmdlet [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Este comando asigna todos sus nodos para ejecutar paquetes SSIS.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para más información sobre Integration Runtime de SSIS de Azure: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Unión de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
 
