---
title: "Expansión del disco del sistema operativo en la máquina virtual Linux en Azure | Microsoft Docs"
description: "Descripción sobre cómo expandir discos virtuales del sistema operativo en una máquina virtual Linux mediante la CLI de Azure y el modelo de implementación de Resource Manager"
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
ms.date: 11/22/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: c89ce056cb0fa84b2169df161660916d083bec3f
ms.openlocfilehash: bcef9de167fa31001ee4086d7e5b85c2d8bc4613


---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli"></a>Expansión del disco del sistema operativo en una máquina virtual Linux mediante la CLI de Azure
Normalmente, el tamaño predeterminado del disco duro virtual del sistema operativo (SO) es de 30 GB en una máquina virtual Linux de Azure. Puede [agregar discos de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para proporcionar espacio de almacenamiento adicional, pero puede que también desee expandir el disco del sistema operativo. En este artículo se detalla cómo expandir el disco del sistema operativo para una máquina virtual Linux mediante la CLI de Azure.


## <a name="prerequisites"></a>Requisitos previos
Necesita tener instalada la [CLI de Azure más reciente](../xplat-cli-install.md) y haber iniciado sesión en una [cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/)con el modo Resource Manager de la forma siguiente:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup` y `myVM`.


## <a name="expand-os-disk"></a>Expansión del disco del sistema operativo

1. No se pueden realizar operaciones en los discos duros virtuales con la máquina virtual en ejecución. En el ejemplo siguiente se detiene y desasigna la máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup`:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` no libera los recursos de proceso. Para liberar los recursos de proceso, use `azure vm deallocate`. Debe desasignar la máquina virtual para expandir el disco duro virtual.

2. Actualice el tamaño del disco de sistema operativo mediante el comando `azure vm set`. En el ejemplo siguiente se actualiza la máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup` para que tenga `50` GB:

    ```azurecli
    azure vm set --resource-group myResourceGroup --name myVM --new-os-disk-size 50
    ```

3. Inicie la máquina virtual como se indica a continuación:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH en la máquina virtual con las credenciales adecuadas. Para comprobar que se ha cambiado el tamaño de disco del sistema operativo, use `df -h`. La siguiente salida de ejemplo muestra que la partición principal (`/dev/sda1`) ahora es de 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Pasos siguientes
Si necesita almacenamiento adicional, también puede [agregar discos de datos a una máquina virtual Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para más información acerca del cifrado de disco, consulte [Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



<!--HONumber=Nov16_HO4-->


