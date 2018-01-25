---
title: Ejemplos de la CLI de Azure para Redis Cache | Microsoft Docs
description: Ejemplos de la CLI de Azure para Azure Redis Cache.
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 4450a8f7f16e3503626c9ee0ca125fc2b1ed9052
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Ejemplos de la CLI de Azure para Azure Redis Cache

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con la CLI de Azure.

| | |
|---|---|
|**Create cache**||
| [Creación de una caché](./scripts/create-cache.md) | Crea un grupo de recursos y una instancia de Azure Redis Cache de nivel básico. |
| [Creación de una caché premium con agrupación en clústeres](./scripts/create-premium-cache-cluster.md) | Crea un grupo de recursos y un caché de nivel premium con la agrupación de clústeres habilitada.|
| [Obtención de detalles de caché](./scripts/show-cache.md) | Obtiene los detalles de una instancia de Azure Redis Cache, incluido el estado de aprovisionamiento. |
| [Obtención del nombre de host, los puertos y las claves](./scripts/cache-keys-ports.md) | Obtiene el nombre de host, los puertos y las claves de una instancia de Azure Redis Cache. |
|**Web app plus cache**||
| [Conexión de una aplicación web a Redis Cache](./../app-service/scripts/app-service-cli-app-service-redis.md) | Crea una aplicación web de Azure y una instancia de Redis Cache y, luego, agrega los detalles de conexión de Redis a la configuración de la aplicación. |
|**Delete cache**||
| [Eliminación de una caché](./scripts/delete-cache.md) | Elimina una instancia de Azure Redis Cache  |
| | |

Para más información sobre la CLI de Azure 2.0, consulte [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Introducción a la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).