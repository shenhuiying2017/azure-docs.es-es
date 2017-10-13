---
title: "Ejemplo de script de Azure PowerShell: eliminación de contenedores por prefijo | Microsoft Docs"
description: "Elimine contenedores de blobs de Azure Storage según un prefijo de nombre de contenedor."
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
ms.openlocfilehash: 402958c4e2978630bc79557704a77e77a8b9a7e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminación de contenedores según el prefijo del nombre de contenedor

Este script elimina contenedores en Azure Blob Storage según un prefijo del nombre del contenedor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, contenedores restantes y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para eliminar contenedores según el prefijo del nombre de contenedor. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtiene una cuenta de Storage especificada o todas las cuentas de Storage de un grupo de recursos o la suscripción. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Enumera los contenedores de almacenamiento asociados con una cuenta de almacenamiento. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Elimina el contenedor de almacenamiento especificado. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de script adicionales de PowerShell de almacenamiento en los [ejemplos de PowerShell para Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
