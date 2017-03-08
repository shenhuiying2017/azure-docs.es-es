---
title: "Ejemplo de script de la CLI de Azure: creación de una aplicación web e implementación de código desde GitHub | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una aplicación web e implementación de código desde GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9d30f8a40606cb1f5830905c6d172709800feeec
ms.openlocfilehash: e20e35b01ba9bdd723776ea3d990041a83d5cdcf
ms.lasthandoff: 02/27/2017

---


# <a name="create-a-web-app-and-deploy-code-from-github"></a>Creación de una aplicación web e implementación de código desde GitHub

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, implementa el código de la aplicación web desde un repositorio de GitHub (sin una implementación continua). Para la implementación de GitHub con una implementación continua, consulte [Creación de una aplicación web con implementación continua desde GitHub](app-service-cli-continuous-deployment-github.md).

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`, y de que tiene la URL de un repositorio GitHub para realizar la implementación.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="create-app-sample"></a>Creación de una aplicación de ejemplo

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Creación de una aplicación web e implementación de código desde GitHub")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación App Service y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

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
