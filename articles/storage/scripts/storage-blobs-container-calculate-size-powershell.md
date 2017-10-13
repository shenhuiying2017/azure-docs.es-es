---
title: "Ejemplo de script de Azure PowerShell: calcular el tamaño del contenedor de blobs | Microsoft Docs"
description: "Calcule el tamaño de un contenedor en Azure Blob Storage sumando el tamaño de cada uno de sus blobs."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: robinsh
ms.openlocfilehash: b78462fd182af52cb058e9a95a77c24656658767
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Cálculo del tamaño de un contenedor de Blob Storage

Este script calcula el tamaño de un contenedor en Azure Blob Storage sumando el tamaño de los blobs de dicho contenedor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, el contenedor y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para calcular el tamaño del contenedor de Blob Storage. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtiene una cuenta de Storage especificada o todas las cuentas de Storage de un grupo de recursos o la suscripción. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Enumera los blobs de un contenedor. ||

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de script adicionales de PowerShell de almacenamiento en los [ejemplos de PowerShell para Azure Storage](../blobs/storage-samples-blobs-powershell.md).
