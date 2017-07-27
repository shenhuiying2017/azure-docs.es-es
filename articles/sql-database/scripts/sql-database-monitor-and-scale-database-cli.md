---
title: Ejemplo de la CLI para supervisar y escalar una instancia de Azure SQL Database | Microsoft Docs
description: Script de ejemplo de la CLI de Azure para supervisar y escalar una instancia de Azure SQL Database
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 01911b85268244a8fddb32aa726f8a870abbaf77
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Uso de la CLI para supervisar y escalar una instancia de SQL Database

Este ejemplo de script de la CLI de Azure escala una sola instancia de Azure SQL Database a un nivel de rendimiento distinto después de consultar la información del tamaño de la base de datos. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Supervisión y escalado de una instancia única de SQL Database")]

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
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un servidor lógico que hospeda una base de datos. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | Muestra la información de uso del tamaño de una base de datos. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Actualiza las propiedades de la base de datos (por ejemplo, el nivel de rendimiento o el de servicio), o traslada una base de datos a un grupo elástico, fuera de este o entre grupos elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de SQL Database en la [documentación de Azure SQL Database](../sql-database-cli-samples.md).

