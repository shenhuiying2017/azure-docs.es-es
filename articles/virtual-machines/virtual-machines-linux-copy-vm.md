---
title: "Copia de una máquina virtual Linux mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Aprenda a crear una copia de su máquina virtual Linux en el modelo de implementación de Resource Manager mediante la CLI de Azure 2.0 (versión preliminar)."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Creación de una copia de una máquina virtual Linux mediante la CLI de Azure 2.0 (versión preliminar)
En este artículo se muestra cómo crear una copia de su máquina virtual (VM) de Azure con Linux mediante el modelo de implementación de Azure Resource Manager.

Copie los discos del sistema operativo y de datos en un nuevo contenedor y, después, configure los recursos de red y cree la máquina virtual.

También puede [cargar y crear una máquina virtual a partir de una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions"></a>Versiones de la CLI
Puede completar la tarea mediante una de las siguientes versiones de la interfaz de la línea de comandos (CLI):

* [CLI de Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): para los modelos de implementación clásica y de administración de recursos.
* CLI de Azure 2.0 (versión preliminar): la CLI de última generación para el modelo de implementación de administración de recursos, que se trata en este artículo.

## <a name="prerequisites"></a>Requisitos previos
* [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2): se debe tener instalada y haber iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).
* Una máquina virtual de Azure para usarla como el origen de la copia.

## <a name="step-1-stop-the-source-vm"></a>Paso 1: Preparación de la máquina virtual de origen
Desasigne la máquina virtual de origen mediante [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la máquina virtual `myVM` del grupo de recursos `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Paso 2: Copia de la máquina virtual de origen
Para copiar una VM, se crea una copia del disco duro virtual subyacente. Mediante este proceso, se crea una máquina virtual especializada que contiene la misma configuración que la máquina virtual de origen.

El proceso de copia de un disco virtual es diferente según sean discos administrados o sin administrar de Azure. Los discos administrados se administran en la plataforma de Azure y no necesitan preparación ni una ubicación donde almacenarlos. Como los discos administrados son un recurso de nivel superior, es más fácil trabajar con ellos. Es decir, puede realizar una copia directa de los recursos del disco.

Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks).

Según el tipo de almacenamiento de la máquina virtual de origen, siga las instrucciones de las dos secciones siguientes y, a continuación, vaya al "Paso 3: Configuración de una red virtual".

### <a name="managed-disks"></a>Discos administrados

1. Enumere cada máquina virtual y el nombre de su disco administrado del SO con [az vm list](/cli/azure/vm#list). En el ejemplo siguiente se enumeran todas las máquinas virtuales del grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. Para copiar el disco, cree un nuevo disco administrado con [az disk create](/cli/azure/disk#create). En el ejemplo siguiente se crea un disco `myCopiedDisk` a partir del disco administrado `myDisk`:

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. Compruebe ahora los discos administrados del grupo de recursos mediante [az disk list](/cli/azure/disk#list). En el ejemplo siguiente se enumeran los discos administrados del grupo de recursos `myResourceGroup`:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. Vaya al ["Paso 3: Configuración de una red virtual"](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Discos no administrados

1. Para crear una copia de un disco duro virtual, necesita las claves de la cuenta de almacenamiento de Azure y el identificador URI del disco. Para ver las claves de la cuenta de almacenamiento, use [az storage account keys list](/cli/azure/storage/account/keys#list).

 En el ejemplo siguiente se enumeran las claves de la cuenta de almacenamiento `mystorageaccount` del grupo de recursos `myResourceGroup`:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 La salida es similar a la del ejemplo siguiente:

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. Para ver una lista de máquinas virtuales y sus identificadores URI, use [az vm list](/cli/azure/vm#list). En el ejemplo siguiente se enumeran las máquina virtuales del grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. Copie el disco duro virtual mediante [az storage blob copy start](/cli/azure/storage/blob/copy#start). Para proporcionar las claves necesarias de la cuenta de almacenamiento y el identificador URI del disco duro virtual, use la información de las listas `az storage account keys` y `az vm`.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>Paso 3: Configuración de una red virtual
Con los siguientes pasos opcionales se crea una nueva red virtual, una subred, una dirección IP pública y una tarjeta de interfaz de red virtual (NIC).

Si va a copiar una máquina virtual para solucionar problemas o para realizar implementaciones adicionales, puede que no desee usar una máquina virtual de una red virtual existente.

Si desea crear una infraestructura de red virtual para las máquinas virtuales copiadas, siga estos pasos. Si no desea crear una red virtual, vaya al ["Paso 4: Creación de una máquina virtual](#create-a-vm)".

1. Cree la red virtual mediante [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual `myVnet` y una subred `mySubnet`.

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. Cree una IP pública mediante [az network public-ip create](/cli/azure/network/public-ip#create). En el ejemplo siguiente se crea una IP pública `myPublicIP` con el nombre DNS `mypublicdns`. (El nombre DNS debe ser único, así que proporcione un nombre único).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. Cree la NIC mediante [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una NIC `myNic` conectada a la subred `mySubnet`:

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>Paso 4: Creación de una máquina virtual
Ahora puede crear la máquina virtual mediante [az vm create](/cli/azure/vm#create). Al igual que al copiar un disco, el proceso varía ligeramente entre discos administrados y discos sin administrar. Según el tipo de almacenamiento de la máquina virtual de origen, siga las instrucciones de las dos secciones siguientes.

### <a name="managed-disks"></a>Discos administrados
1. Cree una máquina virtual mediante [az vm create](/cli/azure/vm#create).
2. Especifique el disco administrado copiado que usará como disco del SO (`--attach-os-disk`), como se indica a continuación:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>Discos no administrados
1. Cree la VM con [az vm create](/cli/azure/vm#create).
2. Especifique la cuenta de almacenamiento, el nombre del contenedor y el disco duro virtual que usó cuando creó el disco copiado con `az storage blob copy start` (`--image`), de la manera siguiente:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>Pasos siguientes
Para aprender a usar la CLI de Azure para administrar la nueva máquina virtual, consulte [Comandos de la CLI de Azure en el modo de Resource Manager](azure-cli-arm-commands.md).

