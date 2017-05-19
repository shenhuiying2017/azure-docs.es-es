---
title: "Creación de una aplicación de función e implementación de código de función desde GitHub | Microsoft Docs"
description: "Creación de una aplicación de función e implementación de código de función desde GitHub"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: functions
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dc1b15d4e5c5ab0525b64e8844fe644cde3cb257
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="create-an-app-service"></a>Creación de una instancia de App Service

Este script de ejemplo crea una aplicación de función usando el [plan de consumo](../functions-scale.md#consumption-plan) con sus recursos relacionados e implementa de forma continua el código de la función desde un repositorio de GitHub. En este ejemplo, necesita:

* Un repositorio de GitHub con código de funciones, para el cual tenga permisos administrativos.
* Un [token de acceso personal (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) para su cuenta de GitHub.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Servicio de Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Cada comando de la tabla crea un vínculo a documentación específica del comando. Este script usa lo siguiente:

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Asocia una aplicación de función con un repositorio GIT o Mercurial. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Azure Functions en la [documentación de Azure Functions](../functions-cli-samples.md).
