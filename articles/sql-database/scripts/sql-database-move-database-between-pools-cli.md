---
title: "Script de la CLI de Azure: traslado de una base de datos SQL y grupos elásticos | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: traslado de una instancia de SQL Database entre grupos elásticos mediante la CLI de Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: df4a62783cf3f8f644de850cb80bdad8352cf372
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>Cree grupos elásticos y mueva las bases de datos de un grupo a otro, y fuera de un grupo mediante la CLI de Azure

Este script de ejemplo de la CLI crea dos grupos elásticos, traslada una base de datos de un grupo elástico al otro y, a continuación, traslada una base de datos fuera de un grupo elástico a un nivel de rendimiento de base de datos única. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Traslado de una base de datos de un grupo a otro")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda una base de datos o un grupo elástico. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Crea un grupo elástico en el servidor lógico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Crea una base de datos en un servidor lógico como una base de datos única o agrupada. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Actualiza las propiedades de la base de datos o traslada una base de datos a un grupo elástico, fuera de este o entre grupos elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).



