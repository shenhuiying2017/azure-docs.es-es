---
title: "Ejemplo de script de la CLI de Azure: creación de una aplicación web ASP.NET Core en un contenedor de Docker desde Azure Container Registry | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una aplicación web ASP.NET Core en un contenedor de Docker desde Azure Container Registry"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: ec524ed8dd4cc58b948d3047c36a9f7913d7d041
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Creación de una aplicación web de ASP.NET Core en un contenedor de Docker desde Azure Container Registry

En este escenario aprenderá cómo crear un grupo de recursos, el plan de servicio de aplicaciones de Linux y la aplicación web, y cómo implementar una aplicación de ASP.NET Core utilizando un contenedor de Docker desde Azure Container Registry.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="create-app-sample"></a>Creación de una aplicación de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Azure Container Registry de Linux")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crea una aplicación web de Azure en el plan de App Service. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | Establece el contenedor de Docker para la aplicación web de Azure. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).

