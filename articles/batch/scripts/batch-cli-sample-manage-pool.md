---
title: "Ejemplo de script de la CLI de Azure: administración de grupos en Batch | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: administración de grupos en Batch"
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
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Administración de grupos de Azure Batch con la CLI de Azure

Estos scripts muestran algunas de las herramientas disponibles en la CLI de Azure para crear y administrar grupos de nodos de ejecución en el servicio de Azure Batch.

> [!NOTE]
> Los comandos de este ejemplo crean máquinas virtuales de Azure. La ejecución de máquinas virtuales acumulará cargos en su cuenta. Para minimizarlos, elimine las máquinas virtuales cuando termine de ejecutar el ejemplo. Consulte el artículo sobre cómo [limpiar los grupos](#clean-up-pools).

Los grupos de Batch se pueden configurar de dos formas, ya sea con una configuración de Cloud Services (solo Windows) o una configuración de Virtual Machine (Windows y Linux). Los scripts de ejemplo siguientes muestran cómo crear grupos con ambas configuraciones.

## <a name="prerequisites"></a>Requisitos previos

- Instale la CLI de Azure con las instrucciones que se encuentran en la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) si aún no lo ha hecho.
- Cree una cuenta de Batch si aún no tiene una. Consulte [Creación de una cuenta de Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para ver un script de ejemplo que crea una cuenta.
- Configure una aplicación para que se ejecute desde una tarea de inicio si aún no lo ha hecho. Consulte [Adición de aplicaciones a Azure Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) para ver un script de ejemplo que crea una aplicación y carga un paquete de aplicación en Azure.

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Grupo con script de ejemplo de configuración de Cloud Services

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Grupo con script de ejemplo de configuración de Virtual Machine

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

## <a name="clean-up-pools"></a>Limpieza de grupos

Después de ejecutar el script de ejemplo anterior, ejecute el comando siguiente para eliminar los grupos.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para crear grupos de Batch y manipularlos.
Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Autenticarse en una cuenta de Batch.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Enumerar las aplicaciones disponibles en la cuenta de Batch.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Crear un grupo de máquinas virtuales.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Actualizar propiedades de un grupo.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Enumerar la información disponible de imagen y SKU de agente del nodo.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Cambiar la cantidad de máquinas virtuales en ejecución en el grupo especificado.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Mostrar las propiedades de un grupo.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Eliminar el grupo especificado.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Habilitar el escalado automático de un grupo y aplicar una fórmula.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Deshabilitar el escalado automático de un grupo.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Enumerar todos los nodos de ejecución del grupo especificado.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Reiniciar el nodo de ejecución especificado.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Eliminar los nodos de la lista del grupo especificado.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).

