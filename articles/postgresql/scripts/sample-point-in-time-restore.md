---
title: "Script de la CLI de Azure: restauración de un servidor de Azure Database for PostgreSQL"
description: "En este script de la CLI de Azure de ejemplo se muestra cómo restaurar un servidor de Azure Database for MySQL y sus bases de datos a un momento anterior."
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: dfc53ae10055b0e8583fb0c705e605bbbb999760
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Restauración de un servidor de Azure Database for PostgreSQL mediante la CLI de Azure
Este script de la CLI de ejemplo restaura un único servidor de Azure Database for PostgreSQL a un momento anterior.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure para este ejemplo. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, cambie las líneas resaltadas para personalizar el nombre de usuario de administrador y la contraseña. Reemplace el identificador de suscripción que se usa en los comandos az monitor por su propio identificador de suscripción.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgresql server create](/cli/azure/postgresql/server#az_msql_server_create) | Crea un servidor de PostgreSQL que hospeda las bases de datos. |
| [az postgresql server restore](/cli/azure/postgresql/server#az_msql_server_restore) | Restaura un servidor desde una copia de seguridad. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>pasos siguientes
- Para más información sobre la CLI de Azure: [Documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- [Copia de seguridad y restauración de un servidor en Azure Database for PostgreSQL mediante Azure Portal](../howto-restore-server-portal.md)
