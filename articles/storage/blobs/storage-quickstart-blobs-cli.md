---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante la CLI de Azure | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante la CLI de Azure"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: marsma
ms.openlocfilehash: c9b7e7a1fbc6b67821183ce31bdf2527de490c92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Transferencia de objetos a y desde Azure Blob Storage mediante la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Este tutorial de inicio rápido detalla el uso de la CLI de Azure para cargar y descargar datos a y desde el Azure Blob Storage.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan a un contenedor. Los contenedores permiten organizar grupos de blobs de la misma forma que se organizan archivos en directorios en el equipo.

Cree un contenedor para almacenar blobs con el comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Carga de un blob

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. La mayoría de los archivos almacenados en Blob Storage se almacenan como blobs en bloques. Los blobs en anexos se usan cuando los datos deben agregarse a un blob existente sin modificar el contenido que ya tiene, como, por ejemplo, para realizar operaciones de registro. Los blobs en páginas respaldan los archivos VHD de máquinas virtuales IaaS.

En este ejemplo, se carga un blob en el contenedor que creamos en el último paso con el comando [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Esta operación crea el blob si todavía no existe y lo sobrescribe si ya existe. Cargue tantos archivos como desee antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs del contenedor con el comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Descarga de un blob

Use el comando [az storage blob download](/cli/azure/storage/blob#download) para descargar un blob que cargó anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferencia de datos con AzCopy

La utilidad [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) es otra opción para la transferencia de datos que permite ejecutar scripts de alto rendimiento para Azure Storage. Puede utilizar AzCopy para transferir datos a y desde Blob Storage, File Storage y Table Storage.

A modo de un ejemplo rápido, aquí está el comando de AzCopy para cargar un archivo denominado *myfile.txt* al contenedor *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida la cuenta de almacenamiento que creó en esta guía de inicio rápido, elimine el grupo de recursos con el comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y un contenedor en Azure Blob Storage. Para más información sobre cómo trabajar con blobs en Azure Storage, continúe con el tutorial para trabajar con Azure Blob Storage.

> [!div class="nextstepaction"]
> [Operaciones de Blob Storage con la CLI de Azure](storage-how-to-use-blobs-cli.md)
