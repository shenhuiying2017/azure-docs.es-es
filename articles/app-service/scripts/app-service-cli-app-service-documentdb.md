---
title: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a Cosmos DB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a Cosmos DB"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b581f8d5a4e924e0aa88bf614839c917345eb570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-cosmos-db"></a>Conexión de una aplicación web a Cosmos DB

En este escenario aprenderá a crear una cuenta de Cosmos DB y una aplicación web de Azure. Luego, vinculará la instancia de Cosmos DB a la aplicación web mediante la configuración de la aplicación.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web, Cosmos DB y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crea una aplicación web de Azure. |
| [az cosmosdb create](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#az_cosmosdb_create) | Crea una cuenta de Cosmos DB. Aquí es donde se almacenarán los datos. |
| [az cosmosdb list-keys](https://docs.microsoft.com/en-us/cli/azure/cosmosdb#az_cosmosdb_list_keys) | Devuelve las claves de acceso de la cuenta de Cosmos DB especificada. |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Crea o actualiza una configuración de aplicación para una aplicación web de Azure. La configuración de la aplicación se expone como variables de entorno para la aplicación. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
