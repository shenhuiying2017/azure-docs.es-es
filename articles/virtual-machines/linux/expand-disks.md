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
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017

---

# Expansión de discos duros virtuales en una máquina virtual Linux mediante la CLI de Azure
<a id="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli" class="xliff"></a>
Normalmente, el tamaño predeterminado del disco duro virtual del sistema operativo (SO) es de 30 GB en una máquina virtual Linux de Azure. Tiene la opción de [agregar discos de datos](add-disk.md) para proporcionar espacio de almacenamiento adicional, pero puede que también desee expandir el disco del sistema operativo o el disco de datos existente. En este artículo se explica cómo expandir discos administrados para una máquina virtual con Linux mediante la utilización de la CLI de Azure 2.0. También puede expandir el disco del sistema operativo no administrado con la [CLI de Azure 1.0](expand-disks-nodejs.md).

## Expansión del disco
<a id="expand-disk" class="xliff"></a>
Asegúrese de que ha instalado la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

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
    > Si expande un disco administrado, el tamaño actualizado se asigna al tamaño de disco administrado más próximo. Para consultar una tabla de los tamaños y las capas disponibles para discos administrados, vea [Introducción a Azure Managed Disks: precios y facturación](../../storage/storage-managed-disks-overview.md#pricing-and-billing).

3. Inicie la máquina virtual con [az vm start](/cli/azure/vm#start). En el ejemplo siguiente se inicia la máquina virtual llamada *myVM* en el grupo de recursos llamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH en la máquina virtual con las credenciales adecuadas. Para comprobar que se ha cambiado el tamaño de disco del sistema operativo, use `df -h`. La siguiente salida de ejemplo muestra que la unidad de datos (*sdc1/dev/*) es ahora de 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## Pasos siguientes
<a id="next-steps" class="xliff"></a>
Si necesita almacenamiento adicional, también puede [agregar discos de datos a una máquina virtual Linux](add-disk.md). Para más información acerca del cifrado de disco, consulte [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](encrypt-disks.md).

