---
title: Montaje de Azure File Storage en VM Linux mediante SMB con la CLI de Azure 1.0 | Microsoft Docs
description: Montaje de Azure File Storage en VM Linux con SMB
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
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 4951860630f0aad107d0846d52ebe4423ee0b91c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montaje de Azure File Storage en VM Linux mediante SMB con la CLI de Azure 1.0

En este artículo se muestra cómo montar Azure File Storage en una VM Linux mediante el protocolo Bloque de mensajes del servidor (SMB). El almacenamiento de archivos ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. Los requisitos son:

* Una [cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Archivos de clave pública y privada Secure Shell (SSH)](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Versiones CLI para usar
Puede completar la tarea mediante una de las siguientes versiones de la interfaz de la línea de comandos (CLI):

- [CLI de Azure 1.0](#quick-commands): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI de última generación para el modelo de implementación de Resource Manager


## <a name="quick-commands"></a>Comandos rápidos
Para realizar la tarea rápidamente, siga los pasos descritos en esta sección. Para más información y contexto, comience en la sección ["Tutorial detallado"](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

### <a name="prerequisites"></a>Requisitos previos
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
Agregue la línea siguiente a `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El almacenamiento de archivos ofrece recursos compartidos de archivos en la nube que usan el protocolo SMB estándar. Con la versión más reciente de File Storage, también es posible montar un recurso compartido de archivos desde cualquier SO que sea compatible con SMB 3.0. Cuando se usa un montaje de SMB en Linux se obtienen copias de seguridad fáciles en una ubicación de almacenamiento de archivado permanente y sólida que se proporciona mediante un Acuerdo de Nivel de Servicio.

El movimiento de archivos de una VM a un montaje de SMB que se hospeda en File Storage es una excelente manera de depurar registros. Eso es porque se puede montar el mismo recurso compartido SMB localmente en la estación de trabajo de Windows, Linux o Mac. SMB no es la mejor solución para transmitir registros de aplicación o Linux en tiempo real porque el protocolo SMB no está creado para controlar tareas de registro tan pesadas. Una herramienta de nivel de registro unificado dedicada como Fluentd sería una opción mejor que SMB para recopilar la salida de registro de la aplicación y Linux.

Para este tutorial detallado, desarrollamos los requisitos previos necesarios para crear primero el recurso compartido de File Storage y luego montarlo a través de SMB en una VM Linux.

1. Cree una cuenta de almacenamiento de Azure mediante el código siguiente:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Muestre las claves de cuenta de almacenamiento.

    Cuando cree una cuenta de almacenamiento, las claves de cuenta de almacenamiento se crean en pares de modo que las claves se pueden girar sin ninguna interrupción del servicio. Cuando cambia a la segunda clave del par, se crea un nuevo par de claves. Las nuevas claves de cuenta de almacenamiento se crean siempre en pares, lo que garantiza que siempre tiene al menos una clave de almacenamiento sin usar lista para cambiar. Para mostrar las claves de cuenta de almacenamiento, utilice el código siguiente:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Cree el recurso compartido de File Storage.

    El recurso compartido de File Storage contiene el recurso compartido de SMB. La cuota siempre se expresa en gigabytes (GB). Para crear el recurso compartido de File Storage, use el código siguiente:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Cree el directorio de punto de montaje.

    Debe crear un directorio local en el sistema de archivos de Linux en el que montar el recurso compartido de SMB. No se envía nada escrito o leído desde el directorio de montaje local al recurso compartido de SMB hospedado en File Storage. Para crear el directorio, use el código siguiente:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Monte el recurso compartido de SMB mediante el código siguiente:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Conserve el montaje de SMB a través de reinicios.

    Cuando reinicie la VM de Linux, el recurso compartido de SMB montado se desmontará durante el cierre. Para volver a montar el recurso compartido de SMB en el arranque, debe agregar una línea a /etc/fstab de Linux. Linux utiliza el archivo fstab para enumerar los sistemas de archivos que necesita montar durante el proceso de arranque. Agregue el recurso compartido de SMB para garantizar que el recurso compartido de File Storage es un sistema de archivos montado de manera permanente para la VM Linux. La adición del recurso compartido de SMB de File Storage a una nueva VM es posible cuando se usa cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de cloud-init para personalizar una VM de Linux durante la creación](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adición de un disco a una máquina virtual de Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Cifrado de discos en una VM Linux mediante la CLI de Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
