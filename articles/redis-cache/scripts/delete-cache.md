---
title: "Ejemplo de script de CLI de Azure: Eliminación de Azure Redis Cache | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: Eliminación de Azure Redis Cache"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 11d228cc633fee8c6a2984e2eb23690a43d4c0fc
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---

# <a name="delete-an-azure-redis-cache"></a>Eliminación de una instancia de Azure Redis Cache

En este escenario, aprenderá a eliminar una instancia de Azure Redis Cache.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para eliminar una instancia de Azure Redis Cache. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [eliminación de az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Elimine una instancia de Redis Cache. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI de Azure Redis Cache en la [documentación de Azure Redis Cache](../cli-samples.md).
