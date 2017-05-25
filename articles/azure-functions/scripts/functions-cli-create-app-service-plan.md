---
title: "Ejemplo de script de la CLI de Azure: creación de una instancia de Function App en un plan de App Service | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una instancia de Function App en un plan de App Service"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 8c98e392c1c735458184bb782ed9c42f468f33de
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Creación de una instancia de Function App en un plan de App Service

Este script de ejemplo crea una instancia de Azure Function App, que es un contenedor para las funciones. La instancia de Function App se creará usando un plan de App Service dedicado, lo que significa que los recursos del servidor están siempre disponibles.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

Este script crea una aplicación de Azure Function App mediante un [plan de App Service](../functions-scale.md#app-service-plan) dedicado.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Creación de una función de Azure en un plan de App Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Cada comando de la tabla crea un vínculo a documentación específica del comando. Este script usa los siguientes comandos:

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea una cuenta de Azure Storage. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Crea un plan de App Service, |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Crea una instancia de Azure Function App. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Azure Functions en la [documentación de Azure Functions](../functions-cli-samples.md).

