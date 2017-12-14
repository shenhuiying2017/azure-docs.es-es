---
title: "El ejemplo de la CLI escala un grupo elástico de SQL en Azure SQL Database | Microsoft Docs"
description: "Script de ejemplo de la CLI de Azure para escalar un grupo elástico de SQL en Azure SQL Database"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 1bd1bb6005f463a8a317ec959661ac6c75d600d1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="use-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Uso de la CLI para escalar un grupo elástico de SQL en Azure SQL Database

Este script de ejemplo de la CLI de Azure crea grupos elásticos de SQL, traslada bases de datos agrupadas y cambia los niveles de rendimiento de los grupos elásticos. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, un servidor lógico, SQL Database y reglas de firewall. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Crea un servidor lógico que hospeda SQL Database. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) | Crea un grupo elástico de bases de datos en el servidor lógico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Crea la instancia de SQL Database en el servidor lógico. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) | Actualiza un grupo elástico de bases de datos; en este ejemplo, cambia la eDTU asignada. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).
