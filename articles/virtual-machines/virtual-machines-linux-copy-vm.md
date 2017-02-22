---
title: "Copia de una VM con Linux mediante la CLI de Azure 2.0 (versión preliminar) | Microsoft Docs"
description: "Obtenga información sobre cómo crear una copia de una máquina virtual con Linux en el modelo de implementación de Resource Manager mediante la CLI de Azure 2.0 (versión preliminar)"
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Creación de una copia de una máquina virtual con Linux mediante la CLI de Azure 2.0 (versión preliminar)
En este artículo se muestra cómo crear una copia de una máquina virtual de Azure con Linux con el modelo de implementación de Resource Manager. Primero copie los discos del sistema operativo y de datos en un nuevo contenedor y, después, configure los recursos de red y cree la máquina virtual.

También puede [cargar y crear una máquina virtual a partir de una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- CLI de Azure 2.0 (versión preliminar): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)

## <a name="prerequisites"></a>Requisitos previos
- Necesita tener instalada la última versión de la [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/#login).
- Necesita una VM de Azure que se usará como el origen cuando se crea una copia.

## <a name="stop-the-vm"></a>Parada de la máquina virtual
Desasigne la VM de origen con [az vm deallocate](/cli/azure/vm#deallocate). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>Copia de la VM
Para copiar una VM, se crea una copia del disco duro virtual subyacente. Este proceso permite crear una VM especializada que contiene la configuración y los mismos ajustes que la VM de origen. El proceso para copiar el disco virtual es diferente entre discos no administrados y Azure Managed Disks. Los discos administrados se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Habida cuenta de que los discos administrados son un recurso de nivel superior, resulta más fácil trabajar con ellos; puede realizar una copia directa del recurso de disco. Para más información sobre Azure Managed Disks, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Introducción a Azure Managed Disks). Elija uno de los siguientes pasos adecuados para el tipo de almacenamiento de la VM de origen:

- [Discos administrados](#managed-disks)
- [Discos no administrados](#unmanaged-disks) 

### <a name="managed-disks"></a>Discos administrados
Enumere cada VM y el nombre de su disco administrado del SO con [az vm list](/cli/azure/vm#list). En el ejemplo siguiente se enumeran todas las VM del grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

La salida es similar a la del ejemplo siguiente:

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

Copie el disco mediante la creación de un disco administrado con [az disk create](/cli/azure/disk#create). En el ejemplo siguiente se crea un disco llamado `myCopiedDisk` a partir del disco administrado denominado `myDisk`:

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

Verifique los discos administrados que se encuentran actualmente en el grupo de recursos con [az disk list](/cli/azure/disk#list). En el ejemplo siguiente se enumeran todos los discos administrados del grupo de recursos denominado `myResourceGroup`:

```azurecli
az disk list --resource-group myResourceGroup --output table
```

Mover a la sección sobre la [configuración y la revisión de la configuración de la red virtual](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Discos no administrados
Para crear una copia de un VHD, necesitará las claves de la cuenta de almacenamiento y el identificador URI del disco. Use [az storage account keys list](/cli/azure/storage/account/keys#list) para ver las claves de la cuenta de almacenamiento. En el ejemplo siguiente se enumeran las claves de la cuenta de almacenamiento denominada `mystorageaccount` en el grupo de recursos llamado `myResourceGroup`

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

Use [az vm list](/cli/azure/vm#list) para ver una lista de las VM y sus identificadores URI. En el ejemplo siguiente se enumeran las VM del grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

La salida es similar a la del ejemplo siguiente:

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

Copie el VHD con [az storage blob copy start](/cli/azure/storage/blob/copy#start). Utilice la información de **az storage account keys list** y **az vm list** para proporcionar las claves de la cuenta de almacenamiento necesarias y el identificador URI del VHD.

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>Configuración de la red virtual
Con los pasos siguientes se crea una red virtual, una subred, una dirección IP pública y una tarjeta de interfaz de red virtual (NIC). Estos pasos son opcionales. Si va a copiar una VM para solucionar problemas o para realizar implementaciones adicionales, puede que no desee usar una VM en una red virtual existente. Si desea crear una infraestructura de red virtual para las VM copiadas, siga los pasos siguientes; de lo contrario, pase a [Crear una VM](#create-a-vm).

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#create). En el ejemplo siguiente se crea una red virtual denominada `myVnet` y una subred llamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Cree una dirección IP pública con [az network public-ip create](/cli/azure/network/public-ip#create). En el ejemplo siguiente se crea una IP pública denominada `myPublicIP` con el nombre DNS `mypublicdns`. El nombre DNS debe ser único, así que indique su propio nombre único.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Cree la tarjeta de interfaz de red (NIC) con [az network nic create](/cli/azure/network/nic#create). En el ejemplo siguiente se crea una tarjeta de interfaz de red denominada `myNic` conectada a la subred `mySubnet`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>Crear una VM
Ahora puede crear la VM con [az vm create](/cli/azure/vm#create). Como sucede con la copia del disco, el proceso difiere ligeramente entre los discos administrados y los discos no administrados. Cree una VM con uno de los siguientes comandos adecuados.

### <a name="managed-disks"></a>Discos administrados
Cree la VM con [az vm create](/cli/azure/vm#create). Especifique el disco copiado administrado que se usará como el disco del SO (`--attach-os-disk`) como se indica a continuación:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>Discos no administrados
Cree la VM con [az vm create](/cli/azure/vm#create). Especifique la cuenta de almacenamiento, el nombre de contenedor y el VHD utilizado para crear la copia con **az storage blob copy start** (`--image`), como se indica a continuación:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo utilizar la CLI de Azure para administrar una máquina virtual nueva, consulte [Comandos de la CLI de Azure en el modo de Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


