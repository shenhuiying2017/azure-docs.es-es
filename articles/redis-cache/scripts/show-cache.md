---
title: 'Ejemplo de script de CLI de Azure: detalles sobre Azure Redis Cache | Microsoft Docs'
description: 'Ejemplo de script de CLI de Azure: detalles sobre Azure Redis Cache'
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 9f4eb32227bd8a68837eabd58b9d058bc4995d17
ms.lasthandoff: 04/15/2017

---

# <a name="get-details-of-an-azure-redis-cache"></a>Obtenga detalles sobre una instancia de Azure Redis Cache

En este escenario, obtendrá información sobre cómo recuperar los detalles de una instancia de Azure Redis Cache, incluido su estado de aprovisionamiento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para recuperar los detalles de una instancia de Azure Redis Cache. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | Recupera detalles de una instancia de Azure Redis Cache. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI de Azure Redis Cache en la [documentación de Azure Redis Cache](../cli-samples.md).
