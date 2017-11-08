---
title: "Ejemplo de script de la CLI de Azure: creación de una definición de aplicación administrada | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una definición de aplicación administrada"
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
ms.openlocfilehash: 6cfc51e5787ad6ab100638d0965b69232cda070a
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2017
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Creación de una definición de aplicación administrada con la CLI de Azure

Este script publica una definición de aplicación administrada en un catálogo de servicios. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Explicación del script

Este script usa el siguiente comando para crear la definición de aplicación administrada. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az_managedapp_definition_create) | Cree una definición de aplicación administrada. Proporcione el paquete que contiene los archivos necesarios. |


## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](../overview.md).
* Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).
