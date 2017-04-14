---
title: "Conversión de una VM con Linux en Azure de discos no administrados a discos administrados | Microsoft Docs"
description: "Procedimiento para convertir una máquina virtual de discos no administrados a discos administrados de Azure mediante la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8429ee543d8cf838765ff9e8624390746474ee0d
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Cómo convertir una VM con Linux de discos no administrados a Azure Managed Disks

Si tiene VM existentes con Linux en Azure que usan discos no administrados en las cuentas de almacenamiento y desea que esas VM puedan beneficiarse de las ventajas de los discos administrados, puede convertir las VM. Este proceso convierte el disco del SO y los discos de datos conectados. El proceso de conversión requiere reiniciar la VM, por lo que debe programar la migración de las VM durante una ventana de mantenimiento existente previamente. El proceso de migración es irreversible. Asegúrese de probar el proceso de migración con la migración de una máquina virtual de prueba antes de realizar la migración en producción.

> [!IMPORTANT]
> Durante la conversión, se desasigna la VM. La VM recibe una nueva dirección IP cuando se inicia después de la conversión. Si tiene una dependencia en una dirección IP fija, use una dirección IP reservada.

No puede convertir un disco no administrado en un disco administrado si el disco no administrado se encuentra en una cuenta de almacenamiento que está, o en algún momento ha estado, cifrada con el [Cifrado del servicio Azure Storage (SSE)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Los siguientes pasos explican cómo convertir discos no administrados que están, o han estado, en una cuenta de almacenamiento cifrada:

- Copie el disco duro virtual (VHD) con [az storage blob copy start](/cli/azure/storage/blob/copy#start) en una cuenta de almacenamiento que nunca se ha habilitado para el Cifrado del servicio Azure Storage.
- Cree una VM que use discos administrados y especifique el archivo de VHD durante la creación con [az vm create](/cli/azure/vm#create).
- O conecte el VHD copiado con [az vm disk attach](/cli/azure/vm/disk#attach) a una VM en ejecución con discos administrados.

## <a name="convert-vm-to-azure-managed-disks"></a>Conversión de VM a Azure Managed Disks
En esta sección se explica cómo convertir las VM de Azure existentes de discos no administrados a discos administrados. Puede utilizar este proceso para realizar conversiones de discos no administrados premium (SDD) a discos administrados premium, o bien de discos no administrados estándar (HDD) a discos administrados estándar.

> [!IMPORTANT]
> Después de realizar el procedimiento siguiente, hay un blob en bloques único que permanece en el contenedor de VHD predeterminado. El nombre del archivo es "VMName.xxxxxxx.status". No elimine este objeto de estado de mantenimiento restante. En los futuros trabajos se debe abordar este problema.

1. Desasigne la VM con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convierta la VM en discos administrados con [az vm convert](/cli/azure/vm#convert). El proceso siguiente convierte la VM denominada `myVM`, incluidos el disco del SO y todos los discos de datos:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie la VM después de la conversión a discos administrados con [az vm start](/cli/azure/vm#start). En el ejemplo siguiente se inicia la VM llamada `myVM` en el grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>Conversión de VM de un conjunto de disponibilidad en discos administrados

Si las VM que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

Todas las VM del conjunto de disponibilidad deben desasignarse antes de convertir el conjunto de disponibilidad. Planee la conversión de todas las VM a discos administrados después de haber convertido el propio conjunto de disponibilidad en un conjunto de disponibilidad administrado. A continuación, puede iniciar todas las VM y seguir trabajando con normalidad.

1. Enumere todas las VM de un conjunto de disponibilidad con [az vm availability-set list](/cli/azure/vm/availability-set#list). En el ejemplo siguiente se enumeran todas las VM del conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. Desasigne todas las VM con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convierta el conjunto de disponibilidad con [az vm availability-set convert](/cli/azure/vm/availability-set#convert). En el ejemplo siguiente se convierte el conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convierta todas las VM a discos administrados con [az vm convert](/cli/azure/vm#convert). El proceso siguiente convierte la VM denominada `myVM`, incluidos el disco del SO y todos los discos de datos:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas las VM después de la conversión a discos administrados con [az vm start](/cli/azure/vm#start). En el ejemplo siguiente se inicia la VM llamada `myVM` en el grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las opciones de almacenamiento, vea [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks).

