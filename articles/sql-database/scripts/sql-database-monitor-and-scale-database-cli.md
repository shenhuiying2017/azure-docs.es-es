---
title: "Script de la CLI de Azure: supervisión y escalado de una instancia única de SQL Database | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: supervisión y escalado de una instancia única de SQL Database mediante la CLI de Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 4cfc4ab09f7adead289cb949373730bffcfa13ec
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-the-azure-cli"></a>Supervisión y escalado de una sola base de datos SQL mediante la CLI de Azure

Este ejemplo de script de la CLI permite escalar una instancia única de Azure SQL Database a un nivel de rendimiento diferente después de consultar la información de tamaño de la base de datos. 

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`. 

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en Windows, consulte [Using the Azure CLI on Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).


## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Supervisión y escalado de una instancia única de SQL Database")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda una base de datos. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | Muestra la información de uso del tamaño de una base de datos. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Actualiza las propiedades de la base de datos (por ejemplo, el nivel de rendimiento o el de servicio), o traslada una base de datos a un grupo elástico, fuera de este o entre grupos elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).

