---
title: "Administración de Integration Runtime de SSIS de Azure | Microsoft Docs"
description: "Aprenda a reconfigurar Integration Runtime de SSIS de Azure en Azure Data Factory después de haberlo aprovisionado."
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
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: cc0ed958a9e1018ed9f06fdcc94873ae5420ba95
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Administración de una instancia de Integration Runtime de SSIS de Azure
En el artículo [Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure) se muestra cómo crear una instancia de Integration Runtime de SSIS de Azure mediante Azure Data Factory. Este artículo lo complementa al proporcionar información sobre cómo detener, iniciar, rconfigurar o eliminar una instancia de Integration Runtime de SSIS de Azure.  

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

## <a name="stop"></a>Detención 
Detiene Integration Runtime de SSIS de Azure. Este comando libera todos sus nodos y detiene la facturación.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>Iniciar 
Inicia Integration Runtime de SSIS de Azure. Este comando asigna todos sus nodos e inicia la facturación.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>Volver a configurar
Después de aprovisionar e iniciar una instancia de Integration Runtime de SSIS de Azure, puede volver a configurarla mediante la ejecución de una secuencia de los cmdlets `Stop` - `Set` - `Start` de PowerShell de forma consecutiva. Por ejemplo, el siguiente script de PowerShell cambia el número de nodos asignados a la instancia de Integration Runtime de SSIS de Azure a 5.

1. En primer lugar, detenga Integration Runtime de SSIS de Azure mediante el siguiente comando:

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ahora, escale horizontalmente su instancia de Integration Runtime de SSIS de Azure a cinco nodos.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Luego, inicie Integration Runtime de SSIS de Azure. Todos sus nodos se asignan para ejecutar paquetes SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Remove
Para quitar una instancia de Integration Runtime de SSIS de Azure existente, ejecute el siguiente comando: 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para más información sobre Integration Runtime de SSIS de Azure: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Unión de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 
