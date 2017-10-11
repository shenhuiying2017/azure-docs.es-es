---
title: "Montaje de Azure File Storage en máquinas virtuales Linux con SMB | Microsoft Docs"
description: "Procedimientos de montaje de Azure File Storage en máquinas virtuales Linux mediante SMB con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: 9eae17b304f8a987b44ebed8906dabd8ff3a36a8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montaje de Azure File Storage en máquinas virtuales Linux con SMB

En este artículo se muestra cómo usar el servicio Azure File Storage en una máquina virtual Linux mediante un montaje de SMB con la CLI de Azure 2.0. El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);
- [archivos de clave SSH pública y privada](mac-create-ssh-keys.md).

## <a name="quick-commands"></a>Comandos rápidos

* Un grupo de recursos
* Una red virtual de Azure
* Un grupo de seguridad de red con un SSH entrante
* Una subred
* Una cuenta de almacenamiento de Azure
* Claves de cuenta de almacenamiento de Azure
* Un recurso compartido de Azure File Storage
* Una VM Linux

Reemplace los ejemplos por su propia configuración.

### <a name="create-a-directory-for-the-local-mount"></a>Creación de un directorio para el montaje local

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montaje del recurso compartido de SMB de File Storage en el punto de montaje

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Conserve el montaje tras un reinicio
Para ello, agregue la siguiente línea a `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El almacenamiento de archivos ofrece recursos compartidos de archivos en la nube que usan el protocolo SMB estándar. Con la versión más reciente de File Storage, también es posible montar un recurso compartido de archivos desde cualquier SO que sea compatible con SMB 3.0. Cuando se usa un montaje de SMB en Linux se obtienen copias de seguridad fáciles en una ubicación de almacenamiento de archivado permanente y sólida que se proporciona mediante un Acuerdo de Nivel de Servicio.

El movimiento de archivos de una VM a un montaje de SMB que se hospeda en File Storage es una excelente manera de depurar registros. Eso es porque se puede montar el mismo recurso compartido SMB localmente en la estación de trabajo de Windows, Linux o Mac. SMB no es la mejor solución para transmitir registros de aplicación o Linux en tiempo real porque el protocolo SMB no está creado para controlar tareas de registro tan pesadas. Una herramienta de nivel de registro unificado dedicada como Fluentd sería una opción mejor que SMB para recopilar la salida de registro de la aplicación y Linux.

Para este tutorial detallado, desarrollamos los requisitos previos necesarios para crear primero el recurso compartido de File Storage y luego montarlo a través de SMB en una VM Linux.

1. Cree un grupo de recursos con [az group create](/cli/azure/group#create) para que contenga el recurso compartido de archivos.

    Para crear un grupo de recursos llamado `myResourceGroup` en la ubicación "Oeste de EE. UU.", use el ejemplo siguiente:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Cree una cuenta de almacenamiento de Azure con [az storage account create](/cli/azure/storage/account#create) para almacenar los archivos reales.

    Para crear una cuenta de almacenamiento llamada mystorageaccount mediante la SKU de almacenamiento Standard_LRS, use el ejemplo siguiente:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Muestre las claves de cuenta de almacenamiento.

    Cuando cree una cuenta de almacenamiento, las claves de cuenta de almacenamiento se crean en pares de modo que las claves se pueden girar sin ninguna interrupción del servicio. Cuando cambia a la segunda clave del par, se crea un nuevo par de claves. Las nuevas claves de la cuenta de almacenamiento se crean siempre por pares, lo que garantiza que siempre tenga al menos una clave de cuenta de almacenamiento sin usar lista para cambiar.

    Vea las claves de la cuenta de almacenamiento con [az storage account keys list](/cli/azure/storage/account/keys#list). Las claves de la cuenta de almacenamiento de la denominada `mystorageaccount` se muestran en el ejemplo siguiente:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Para extraer una sola clave, use la marca `--query`. En el ejemplo siguiente se extrae la primera clave (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Cree el recurso compartido de File Storage.

    El recurso compartido de File Storage contiene el recurso compartido de SMB con [az storage share create](/cli/azure/storage/share#create). La cuota siempre se expresa en gigabytes (GB). Pase una de las claves desde el comando `az storage account keys list` anterior. Cree un recurso compartido llamado mystorageshare con una cuota de 10 GB mediante el siguiente ejemplo:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Cree un directorio de punto de montaje.

    Cree un directorio local en el sistema de archivos de Linux en el que montar el recurso compartido de SMB. No se envía nada escrito o leído desde el directorio de montaje local al recurso compartido de SMB hospedado en File Storage. Para crear un directorio local en /mnt/mymountdirectory, use el ejemplo siguiente:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Monte el recurso compartido de SMB en el directorio local.

    Indique el nombre de usuario y la clave de su cuenta de almacenamiento en las credenciales de montaje de esta forma:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Conserve el montaje de SMB a través de reinicios.

    Cuando reinicie la VM de Linux, el recurso compartido de SMB montado se desmontará durante el cierre. Para volver a montar el recurso compartido de SMB al inicio, agregue una línea a /etc/fstab de Linux. Linux utiliza el archivo fstab para enumerar los sistemas de archivos que necesita montar durante el proceso de arranque. Agregue el recurso compartido de SMB para garantizar que el recurso compartido de File Storage es un sistema de archivos montado de manera permanente para la VM Linux. La adición del recurso compartido de SMB de File Storage a una nueva VM es posible cuando se usa cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de cloud-init para personalizar una VM de Linux durante la creación](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adición de un disco a una máquina virtual de Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Cifrado de discos en una VM Linux mediante la CLI de Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
