---
title: "Script de la CLI de Azure: creación de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) | Microsoft Docs"
description: 'Ejemplo de script de la CLI de Azure: crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor.'
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 11/27/2017
ms.openlocfilehash: f92739181a2011be7ce609b65bf7c862ac705129
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Creación de una base de datos de Azure para el servidor PostgreSQL y configuración de una regla de firewall mediante la CLI de Azure
Este script de la CLI de ejemplo crea un servidor de Azure Database for PostgreSQL (Base de datos de Azure para PostgreSQL) y configura una regla de firewall de nivel de servidor. Después de ejecutar el script correctamente, el servidor PostgreSQL es accesible desde todos los servicios de Azure y la dirección IP configurada.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, edite las líneas resaltadas para personalizar el nombre de usuario y la contraseña de administrador.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Crea un servidor de PostgreSQL que hospeda las bases de datos. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) | Crea una regla de firewall para permitir el acceso al servidor y las bases de datos de este desde el intervalo de direcciones IP especificado. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la CLI de Azure, vea la [documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe otros scripts adicionales: [Ejemplos de la CLI de Azure para Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
