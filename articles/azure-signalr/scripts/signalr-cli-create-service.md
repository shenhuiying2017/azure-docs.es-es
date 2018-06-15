---
title: 'Ejemplo de script de la CLI de Azure: creación de una instancia de SignalR Service | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: creación de una instancia de SignalR Service'
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
ms.openlocfilehash: a8df62fe229a627b2e551ab04528ad601c5d5d2a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766434"
---
# <a name="create-a-signalr-service"></a>Creación de una instancia de SignalR Service 

Este script de ejemplo crea un nuevo recurso de Azure SignalR Service en un nuevo grupo de recursos con un nombre aleatorio.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este artículo es preciso que ejecute la versión 2.0 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

Este script usa la extensión *signalr* con la CLI de Azure. Ejecute el siguiente comando para instalar la extensión *signalr* para la CLI de Azure antes de usar este script de ejemplo:

```azurecli-interactive
az extension add -n signalr
```

Este script crea un nuevo recurso de SignalR Service y un nuevo grupo de recursos. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Anote el nombre real generado para el nuevo grupo de recursos. Usará ese nombre de grupo de recursos cuando quiera eliminar todos los recursos del grupo.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Cada comando de la tabla crea un vínculo a documentación específica del comando. Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az signalr create](/cli/azure/group#az-group-create) | Crea un recurso de Azure SignalR Service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Muestra las claves, que usará la aplicación al insertar actualizaciones de contenido en tiempo real con SignalR. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Se pueden encontrar ejemplos de scripts adicionales de la CLI de Azure SignalR Service en la [documentación de Azure SignalR Service](../signalr-cli-samples.md).
