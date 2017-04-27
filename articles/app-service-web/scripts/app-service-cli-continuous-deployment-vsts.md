---
title: "Ejemplo de script de la CLI de Azure: creación de una aplicación web con implementación continua desde Visual Studio Team Services | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una aplicación web con implementación continua desde Visual Studio Team Services"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: b82e51d8d96c98cba1d5989060eed40ed7f2d4fe
ms.lasthandoff: 04/04/2017

---
# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Creación de una aplicación web con implementación continua desde Visual Studio Team Services

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, configura la implementación continua desde un repositorio de Visual Studio Team Services. Para este ejemplo, necesitará lo siguiente:

* Un repositorio de Visual Studio Team Services con el código de la aplicación, para la cual tendrá que tener permisos administrativos.
* Un [token de acceso personal (PAT)](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para la cuenta de Visual Studio Team Services.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="create-app-sample"></a>Creación de una aplicación de ejemplo

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Creación de una aplicación web con implementación continua desde Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea una aplicación web de Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Asocia una aplicación web de Azure con un repositorio de GIT o Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Abre una aplicación web de Azure en un explorador. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
