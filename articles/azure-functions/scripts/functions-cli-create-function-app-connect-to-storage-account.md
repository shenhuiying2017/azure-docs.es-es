---
title: "Creación de una función de Azure que se conecta a una instancia de Azure Storage | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una función de Azure que se conecta a una instancia de Azure Storage"
services: functions
documentationcenter: functions
author: rachelappel
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: af90702601d1bd05836dbf2b20cd3e318832b07c
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Integración de Function App en una cuenta de almacenamiento de Azure

Este script de ejemplo crea una instancia de Function App y una cuenta de almacenamiento.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

Este ejemplo crea una aplicación de Azure Function App y agrega la cadena de conexión de almacenamiento a una configuración de aplicación.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integración de Function App en una cuenta de almacenamiento de Azure")]


## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación de App Service y todos los recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Inicie sesión en Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos con una ubicación. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Crear una cuenta de almacenamiento |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Crea una nueva instancia de Function App. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Limpieza |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Azure Functions en la [documentación de Azure Functions](../functions-cli-samples.md).

