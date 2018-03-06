---
title: "Inicio rápido de Azure: Carga, descarga y enumeración de blobs de Azure Storage mediante la CLI de Azure | Microsoft Docs"
description: "En esta guía de inicio rápido, puede usar la CLI de Azure para crear una cuenta de almacenamiento y un contenedor. Después, puede usar la CLI de Azure para cargar un blob en Azure Storage, descargar un blob o enumerar los blobs de un contenedor."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 2c567f06ca501799c895448c54955c8858705611
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Inicio rápido: Carga, descarga y enumeración de blobs mediante la CLI de Azure

La CLI de Azure es la forma de usar la línea de comandos de Azure para administrar los recursos de Azure. Puede utilizarlo en el explorador con Azure Cloud Shell. También puede instalarla en macOS, Linux o Windows, y ejecutarla desde la línea de comandos. En esta guía de inicio rápido aprenderá a usar la CLI de Azure para cargar y descargar datos en Azure Blob Storage y desde este.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para determinar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan en un contenedor. Puede organizar los grupos de blobs de una forma similar a la que organiza los archivos en carpetas en el equipo.

Cree un contenedor para almacenar blobs con el comando [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Carga de un blob

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. La mayoría de los archivos almacenados en Blob Storage se almacenan como blobs en bloques. Los blobs en anexos se usan cuando los datos deben agregarse a un blob existente sin modificar el contenido que ya tiene, como, por ejemplo, para realizar operaciones de registro. Los blobs en páginas respaldan los archivos VHD de máquinas virtuales IaaS.

En primer lugar, cree un archivo para cargarlo en un blob.
Si usa Azure Cloud Shell, para crear un archivo use `vi helloworld` cuando el archivo se abra, presione **Insertar**, escriba "Hola mundo" y, a continuación, presione **Esc**, escriba `:x` y presione **Entrar**.

En este ejemplo, se carga un blob en el contenedor que se creó en el último paso con el comando [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Si usó el método descrito anteriormente para crear un archivo en Azure Cloud Shell, puede usar este comando CLI en su lugar (tenga en cuenta que no tuvo que especificar una ruta de acceso porque el archivo se creó en el directorio base, pero normalmente se debe especificar una ruta de acceso):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

Esta operación crea el blob si todavía no existe y lo sobrescribe si ya existe. Cargue tantos archivos como desee antes de continuar.

Para cargar varios archivos al mismo tiempo, puede usar el comando [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch).

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs del contenedor con el comando [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Descarga de un blob

Use el comando [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) para descargar el blob que cargó anteriormente.

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

Si ya no necesita ninguno de los recursos del grupo de recursos, incluida la cuenta de almacenamiento que creó en esta guía de inicio rápido, elimine el grupo de recursos con el comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y un contenedor en Azure Blob Storage. Para más información sobre cómo trabajar con blobs en Azure Storage, continúe con el tutorial para trabajar con Azure Blob Storage.

> [!div class="nextstepaction"]
> [Operaciones de Blob Storage con la CLI de Azure](storage-how-to-use-blobs-cli.md)
