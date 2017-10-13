---
title: "Creación de un recurso compartido de Azure Files | Microsoft Docs"
description: "Cómo crear un recurso compartido de archivos de Azure en Azure Files mediante Azure Portal, PowerShell y la CLI de Azure."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: bc01e5427f32e9532e39694f6de9f0b1146eda35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-file-share-in-azure-files"></a>Creación de un recurso compartido de archivos en Azure Files
Puede crear recursos compartidos de archivos de Azure mediante [Azure Portal](https://portal.azure.com/), los cmdlets de PowerShell, las bibliotecas de cliente o la API de REST de Azure Storage. En este tutorial, aprenderá a:
* [Crear un recurso compartido de archivos de Azure mediante Azure Portal](#Create file share through the Portal)
* [Crear un recurso compartido de archivos de Azure mediante Powershell](#Create file share using PowerShell)
* [Crear un recurso compartido de archivos de Azure mediante la CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Requisitos previos
Para crear un recurso compartido de archivos de Azure, puede usar una cuenta de almacenamiento que ya exista o [crear una nueva cuenta de Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para crear un recurso compartido de archivos de Azure con PowerShell, necesitará la clave de la cuenta y el nombre de la cuenta de almacenamiento. Necesitará la clave de la cuenta de almacenamiento si tiene previsto usar Powershell o la CLI.

## <a name="create-file-share-through-the-azure-portal"></a>Creación de un recurso compartido de archivos mediante Azure Portal
1. **Vaya a la hoja de la cuenta de almacenamiento en Azure Portal**:    
    ![Hoja de la cuenta de almacenamiento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Haga clic en el botón Agregar recurso compartido de archivos**:    
    ![Haga clic en el botón Agregar recurso compartido de archivos](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Proporcione el nombre y la cuota. Actualmente, la cuota puede llegar a un máximo de 5 TiB**:    
    ![Proporcione el nombre y cuota deseada para el nuevo recurso compartido de archivos](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Ver el nuevo recurso compartido de archivos**: ![ver el nuevo recurso compartido de archivos](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Cargar un archivo**: ![Cargar un archivo](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Explorar el recurso compartido de archivos y administrar los directorios y archivos**: ![Explorar el recurso compartido de archivos](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Creación de un recurso compartido de archivos mediante PowerShell
Para prepararse para usar PowerShell, descargue e instale los cmdlets de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) para obtener instrucciones sobre el punto de instalación y la instalación.

> [!Note]  
> Es recomendable descargar e instalar el módulo más reciente de Azure PowerShell o actualizar a dicho módulo.

1. **Creación de un contexto para la cuenta de almacenamiento y la clave** El contexto encapsula la clave de cuenta y el nombre de la cuenta de almacenamiento. Para obtener instrucciones acerca de cómo copiar la clave de una cuenta desde [Azure Portal](https://portal.azure.com/), consulte [Visualización y copia de las claves de acceso de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Creación de un nuevo recurso compartido de archivos**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> El nombre del recurso compartido de archivos debe estar en minúsculas. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Creación de un recurso compartido de archivos mediante la interfaz de línea de comandos (CLI)
1. **Para prepararse para usar la interfaz de línea de comandos (CLI), descargue e instale la CLI de Azure.**  
    Consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-az-cli2.md) e [Introducción a la CLI de Azure 2.0](/cli/azure/get-started-with-azure-cli.md).

2. **Creación de una cadena de conexión a la cuenta de almacenamiento donde desea crear el recurso compartido.**  
    Reemplace ```<storage-account>``` y ```<resource_group>``` por el nombre de la cuenta de almacenamiento y el grupo de recursos en el siguiente ejemplo:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Creación de un recurso compartido de archivos**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Conectar y montar un recurso compartido de archivos - Windows](storage-how-to-use-files-windows.md)
* [Conectar y montar un recurso compartido de archivos - Linux](../storage-how-to-use-files-linux.md)
* [Conectar y montar un recurso compartido de archivos - macOS](storage-how-to-use-files-mac.md)

Consulte los vínculos siguientes para más información acerca Azure Files.

* [Preguntas más frecuentes](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solución de problemas en Linux](storage-troubleshoot-linux-file-connection-problems.md)   