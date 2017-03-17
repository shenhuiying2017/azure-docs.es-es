---
title: "Ejemplo de script de la CLI de Azure: creación de una aplicación web e implementación de código desde un repositorio local de Git | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una aplicación web e implementación de código desde un repositorio local de Git"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 20ea69ea285fc97bbcf755ea1c40447745ff4be0
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Creación de una aplicación web e implementación de código desde un repositorio local de Git

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, implementa el código de la aplicación web en un repositorio local de GitHub.

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login` y de que el código de la aplicación está confirmado en el repositorio local de Git.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Creación de una aplicación web e implementación de código desde un repositorio local de Git")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea una aplicación web de Azure. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | Establece las credenciales de implementación a nivel de la cuenta para App Service. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | Crea una configuración de control de código fuente para un repositorio GIT local. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Abre una aplicación web de Azure en un explorador. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
