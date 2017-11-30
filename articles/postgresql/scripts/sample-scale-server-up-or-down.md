---
title: 'Script de la CLI de Azure: escalado de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) | Microsoft Docs'
description: "Ejemplo de script de la CLI de Azure: escalado del servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) a un nivel de rendimiento diferente después de consultar las métricas."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/27/2017
ms.openlocfilehash: a5a24e9aeea193df28bd49d5c428a72b5ec75d1b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Supervisión y escalado de un solo servidor PostgreSQL mediante la CLI de Azure
Este script de la CLI de ejemplo escala un solo servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) a un nivel de rendimiento diferente después de consultar las métricas. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, cambie las líneas resaltadas para personalizar el nombre de usuario de administrador y la contraseña. Reemplace el identificador de suscripción usado en los comandos de monitor az por su propio identificador de suscripción.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Crea un servidor PostgreSQL que hospeda las bases de datos. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Especifica el valor de métrica de los recursos. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la CLI de Azure, vea la [documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- Para más información sobre el escalado, vea [Niveles de servicio](../concepts-service-tiers.md) y [Unidades de Compute y Storage](../concepts-compute-unit-and-storage.md).
