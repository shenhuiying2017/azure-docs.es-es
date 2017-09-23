---
title: "Aumento del tamaño de una máquina virtual Linux con la CLI de Azure 1.0 | Microsoft Docs"
description: "Descripción sobre cómo expandir discos virtuales del sistema operativo en una máquina virtual Linux mediante la CLI 1.0 de Azure y el modelo de implementación de Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 0aedcd70b54c2ed47ec327ccf0529a48351353c0
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017

---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Expansión del disco del sistema operativo en una máquina virtual Linux mediante la CLI de Azure 1.0
Normalmente, el tamaño predeterminado del disco duro virtual del sistema operativo (SO) es de 30 GB en una máquina virtual Linux de Azure. Puede [agregar discos de datos](add-disk.md) para proporcionar espacio de almacenamiento adicional, pero puede que también desee expandir el disco del sistema operativo. En este artículo se detalla cómo expandir el disco del sistema operativo para una máquina virtual Linux mediante discos no administrados con la CLI de Azure 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](#prerequisites): la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0](expand-disks.md): la CLI de última generación para el modelo de implementación de administración de recursos

## <a name="prerequisites"></a>Requisitos previos
Necesita tener instalada la [CLI de Azure 1.0 más reciente](../../cli-install-nodejs.md) y haber iniciado sesión en una [cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/) con el modo Resource Manager de la forma siguiente:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen *myResourceGroup* y *myVM*.

## <a name="expand-os-disk"></a>Expansión del disco del sistema operativo

1. No se pueden realizar operaciones en los discos duros virtuales con la máquina virtual en ejecución. En el ejemplo siguiente se detiene y desasigna la máquina virtual llamada *myV* en el grupo de recursos llamado *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` no libera los recursos de proceso. Para liberar los recursos de proceso, use `azure vm deallocate`. Debe desasignar la máquina virtual para expandir el disco duro virtual.

2. Actualice el tamaño del disco no administrado de sistema operativo mediante el comando `azure vm set`. En el ejemplo siguiente se desasigna la máquina virtual denominada *myVM* del grupo de recursos llamado *myResourceGroup* para que tenga *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Inicie la máquina virtual como se indica a continuación:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH en la máquina virtual con las credenciales adecuadas. Para comprobar que se ha cambiado el tamaño de disco del sistema operativo, use `df -h`. La siguiente salida de ejemplo muestra que la partición principal (*/dev/sda1*) es ahora de 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Pasos siguientes
Si necesita almacenamiento adicional, también puede [agregar discos de datos a una máquina virtual Linux](add-disk.md). Para más información acerca del cifrado de disco, consulte [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](encrypt-disks.md).

