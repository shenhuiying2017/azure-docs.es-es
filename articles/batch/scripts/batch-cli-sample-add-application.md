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
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Adición de aplicaciones a Azure Batch con la CLI de Azure

Este script demuestra cómo configurar una aplicación para usarla con un grupo o tarea de Azure Batch. Para configurar una aplicación, cree un archivo .zip que incluya el ejecutable y cualquier dependencia. En este ejemplo, el archivo zip con el ejecutable se llama "my-application-exe.zip".
Al ejecutar este script, se da por hecho que se ha creado y configurado anteriormente una cuenta de Batch. Para obtener más información, consulte el [script de ejemplo para crear una cuenta de Batch](./batch-cli-sample-create-account.md).

Si es necesario, instale la CLI de Azure con las instrucciones que se encuentran en la [guía de instalación de CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) y luego ejecute `az login` para iniciar sesión en Azure.

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[principal](../../../cli_scripts/batch/add-application/add-application.sh "Agregar aplicación")]

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
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | Crea una aplicación.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | Actualiza las propiedades de una aplicación.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | Agrega un paquete de aplicación a la aplicación especificada.  |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de Batch en la [documentación de la CLI de Azure Batch](../batch-cli-samples.md).

