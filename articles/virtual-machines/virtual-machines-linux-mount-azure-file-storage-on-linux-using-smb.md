---
title: "Montaje de Azure File Storage en máquinas virtuales Linux con SMB | Microsoft Docs"
description: "Procedimiento para montar Azure File Storage en máquinas virtuales Linux con SMB y la CLI de Azure 2.0 (versión preliminar)"
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
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>Montaje de Azure File Storage en máquinas virtuales Linux con SMB y la CLI de Azure 2.0 (versión preliminar)

En este artículo se explica cómo usar el servicio Azure File Storage en una máquina virtual Linux con un montaje de SMB. El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar.  Los requisitos son:

- [una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/);

- [archivos de clave SSH pública y privada](virtual-machines-linux-mac-create-ssh-keys.md).


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](#quick-commands): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)


## <a name="quick-commands"></a>Comandos rápidos

Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos necesarios. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Requisitos previos: grupo de recursos, VNet, NSG con entrada de SSH, subred, cuenta de Azure Storage, claves de cuenta de Azure Storage, recurso de Azure File Storage y una máquina virtual Linux. Reemplace los ejemplos por sus propios valores.

Cree un directorio para el montaje local de esta forma:

```bash
mkdir -p /mnt/mymountpoint
```

Monte el recurso compartido de SMB de Azure File Storage en el punto de montaje de esta forma:

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Para conservar el montaje tras un reinicio, agregue una línea a `/etc/fstab` de esta forma:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Tutorial detallado

El almacenamiento de archivos de Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. Y con la última versión de File Storage, es posible también montar un recurso compartido de archivos desde cualquier sistema operativo que sea compatible con SMB 3.0. El uso de un montaje de SMB en Linux permite que se realicen copias de seguridad fáciles en una ubicación de almacenamiento de archivado permanente y sólida que se proporciona mediante un Acuerdo de Nivel de Servicio.  

El movimiento de los archivos de una máquina virtual a un montaje de SMB hospedado en Azure File Storage es una buena forma de depurar registros a medida que el mismo recurso de SMB puede montarse localmente en la estación de trabajo de Mac, Linux o Windows. SMB no sería la mejor solución para transmitir registros de aplicación o Linux en tiempo real, puesto que el protocolo SMB no está creado para el registro de controles tan pesados. Una herramienta de nivel de registro unificado dedicada como Fluentd sería una opción mejor a través de SMB para recopilar la salida de registro de la aplicación y Linux.

Para este tutorial detallado, creamos los requisitos previos necesarios para crear primero el recurso compartido de Azure File Storage y, a continuación, montarlo a través de SMB en una máquina virtual Linux.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create) para que contenga nuestro recurso compartido de archivos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>Creación de una cuenta de Azure Storage
Después, cree una cuenta de almacenamiento con [az storage account create](/cli/azure/storage/account#create) para almacenar los archivos reales. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada "`mystorageaccount`" con el SKU de almacenamiento `Standard_LRS`:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>Visualización de las claves de cuenta de almacenamiento

Las claves de la cuenta de Azure Storage se crean en parejas, cuando se crea la cuenta de almacenamiento. Las claves de cuenta de almacenamiento se crean en pares de modo que las claves se pueden girar sin ninguna interrupción del servicio. Una vez que gira las claves a la segunda clave en el par, cree un nuevo par de claves. Las nuevas claves de cuenta de almacenamiento se crean siempre en pares, lo que garantiza que siempre tiene al menos una clave de almacenamiento sin usar lista para girar.

Use [az storage account keys list](/cli/azure/storage/account/keys#list) para ver las claves de la cuenta de almacenamiento. En el ejemplo siguiente se enumeran las claves de cuenta de almacenamiento de la cuenta de almacenamiento denominada "`mystorageaccount`":

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


## <a name="create-the-azure-file-storage-share"></a>Creación del recurso compartido de Azure File Storage

Cree el recurso compartido de File Storage que contiene el recurso compartido de SMB con [az storage share create](/cli/azure/storage/share#create). La cuota es siempre en GigaBytes (GB). Pase una de las claves del comando anterior **az storage account keys list**. En el ejemplo siguiente se crea un recurso compartido denominado "`mystorageshare`" con una cuota de `10` GB:

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Crear el directorio de punto de montaje

Se necesita un directorio local en el sistema de archivos de Linux en el que montar el recurso compartido de SMB. No se envía nada escrito o leído desde el directorio de montaje local al recurso compartido de SMB hospedado en Azure File Storage. Con el ejemplo siguiente se puede crear un directorio local en `/mnt/mymountdirectory`:

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montaje del recurso compartido de SMB
Monte el recurso compartido de SMB en el directorio local que cree de esta forma. Indique el nombre de usuario y la clave de su cuenta de almacenamiento en las credenciales de montaje de esta forma:

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Conservación del montaje de SMB a través de reinicios

Una vez que se reinicia la máquina virtual de Linux, el recurso compartido de SMB montado se desmonta durante el cierre. Para volver a montar el recurso compartido de SMB en el inicio, debe agregar una línea a `/etc/fstab` de Linux. Linux utiliza el archivo `fstab` para enumerar los sistemas de archivos que necesita montar durante el arranque. Agregue el recurso compartido de SMB para garantizar que el recurso compartido de Azure File Storage es un sistema de archivos montado de manera permanente para la máquina virtual Linux. La adición del recurso compartido de SMB de Azure File Storage a una nueva máquina virtual es posible con `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de cloud-init para personalizar una VM de Linux durante la creación](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

