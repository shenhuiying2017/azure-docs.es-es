---
title: "Script de la CLI de Azure: creación de una cuenta, base de datos y colección de API de MongoDB para Azure Cosmos DB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una cuenta, base de datos y colección de API de MongoDB para Azure Cosmos DB"
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
ms.topic: article
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a9408683a577ed068e553ed975995c289dc82f7f
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="azure-cosmos-db-create-an-mongodb-api-account-using-the-azure-cli"></a>Azure Cosmos DB: creación de una cuenta de API de MongoDB mediante la CLI de Azure

Este script de ejemplo de la CLI crea una cuenta, base de datos y colección de API de MongoDB en Azure Cosmos DB. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[principal](../../../cli_scripts/cosmosdb/create-cosmosdb-mongodb-account/create-cosmosdb-mongodb-account.sh?highlight=15-35 "Creación de una cuenta, base de datos y colección de API de MongoDB para Azure Cosmos DB")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#create) | Crea una cuenta de Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en la [documentación de la CLI de Azure Cosmos DB](../cli-samples.md).

