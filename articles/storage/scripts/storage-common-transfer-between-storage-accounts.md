---
title: "Ejemplo de script de Azure PowerShell: migración de blobs entre cuentas de almacenamiento mediante AzCopy en Windows | Documentos de Microsoft"
description: Con AzCopy se copia el contenido de Blob de una cuenta de Azure Storage a otra.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migración de blobs entre cuentas de almacenamiento mediante AzCopy en Windows

Este ejemplo copia todos los objetos blob de una cuenta de almacenamiento de origen proporcionada por el usuario en una cuenta de almacenamiento de destino proporcionada por el usuario. 

Esto se consigue haciendo uso del comando `Get-AzureStorageContainer`, que enumera todos los contenedores en una cuenta de almacenamiento. A continuación, el ejemplo emite comandos de AzCopy, copiando cada contenedor de la cuenta de almacenamiento de origen a la cuenta de almacenamiento de destino. Si se produce algún error, el ejemplo vuelve a intentar $retryTimes (el valor predeterminado es 3 y puede modificarse con el parámetro `-RetryTimes`). Si se produce un error en cada reintento, el usuario puede volver a ejecutar el script proporcionando el ejemplo con el último contenedor que se haya copiado correctamente mediante el parámetro `-LastSuccessContainerName`. El ejemplo continúa copiando contenedores desde ese punto.

Este ejemplo requiere la versión **4.0.2** del módulo de almacenamiento de Azure PowerShell, o cualquier versión posterior. Puede comprobar la versión instalada mediante `Get-Module -ListAvailable Azure.storage`. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este ejemplo también necesita la versión más reciente de [AzCopy en Windows](http://aka.ms/downloadazcopy). El directorio de instalación predeterminado es `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Este ejemplo toma un nombre y una clave de cuenta de almacenamiento de origen, un nombre y una clave de cuenta de almacenamiento de destino y la ruta completa del archivo de AzCopy.exe (si no está instalado en el directorio predeterminado).

Los siguientes son ejemplos de la entrada de este ejemplo:

Si AzCopy se instala en el directorio predeterminado:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Si AzCopy no se instala en el directorio predeterminado:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Si se ha producido el error y se tiene que volver a ejecutar el ejemplo desde un contenedor determinado: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para copiar datos de una cuenta de almacenamiento a otra. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Devuelve los contenedores de almacenamiento asociados con esta cuenta de Storage. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Crea un contexto de Azure Storage. |

## <a name="next-steps"></a>pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Puede encontrar ejemplos de script adicionales de PowerShell de almacenamiento en los [ejemplos de PowerShell para Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
