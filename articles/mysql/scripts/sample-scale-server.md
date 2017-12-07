---
title: Ejemplos de la CLI de Azure para escalar un servidor de Azure Database for MySQL | Microsoft Docs
description: "Este script de la CLI de ejemplo escala el servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) a un nivel de rendimiento diferente después de consultar las métricas."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/02/2017
ms.openlocfilehash: 517208b76757655990f82f50677267320eb1393c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Supervisión y escalado de un servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) mediante la CLI de Azure
Este script de la CLI de ejemplo escala un solo servidor de Azure Database for MySQL (Base de datos de Azure para MySQL) a un nivel de rendimiento diferente después de consultar las métricas.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo
En este script de ejemplo, cambie las líneas resaltadas para personalizar el nombre de usuario de administrador y la contraseña. Reemplace el identificador de suscripción usado en los comandos de monitor az por su propio identificador de suscripción. [!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=15-16 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Después de ejecutar el script de ejemplo, puede quitar el grupo de recursos y todos los recursos asociados ejecutando el comando siguiente: [!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| **Comando** | **Notas** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Crea un servidor MySQL que hospeda las bases de datos. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Especifica el valor de métrica de los recursos. |
| [az group delete](/cli/azure/group#az_group_delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la CLI de Azure: [Documentación de la CLI de Azure](/cli/azure/overview).
- Pruebe scripts adicionales en [Ejemplos de la CLI de Azure para la base de datos de Azure para MySQL](../sample-scripts-azure-cli.md).
- Para obtener más información sobre el escalado, consulte [Service Tiers](../concepts-service-tiers.md) (Niveles de servicio) y [Compute Units and Storage Units](../concepts-compute-unit-and-storage.md) (Unidades de proceso y almacenamiento).
