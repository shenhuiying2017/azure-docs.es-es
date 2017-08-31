---
title: "Ejemplo de script de CLI de Azure: creación de Azure Redis Cache premium con agrupación en clústeres | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: Creación de Azure Redis Cache de nivel premium con agrupación en clústeres"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 87d0fe4c3eaa8f7b75343a36a069ecdac8241d74
ms.contentlocale: es-es
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Creación de una caché en Azure Redis Cache premium con agrupación en clústeres

En este escenario, aprenderá cómo crear Azure Redis Cache de nivel Premium de 6 GB con la agrupación en clústeres habilitada y dos particiones.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos y una caché en Redis de nivel premium con la habilitación de agrupación en clústeres. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#create) | Crea una instancia de Redis Cache. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI de Azure Redis Cache en la [documentación de Azure Redis Cache](../cli-samples.md).
