---
title: "Ejemplo de script de CLI de Azure: copia de seguridad de una aplicación web | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: copia de seguridad de una aplicación web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: e585c9b203145dcdaf2b63a6044ba2e3bded9572
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="back-up-a-web-app"></a>Copia de seguridad de una aplicación web

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, crea una copia de seguridad única para ella. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, necesitará la CLI de Azure versión 2.0 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) | Crea una cuenta de almacenamiento. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create) | Crea un contenedor de Azure Storage. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_generate_sas) | Genera un token de SAS para un contenedor de Azure Storage.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Crea un plan de App Service, |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Crea una aplicación web de Azure. |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_create) | Crea una copia de seguridad para una aplicación web. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_list) | Obtiene una lista de copias de seguridad de una aplicación web. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
