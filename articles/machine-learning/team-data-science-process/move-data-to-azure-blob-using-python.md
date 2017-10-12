---
title: Mover datos hacia y desde Azure Blob Storage con Python | Microsoft Docs
description: Mover datos hacia y desde el almacenamiento de blobs de Azure con Python
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: d7847f695a77ad469f56a20518cb979c41384d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Movimiento de datos hacia y desde Azure Blob Storage de Azure con Python
En este tema se describe cómo enumerar, cargar y descargar blobs usando la API de Python. Con la API de Python proporcionada en el SDK de Azure, puede:

* Crear un contenedor
* Cargar un blob en un contenedor
* Descargar blobs
* Enumerar los blobs de un contenedor
* Eliminar un blob

Para obtener más información sobre el uso de la API de Python, consulte [Uso del servicio de almacenamiento de blobs desde Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](virtual-machines.md), AzCopy ya está instalado en la máquina virtual.
> 
> [!NOTE]
> Para ver una introducción completa a Azure Blob Storage, consulte [Aspectos básicos de Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) y [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

* Para configurar una suscripción a Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md).

## <a name="upload-data-to-blob"></a>Cargar datos en blob
Agregue el siguiente fragmento de código cerca de la parte superior de cualquier código de Python en el que desee obtener acceso mediante programación al almacenamiento de Azure:

    from azure.storage.blob import BlobService

El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto BlobService utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: Reemplace el nombre de la cuenta y la clave de cuenta por la cuenta real y la clave.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use los siguientes métodos para cargar datos en un blob:

1. put\_block\_blob\_from\_path (carga el contenido de un archivo desde la ruta especificada)
2. put\_block_blob\_from\_file (carga el contenido de una secuencia o archivo ya abierto)
3. put\_block\_blob\_from\_bytes (carga una matriz de bytes)
4. put\_block\_blob\_from\_text (carga el valor de texto especificado con la codificación especificada)

El siguiente código de ejemplo carga un archivo local en un contenedor:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo carga todos los archivos (excepto los directorios) en un directorio local para el almacenamiento de blobs:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Descargar datos del blob
Utilice los siguientes métodos para descargar datos de un blob:

1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

Estos métodos que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

El código de ejemplo siguiente descarga el contenido de un blob de un contenedor en un archivo local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo descarga todos los blobs de un contenedor. Usa list\_blobs para obtener la lista de blobs disponibles del contenedor y los descarga en un directorio local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
