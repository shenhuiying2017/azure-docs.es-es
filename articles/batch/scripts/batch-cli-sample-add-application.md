---
title: "Ejemplo de script de la CLI de Azure: adición de una aplicación a Batch | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: adición de una aplicación a Batch"
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
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Adición de aplicaciones a Azure Batch con la CLI de Azure

Este script demuestra cómo configurar una aplicación para usarla con un grupo o tarea de Azure Batch. Para configurar una aplicación, cree un archivo .zip que incluya el ejecutable y cualquier dependencia. En este ejemplo, el archivo zip con el ejecutable se llama "my-application-exe.zip".

## <a name="prerequisites"></a>Requisitos previos

- Instale la CLI de Azure con las instrucciones que se encuentran en la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) si aún no lo ha hecho.
- Cree una cuenta de Batch si aún no tiene una. Consulte [Creación de una cuenta de Batch con la CLI de Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para ver un script de ejemplo que crea una cuenta.

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>Aplicación de limpieza

Después de ejecutar el script de muestra anterior, ejecute los comandos siguientes para eliminar la aplicación y todos los paquetes de aplicación cargados.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los comandos siguientes para crear una aplicación y cargar el paquete de aplicación.
Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Crea una aplicación.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Actualiza las propiedades de una aplicación.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Agrega un paquete de aplicación a la aplicación especificada.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).
