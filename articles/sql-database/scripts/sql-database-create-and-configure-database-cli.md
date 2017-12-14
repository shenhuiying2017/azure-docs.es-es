---
title: Ejemplo de la CLI para crear una instancia de Azure SQL Database | Microsoft Docs
description: Use este ejemplo de script de la CLI de Azure para crear una base de datos SQL.
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 10/11/2017
ms.author: janeng
ms.openlocfilehash: 404d43a6f2fa38276b9517e9542f1e50a4b1980b
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Uso de la CLI para crear una instancia única de Azure SQL Database y configurar una regla de firewall

Este script de ejemplo de la CLI de Azure crea una instancia de Azure SQL Database y configura una regla de firewall en el nivel de servidor. Después de ejecutar el script correctamente, SQL Database es accesible desde todos los servicios de Azure y la dirección IP configurada. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Crea un servidor lógico que hospeda SQL Database. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Crea una regla de firewall para permitir el acceso a todas las instancias de SQL Database en el servidor desde el intervalo de direcciones IP especificado. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Crea la instancia de SQL Database en el servidor lógico. |
| [az group delete](/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).

