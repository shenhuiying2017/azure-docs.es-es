---
title: "Script de la CLI de Azure: creación de un servidor de Azure Database for MySQL"
description: Este script de la CLI de ejemplo crea un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) y configura una regla de firewall de nivel de servidor.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 217b3d1ba8d9bc1f287327eb376a789dbfb4ee27
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Cree un servidor MySQL y configure una regla de firewall mediante la CLI de Azure
Este script de la CLI de ejemplo crea un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) y configura una regla de firewall de nivel de servidor. Una vez que el script se ejecuta correctamente, todos los servicios de Azure y la dirección IP configurada pueden tener acceso al servidor MySQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide ejecutar la CLI localmente, este artículo necesita la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para comprobar la versión. Consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli) para instalar o actualizar su versión. 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, va a modificar las líneas resaltadas para actualizar el nombre de usuario administrador y la contraseña a los suyos propios.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=18-19 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Use el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él después de ejecutarse el script de ejemplo. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los comandos que se describen en la tabla siguiente:

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Crea un servidor MySQL que hospeda las bases de datos. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) | Crea una regla de firewall para permitir el acceso al servidor y las bases de datos de este desde el intervalo de direcciones IP especificado. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>pasos siguientes
- Para más información sobre la CLI de Azure: [Documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe scripts adicionales en: [Ejemplos de la CLI de Azure para Azure Database for MySQL](../sample-scripts-azure-cli.md).
