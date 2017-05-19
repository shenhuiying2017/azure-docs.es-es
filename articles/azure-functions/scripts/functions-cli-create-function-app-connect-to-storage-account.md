---
title: "Creación de una función de Azure que se conecta a una instancia de Azure DocumentDB | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una función de Azure que se conecta a una instancia de Azure DocumentDB"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a34793b6b16cce4031e9e871d8e3b143180593ae
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="integrate-function-app-into-azure-storage-account"></a>Integración de Function App en una cuenta de almacenamiento de Azure

Este script de ejemplo crea una instancia de Function App y una cuenta de almacenamiento con recursos relacionados. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo 

Creación de una aplicación de ejemplo

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integración de Function App en una cuenta de almacenamiento de Azure")]


## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación de App Service y todos los recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Inicie sesión en Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos con una ubicación. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Crear una cuenta de almacenamiento |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Crea una nueva instancia de Function App. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Limpieza |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de CLI para Azure Functions en la [documentación de Azure Functions](../functions-cli-samples.md).
