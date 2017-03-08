---
title: "Script de ejemplo de la CLI de Azure: implementación continua de aplicaciones web desde GitHub | Microsoft Docs"
description: "Script de ejemplo de la CLI de Azure: implementación continua de aplicaciones web desde GitHub"
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>Implementación continua de aplicaciones web desde GitHub

Este script de ejemplo realiza lo siguiente mediante la CLI de Azure 2.0: 

* Crear una aplicación web en Azure App Service en la región de Azure de Europa Occidental. 
* Implementar el código de la aplicación web desde GitHub.
* Mostrar la aplicación web implementada en el explorador.

## <a name="prerequisites"></a>Requisitos previos

* Ejecute `az login` para iniciar sesión en Azure.
* Coloque el código de aplicación web en un repositorio de GitHub de su propiedad.

> [!NOTE]
> Si usa un repositorio de GitHub público que no es suyo, App Service implementará el código desde dicho repositorio, pero no podrá configurar la clave SSH y los webhooks necesarios para la implementación continua.
>
>

## <a name="create-vm-sample"></a>Ejemplo de creación de una máquina virtual

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-github/deploy-github.sh "Implementación continua de aplicaciones web desde GitHub")]

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
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Asocia una aplicación web con un repositorio GIT o de Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abre una aplicación web en un explorador. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI para Azure App Service Web Apps en los [ejemplos de la CLI de Azure]().

