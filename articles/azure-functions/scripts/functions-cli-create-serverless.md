---
title: "Ejemplo de script de la CLI de Azure: creación de una instancia de Function App para la ejecución sin servidor | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una instancia de Function App para la ejecución sin servidor"
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
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 37046967bd5ab0d797d1c66690db7200fb4805e2
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>Creación de una instancia de Function App para la ejecución sin servidor

Este script de ejemplo crea una instancia de Azure Function App, que es un contenedor para las funciones. La instancia de Function App se creará usando el [plan de consumo](../functions-scale.md#consumption-plan), que es ideal para las cargas de trabajo sin servidor controladas por eventos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

Este script crea una aplicación de Azure Function App con el [plan de consumo](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Creación de una función de Azure en un plan de consumo")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Cada comando de la tabla crea un vínculo a documentación específica del comando. Este script usa los siguientes comandos:

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account create](/cli/azure/storage/account#create) | Crea una cuenta de Azure Storage. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Crea una función de Azure. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Azure Functions en la [documentación de Azure Functions](../functions-cli-samples.md).

