---
title: "Script de la CLI de Azure: obtención de la cadena de conexión de Azure Cosmos DB para aplicaciones MongoDB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: obtención de la cadena de conexión de Azure Cosmos DB para aplicaciones MongoDB"
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
ms.openlocfilehash: 58e16cfc83c2057168428e8e85a6ebd143d2cd07
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>Obtención de una cadena de conexión de Azure Cosmos DB para aplicaciones MongoDB mediante la CLI de Azure

Este ejemplo obtiene una cadena de conexión de Azure Cosmos DB para aplicaciones MongoDB mediante la CLI de Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "Obtención de la cadena de conexión de Azure Cosmos DB para aplicaciones MongoDB")]

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
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb/list-connection-strings) | Obtiene la cadena de conexión para la cuenta.|
| [az group delete](/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de Azure Cosmos DB en la [documentación de la CLI de Azure Cosmos DB](../cli-samples.md).

