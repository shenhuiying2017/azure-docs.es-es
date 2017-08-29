---
title: "Expansión de discos duros virtuales en máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo expandir discos duros virtuales en una máquina virtual con Linux mediante la CLI de Azure 2.0."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b82cc0473c003da767ee230ab485c69b233977d1
ms.contentlocale: es-es
ms.lasthandoff: 08/23/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expansión de discos duros virtuales en una máquina virtual Linux mediante la CLI de Azure
Normalmente, el tamaño predeterminado del disco duro virtual del sistema operativo (SO) es de 30 GB en una máquina virtual Linux de Azure. Tiene la opción de [agregar discos de datos](add-disk.md) para proporcionar espacio de almacenamiento adicional, pero puede que también desee expandir un disco de datos existente. En este artículo se explica cómo expandir discos administrados para una máquina virtual con Linux mediante la utilización de la CLI de Azure 2.0. También puede expandir el disco del sistema operativo no administrado con la [CLI de Azure 1.0](expand-disks-nodejs.md).

> [!WARNING]
> Asegúrese siempre de realizar una copia de seguridad de los datos antes de cambiar el tamaño de los discos. Para más información, consulte [Copia de seguridad de máquinas virtuales Linux en Azure](tutorial-backup-vms.md).

## <a name="expand-disk"></a>Expansión del disco
Asegúrese de que ha instalado la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En este artículo se requiere una máquina virtual existente en Azure con al menos un disco de datos adjunto y preparado. Si no dispone de una máquina virtual que pueda usar, consulte la sección sobre la [creación y preparación de máquinas virtuales con discos de datos](tutorial-manage-disks.md#create-and-attach-disks).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen *myResourceGroup* y *myVM*.

1. No se pueden realizar operaciones en los discos duros virtuales con la máquina virtual en ejecución. Desasigne la máquina virtual con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada "*myVM*" en el grupo de recursos *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` no libera los recursos de proceso. Para liberar los recursos de proceso, use `az vm deallocate`. Debe desasignar la máquina virtual para expandir el disco duro virtual.

2. Vea la lista de discos administrados de un grupo de recursos con [az disk list](/cli/azure/disk#list). En el ejemplo siguiente se muestra una lista de discos administrados del grupo de recursos denominado *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda el disco necesario con [az disk update](/cli/azure/disk#update). En el ejemplo siguiente se expande el disco administrado llamado *myDataDisk* para cambiar su tamaño a *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Si expande un disco administrado, el tamaño actualizado se asigna al tamaño de disco administrado más próximo. Para consultar una tabla de los tamaños y las capas disponibles para discos administrados, vea [Introducción a Azure Managed Disks: precios y facturación](../windows/managed-disks-overview.md#pricing-and-billing).

3. Inicie la máquina virtual con [az vm start](/cli/azure/vm#start). En el ejemplo siguiente se inicia la máquina virtual llamada *myVM* en el grupo de recursos llamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH en la máquina virtual con las credenciales adecuadas. Puede obtener la dirección IP pública de la máquina virtual con [az vm show](/cli/azure/vm#show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

5. Para usar el disco ampliado, necesita expandir la partición y el sistema de archivos subyacentes.

    a. Si está montado, desmonte el disco:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Use `parted` para ver la información de disco y cambiar el tamaño de la partición:

    ```bash
    sudo parted /dev/sdc
    ```

    Consulte la información del diseño de partición existente con `print`. El resultado es similar al ejemplo siguiente, que muestra que el disco subyacente de 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expanda la partición con `resizepart`. Escriba el número de partición, *1*, y un tamaño para la nueva partición:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para salir, escriba `quit`

5. Una vez cambiado el tamaño de la partición, compruebe la coherencia de esta con `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

6. Ahora cambie el tamaño del sistema de archivos con `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

7. Monte la partición en la ubicación deseada, por ejemplo, `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

8. Para comprobar que se ha cambiado el tamaño de disco del sistema operativo, use `df -h`. La siguiente salida de ejemplo muestra que la unidad de datos, */dev/sdc1*, es ahora de 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Pasos siguientes
Si necesita almacenamiento adicional, también puede [agregar discos de datos a una máquina virtual Linux](add-disk.md). Para más información acerca del cifrado de disco, consulte [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](encrypt-disks.md).

