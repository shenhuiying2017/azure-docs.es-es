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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5fe1e3595d9459e60b2fd54d6f17f6822731f453
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Ejecución de trabajos en Azure Batch con la CLI de Azure

Este script crea un trabajo de Batch y agrega una serie de tareas a dicho trabajo. También muestra cómo supervisar un trabajo y sus tareas. Finalmente, muestra cómo consultar el servicio Batch de forma eficaz para obtener información sobre las tareas del trabajo.

## <a name="prerequisites"></a>Requisitos previos

- Instale la CLI de Azure con las instrucciones que se encuentran en la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) si aún no lo ha hecho.
- Cree una cuenta de Batch si aún no tiene una. Consulte [Creación de una cuenta de Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para ver un script de ejemplo que crea una cuenta.
- Configure una aplicación para que se ejecute desde una tarea de inicio si aún no lo ha hecho. Consulte [Adición de aplicaciones a Azure Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) para ver un script de ejemplo que crea una aplicación y carga un paquete de aplicación en Azure.
- Configure un grupo en el que se ejecutará el trabajo. Consulte [Administración de grupos de Azure Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) para ver un script de ejemplo que crea un grupo con una configuración de servicios en la nube o de máquinas virtuales.

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Ejecutar trabajo")]

## <a name="clean-up-job"></a>Limpieza de un trabajo

Después de ejecutar el script de ejemplo anterior, ejecute el comando siguiente para quitar el trabajo y todas sus tareas. Tenga en cuenta que el grupo debe eliminarse por separado. Consulte [Administración de grupos de Azure Batch con la CLI de Azure](./batch-cli-sample-manage-pool.md) para obtener más información sobre cómo crear y eliminar grupos.

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
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#list) | Enumera las tareas asociadas con el trabajo especificado.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).

