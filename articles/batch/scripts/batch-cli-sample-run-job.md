---
title: "Ejemplo de script de la CLI de Azure - Ejecución de un trabajo con Batch | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure - Ejecución de un trabajo con Batch"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Ejecución de trabajos en Azure Batch con la CLI de Azure

Este script crea un trabajo de Batch y agrega una serie de tareas a dicho trabajo. También muestra cómo supervisar un trabajo y sus tareas.
En la ejecución de este script se da por supuesto que ya se ha configurado una cuenta de Batch, un grupo y una aplicación. Para más información, consulte los [scripts de ejemplo](../batch-cli-samples.md) que tratan sobre cada uno de estos temas.

Si es necesario, instale la CLI de Azure con las instrucciones que se encuentran en la [guía de instalación de CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) y luego ejecute `az login` para iniciar sesión en Azure.

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Ejecutar trabajo")]

## <a name="clean-up-job"></a>Limpieza de un trabajo

Después de ejecutar el script de ejemplo anterior, ejecute el comando siguiente para quitar el trabajo y todas sus tareas. Tenga en cuenta que el grupo debe eliminarse por separado; consulte el [tutorial sobre administración de grupos](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un trabajo de Batch y las tareas. Cada comando de la tabla crea un vínculo a documentación específica de comando.

| Comando | Notas |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Autenticarse en una cuenta de Batch.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Crea un trabajo de Batch.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Actualiza las propiedades de un trabajo de Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | Recupera los detalles de un trabajo de Batch especificado.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | Agrega una tarea al trabajo de Batch especificado.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | Recupera los detalles de una tarea del trabajo de Batch especificado.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).

