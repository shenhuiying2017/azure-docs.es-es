---
title: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a una base de datos SQL | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a una base de datos SQL"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: e85b405e61460c7cde8dd8a6001add736c53c713
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Conexión de una aplicación web a una base de datos SQL

Este script de ejemplo crea una base de datos de Azure SQL Database y una aplicación web de Azure. A continuación, se vincula la base de datos SQL a la aplicación web mediante la configuración de la aplicación.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, necesitará la CLI de Azure versión 2.0 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web, SQL Database y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Crea un plan de App Service, |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Crea una aplicación web de Azure. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) | Crea un servidor de SQL Database.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) | Crea una nueva base de datos con el servidor de SQL Database. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string) | Genera una cadena de conexión en una base de datos. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | Crea o actualiza una configuración de aplicación para una aplicación web de Azure. La configuración de la aplicación se expone como variables de entorno para la aplicación. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
