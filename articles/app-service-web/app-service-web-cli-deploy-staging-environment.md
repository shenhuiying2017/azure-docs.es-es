---
title: "Script de ejemplo de la CLI de Azure: implementación de una aplicación web en un entorno de ensayo | Microsoft Docs"
description: "Script de ejemplo de la CLI de Azure: implementación de una aplicación web en un entorno de ensayo"
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>Implementación de una aplicación web en un entorno de ensayo

Este script de ejemplo realiza lo siguiente mediante la CLI de Azure 2.0: 

* Crear una aplicación web en Azure App Service en la región de Azure de Europa Occidental.
* Actualizar el plan de App Service al nivel ESTÁNDAR (el mínimo para las ranuras de implementación).
* Configurar una ranura de implementación denominada "ensayo".
* Implementar el código de aplicación web de GitHub en la ranura de ensayo.
* Abrir la ranura de ensayo implementada en el explorador para realizar la comprobación.
* Cambiar la ranura de ensayo a producción.

## <a name="prerequisites"></a>Requisitos previos

* Ejecute `az login` para iniciar sesión en Azure.
* Coloque el código de aplicación web en un repositorio de GitHub de su propiedad.

> [!NOTE]
> Si usa un repositorio de GitHub público que no es suyo, App Service implementará el código desde dicho repositorio, pero no podrá configurar la clave SSH y los webhooks necesarios para la implementación continua.
>
>

## <a name="create-vm-sample"></a>Ejemplo de creación de una máquina virtual

[!code-azurecli[principal](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Implementación de una aplicación web en un entorno de ensayo")]

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
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Actualiza un plan de App Service para escalar su plan de tarifa. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | Crea una ranura de implementación. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Asocia una aplicación web con un repositorio GIT o de Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Abre una aplicación web en un explorador. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | Cambia la ranura de implementación especificada a producción. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI para Azure App Service Web Apps en los [ejemplos de la CLI de Azure]().

