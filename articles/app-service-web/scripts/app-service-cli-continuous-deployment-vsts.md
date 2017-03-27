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
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 5fce9171dc249445d78cee248fb2a7ec62a3dabb
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Creación de una aplicación web con implementación continua desde Visual Studio Team Services

Este script de ejemplo realiza lo siguiente mediante la CLI de Azure 2.0: 

* Crear una aplicación web en Azure App Service en la región de Azure de Europa Occidental. 
* Implementar el código de aplicación web desde Visual Studio Team Services.
* Mostrar la aplicación web de Azure implementada en el explorador.

## <a name="prerequisites"></a>Requisitos previos

* Ejecute `az login` para iniciar sesión en Azure.
* Coloque el código de la aplicación web en un repositorio de Visual Studio Team Services.
* Para un repositorio de Visual Studio Team Services de su propiedad, [cree un token de acceso](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

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
