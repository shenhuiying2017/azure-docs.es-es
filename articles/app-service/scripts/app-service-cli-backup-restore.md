---
title: "Ejemplo de script de CLI de Azure: restaurar una aplicación web desde una copia de seguridad | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: restaurar una aplicación web desde una copia de seguridad"
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
ms.openlocfilehash: bec915f3e321cf2422f7cc2cad3ce1dfb6b3317a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="restore-a-web-app-from-a-backup"></a>Restauración de una aplicación web desde una copia de seguridad

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, crea una copia de seguridad única para ella. 

Para ejecutar este script, necesita una copia de seguridad existente de una aplicación web. Para crearla, consulte [Copia de seguridad de una aplicación web](app-service-cli-backup-onetime.md) o [Create a scheduled backup for a web app](app-service-cli-backup-scheduled.md) (Creación de una copia de seguridad programada de una aplicación web).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, necesitará la CLI de Azure versión 2.0 o posterior. Para encontrar la versión, ejecute `az --version`. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_list) | Obtiene una lista de copias de seguridad de una aplicación web. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az_webapp_config_backup_restore) | Restaura una aplicación web desde una copia de seguridad. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
