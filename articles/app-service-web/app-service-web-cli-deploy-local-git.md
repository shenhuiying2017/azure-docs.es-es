---
title: "Script de ejemplo de la CLI de Azure: implementación de una aplicación web desde un repositorio GIT local | Microsoft Docs"
description: "Script de ejemplo de la CLI de Azure: implementación de una aplicación web desde un repositorio GIT local"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>Implementación de una aplicación web desde un repositorio GIT local

Este script de ejemplo realiza lo siguiente mediante la CLI de Azure 2.0: 

* Crear una aplicación web en Azure App Service en la región de Azure de Europa Occidental.
* Implementar código de aplicación web desde un repositorio GIT local.
* Mostrar la aplicación web implementada en el explorador.

## <a name="prerequisites"></a>Requisitos previos

* Ejecute `az login` para iniciar sesión en Azure.
* Confirme el código de la aplicación web en un repositorio GIT local.

## <a name="create-vm-sample"></a>Ejemplo de creación de una máquina virtual

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "Implementación de una aplicación web desde un repositorio GIT local")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación web y todos los recursos relacionados.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Crea una aplicación web. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | Crea una configuración de control de código fuente para un repositorio GIT local. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abre una aplicación web en un explorador. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI para Azure App Service Web Apps en los [ejemplos de la CLI de Azure]().
