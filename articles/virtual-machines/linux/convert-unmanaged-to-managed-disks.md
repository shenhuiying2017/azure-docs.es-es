---
title: "Conversión de una VM con Linux en Azure de Unmanaged Disks a Managed Disks | Microsoft Docs"
description: "Conversión de una máquina virtual de Unmanaged Disks a Managed Disks mediante la CLI de Azure 2.0 en el modelo de implementación de Resource Manager"
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
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 37c47061b0774d9670b9a2d304d069d5f683c2d2
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Conversión de una VM con Linux de Unmanaged Disks a Azure Managed Disks

Si ya dispone de máquinas virtuales (VM) con Linux que usan Unmanaged Disks, puede convertirlas para usar [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Este proceso convierte el disco del SO y los discos de datos conectados.

En este artículo se muestra cómo convertir máquinas virtuales con la CLI de Azure. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Conversión de máquinas virtuales de instancia única
En esta sección se explica cómo convertir máquinas virtuales de Azure de instancia única de Unmanaged Disks a Managed Disks. (Vea la sección siguiente si las máquinas virtuales se encuentran en un conjunto de disponibilidad). Puede usar este proceso para realizar conversiones de discos no administrados premium (SDD) a discos administrados premium, o bien de discos no administrados estándar (HDD) a discos administrados estándar.

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

## <a name="convert-vms-in-an-availability-set"></a>Conversión de VM de un conjunto de disponibilidad

Si las VM que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

Todas las VM del conjunto de disponibilidad deben desasignarse antes de convertir el conjunto de disponibilidad. Planee la conversión de todas las VM a discos administrados después de haber convertido el propio conjunto de disponibilidad en un conjunto de disponibilidad administrado. Después, inicie todas las máquinas virtuales y siga trabajando con normalidad.

1. Enumere todas las VM de un conjunto de disponibilidad con [az vm availability-set list](/cli/azure/vm/availability-set#list). En el ejemplo siguiente se enumeran todas las VM del conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desasigne todas las VM con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convierta el conjunto de disponibilidad con [az vm availability-set convert](/cli/azure/vm/availability-set#convert). En el ejemplo siguiente se convierte el conjunto de disponibilidad `myAvailabilitySet` en el grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
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

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks y Azure Storage Service Encryption
No puede usar los pasos anteriores para convertir un disco no administrado en uno administrado si el disco no administrado se encuentra en una cuenta de Storage cifrada con [Azure Storage Service Encryption](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Los siguientes pasos explican cómo copiar y usar Unmanaged Disks que han estado en una cuenta de almacenamiento cifrada:

1. Copie el disco duro virtual (VHD) con [az storage blob copy start](/cli/azure/storage/blob/copy#start) en una cuenta de almacenamiento que nunca se ha habilitado para el Cifrado del servicio Azure Storage.

2. Use la VM copiada de alguna de las formas siguientes:

* Cree una VM que use Unmanaged Disks y especifique el archivo de VHD durante la creación con [az vm create](/cli/azure/vm#create).

* O conecte el VHD copiado con [az vm disk attach](/cli/azure/vm/disk#attach) a una VM en ejecución con discos administrados.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las opciones de almacenamiento, vea [Introducción a Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

