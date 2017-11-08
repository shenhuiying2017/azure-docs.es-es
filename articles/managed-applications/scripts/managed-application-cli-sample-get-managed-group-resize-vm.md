---
title: "Ejemplo de script de la CLI de Azure: obtención de un grupo de recursos administrados y cambio de tamaño de las máquinas virtuales | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: obtención de un grupo de recursos administrados y cambio de tamaño de las máquinas virtuales"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: c78d2646471e40d60972cf91cb5bbd351f71a66c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obtención de recursos de un grupo de recursos administrados y cambio de tamaño de las máquinas virtuales con la CLI de Azure

Este script recupera los recursos de un grupo de recursos administrados y cambia el tamaño de las máquinas virtuales de ese grupo de recursos.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para implementar la aplicación administrada. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Enumera las aplicaciones administradas. Proporciona valores de consulta que se centran en los resultados. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Enumera los recursos. Proporciona un grupo de recursos y valores de consulta que se centran en el resultado. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Actualiza el tamaño de una máquina virtual. |


## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](../overview.md).
* Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).
