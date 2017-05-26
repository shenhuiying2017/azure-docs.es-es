---
title: "Script de la CLI de Azure: Escalado de un grupo elástico | Microsoft Docs"
description: "Ejemplo de Script de la CLI de Azure: Escalado de un grupo elástico de bases de datos | Microsoft Docs"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: azurecli
ms.topic: samples
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5ca7befc4b7ea031287b81ae6834496579b480fd
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Escalado de un grupo elástico en Azure SQL Database mediante la CLI de Azure

Este script de ejemplo de la CLI crea grupos elásticos, traslada bases de datos agrupadas y cambia los niveles de rendimiento. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Traslado de una base de datos de un grupo a otro")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, un servidor lógico, una base de datos SQL y reglas de firewall. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda la base de datos SQL. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Crea un grupo elástico de bases de datos en el servidor lógico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Crea una base de datos SQL en el servidor lógico. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Actualiza un grupo elástico de bases de datos; en este ejemplo, cambia la eDTU asignada. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).

