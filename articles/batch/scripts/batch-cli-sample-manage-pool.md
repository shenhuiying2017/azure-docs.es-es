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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 2556b02459886390b803407c5cb828687229a44e
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

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

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Administración de grupos de Cloud Services")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Grupo con script de ejemplo de configuración de Virtual Machine

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Administración de grupos de Virtual Machine")]

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
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Autenticarse en una cuenta de Batch.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | Enumerar las aplicaciones disponibles en la cuenta de Batch.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | Crear un grupo de máquinas virtuales.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | Actualizar propiedades de un grupo.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | Enumerar la información disponible de imagen y SKU de agente del nodo.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | Cambiar la cantidad de máquinas virtuales en ejecución en el grupo especificado.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | Mostrar las propiedades de un grupo.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | Eliminar el grupo especificado.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | Habilitar el escalado automático de un grupo y aplicar una fórmula.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | Deshabilitar el escalado automático de un grupo.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | Enumerar todos los nodos de ejecución del grupo especificado.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | Reiniciar el nodo de ejecución especificado.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | Eliminar los nodos de la lista del grupo especificado.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).


