---
title: "Script de la CLI de Azure: creación de un firewall para Azure Cosmos DB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de un firewall para Azure Cosmos DB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sammvcple
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 51f61901e1b1615e48582690dea701a01a56ebca
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="azure-cosmos-db-create-a-firewall-using-the-azure-cli"></a>Azure Cosmos DB: creación de un firewall con la CLI de Azure

Este script de ejemplo de la CLI crea una directiva de firewall para cualquier tipo de cuenta de Azure Cosmos DB. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Creación de un firewall de Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#create) | Crea una cuenta de Azure Cosmos DB. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#update) | Actualiza una cuenta de Azure Cosmos DB para incluir la configuración del firewall. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en la [documentación de la CLI de Azure Cosmos DB](../cli-samples.md).

