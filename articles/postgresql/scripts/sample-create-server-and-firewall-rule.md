---
title: "Script de la CLI de Azure: creación de un servidor de Azure Database for PostgreSQL"
description: 'Ejemplo de script de la CLI de Azure: crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor.'
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: a04d1fd71f17c5ceef175b457c75d609b827c405
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creación de una base de datos de Azure para el servidor PostgreSQL y configuración de una regla de firewall mediante la CLI de Azure
Este script de la CLI de ejemplo crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor. Después de ejecutar el script correctamente, el servidor PostgreSQL es accesible desde todos los servicios de Azure y la dirección IP configurada.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide ejecutar la CLI localmente, este artículo necesita la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para comprobar la versión. Consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli) para instalar o actualizar su versión.

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, va a modificar las líneas resaltadas para actualizar el nombre de usuario administrador y la contraseña a los suyos propios.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=18-19 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Use el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él después de ejecutarse el script de ejemplo. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los comandos que se describen en la tabla siguiente:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Crea un servidor de PostgreSQL que hospeda las bases de datos. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) | Crea una regla de firewall para permitir el acceso al servidor y las bases de datos de este desde el intervalo de direcciones IP especificado. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>pasos siguientes
- Para más información sobre la CLI de Azure, vea la [documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
