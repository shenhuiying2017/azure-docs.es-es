---
title: 'Script de la CLI de Azure: Descargar los registros del servidor de Azure Database for PostgreSQL'
description: "Este script de la CLI de Azure de ejemplo muestra cómo habilitar y descargar los registros del servidor de Azure Database for PostgreSQL."
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
ms.openlocfilehash: 8bd6e193ca52401b16e141162a76d39bdef840f1
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Habilitar y descargar registros de consultas lentos de un servidor de Azure Database for PostgreSQL con la CLI de Azure
Este script de la CLI de ejemplo habilita y descarga los registros de consultas lentos de un único servidor de Azure Database for PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure para este ejemplo. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, cambie las líneas resaltadas para personalizar el nombre de usuario de administrador y la contraseña. Reemplace < log_file_name > en los comandos az monitor por el nombre de su archivo de registro del servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Crea un servidor de PostgreSQL que hospeda las bases de datos. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Enumera los valores de configuración de un servidor. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Actualiza la configuración de un servidor. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Enumeración de los archivos de registro de un servidor. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Descargar los archivos de registro. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>pasos siguientes
- Para más información sobre la CLI de Azure: [Documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- [Configuración y acceso a los registros del servidor en Azure Portal](../howto-configure-server-logs-in-portal.md)
