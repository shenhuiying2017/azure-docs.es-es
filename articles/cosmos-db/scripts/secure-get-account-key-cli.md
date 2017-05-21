---
title: "Script de la CLI de Azure: obtención de claves de cuenta para Azure Cosmos DB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: obtención de claves de cuenta para Azure Cosmos DB"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 21da79a674a55fad910b0b059508acc026f8bd84
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Obtención de claves de cuenta para Azure Cosmos DB mediante la CLI de Azure

Este ejemplo obtiene claves de cuenta para cualquier tipo de cuenta de Azure Cosmos DB.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Obtención de claves de cuenta de Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Actualiza una cuenta de Azure Cosmos DB. |
| [az cosmosdb list-keys](/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda la base de datos SQL. |
| [az group delete](/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en la [documentación de la CLI de Azure Cosmos DB](../cli-samples.md).

