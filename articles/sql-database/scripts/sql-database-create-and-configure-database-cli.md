---
title: "Script de la CLI de Azure: creación de una base de datos SQL | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una instancia de SQL Database mediante la CLI de Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 2e65b7a5c44c240c1bc7dea8119c821a7bca2120
ms.lasthandoff: 03/30/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Cree una base de datos SQL única y configure una regla de firewall mediante la CLI de Azure

Este script de ejemplo de CLI crea una instancia de Azure SQL Database y configura una regla de firewall en el nivel de servidor. Después de ejecutar el script correctamente, la base de datos SQL es accesible desde todos los servicios de Azure y la dirección IP configurada. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Creación de una base de datos SQL")]

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
| [az sql server create](/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda la base de datos SQL. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Crea una regla de firewall para permitir el acceso a todas las bases de datos SQL en el servidor desde el intervalo de direcciones IP especificado. |
| [az sql db create](/cli/azure/sql/db#create) | Crea una base de datos SQL en el servidor lógico. |
| [az group delete](/cli/azure/resource#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).


