---
title: 'Ejemplo de script de la CLI de Azure: creación de una instancia de SignalR Service con una instancia de App Service | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: creación de una instancia de SignalR Service con una instancia de App Service'
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6ac1646da4c952c78bfb787b0d6ab30f4876f36a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766400"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Creación de una instancia de SignalR Service con una instancia de App Service

Este script de ejemplo crea un nuevo recurso de Azure SignalR Service, que se utiliza para insertar las actualizaciones de contenido en tiempo real en los clientes. Este script también agrega una nueva aplicación web y un nuevo plan de App Service para hospedar la aplicación web de ASP.NET que usa la instancia de SignalR Service. La aplicación web se configura con una configuración de aplicación denominada *AzureSignalRConnectionString* para conectar con el nuevo recurso de SignalR Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

Este script usa la extensión *signalr* con la CLI de Azure. Ejecute el siguiente comando para instalar la extensión *signalr* para la CLI de Azure antes de usar este script de ejemplo:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Anote el nombre real generado para el nuevo grupo de recursos. Se mostrará en la salida. Usará ese nombre de grupo de recursos cuando quiera eliminar todos los recursos del grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Cada comando de la tabla crea un vínculo a documentación específica del comando. Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Crea un recurso de Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Muestra las claves, que usará la aplicación al insertar actualizaciones de contenido en tiempo real con SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Crea un plan de Azure App Service para hospedar aplicaciones web. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Crea una aplicación web de Azure mediante el plan de hospedaje de App Service. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Agrega una nueva configuración de aplicación para la aplicación web. Esta configuración de aplicación se usa para almacenar la cadena de conexión de SignalR. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Se pueden encontrar ejemplos de scripts adicionales de la CLI de Azure SignalR Service en la [documentación de Azure SignalR Service](../signalr-cli-samples.md).
