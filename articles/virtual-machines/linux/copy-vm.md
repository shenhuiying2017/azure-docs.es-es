---
title: "Copia de una máquina virtual Linux mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Obtenga información sobre cómo crear una copia de la máquina virtual Linux de Azure mediante la CLI de Azure 2.0 y Managed Disks."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 511a3f1b1bc29514baaa3198125d7d236a6101ab
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Creación de una copia de una máquina virtual Linux mediante la CLI de Azure 2.0 y los discos administrados


En este artículo se muestra cómo crear una copia de su máquina virtual de Azure con Linux mediante el modelo de implementación de Azure Resource Manager y la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

También puede [cargar y crear una máquina virtual a partir de un VHD](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>requisitos previos


-   Instalar la [CLI de Azure 2.0](/cli/azure/install-az-cli2)

-   Iniciar sesión en una cuenta de Azure con [az login](/cli/azure/reference-index#az_login).

-   Tener una máquina virtual de Azure como el origen de la copia.

## <a name="step-1-stop-the-source-vm"></a>Paso 1: Preparación de la máquina virtual de origen


Desasigne la máquina virtual de origen mediante [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
En el ejemplo siguiente se desasigna la máquina virtual denominada "**myVM**" en el grupo de recursos **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Paso 2: Copia de la máquina virtual de origen


Para copiar una VM, se crea una copia del disco duro virtual subyacente. Mediante este proceso, se crea un VHD especializado como disco administrado que contiene la misma configuración que la máquina virtual de origen.

Para más información acerca de Azure Managed Disks, consulte [Introducción a los discos administrados de Azure](../windows/managed-disks-overview.md). 

1.  Enumere cada máquina virtual y el nombre de su disco del SO con [az vm list](/cli/azure/vm#az_vm_list). En el ejemplo siguiente se enumeran todas las VM del grupo de recursos denominado **myResourceGroup**:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copie el disco mediante la creación de un disco administrado con [az disk create](/cli/azure/disk#az_disk_create). En el ejemplo siguiente se crea un disco llamado "**myCopiedDisk**" a partir del disco administrado **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Compruebe ahora los discos administrados del grupo de recursos mediante [az disk list](/cli/azure/disk#az_disk_list). En el ejemplo siguiente se enumeran todos los discos administrados del grupo de recursos **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Paso 3: Configuración de una red virtual


Con los siguientes pasos opcionales se crea una nueva red virtual, una subred, una dirección IP pública y una tarjeta de interfaz de red virtual (NIC).

Si va a copiar una máquina virtual para solucionar problemas o para realizar implementaciones adicionales, puede que no desee usar una máquina virtual de una red virtual existente.

Si desea crear una infraestructura de red virtual para las máquinas virtuales copiadas, siga estos pasos. Si no desea crear una red virtual, vaya a [Paso 4: Creación de una máquina virtual](#step-4-create-a-vm).

1.  Cree la red virtual mediante [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual denominada **myVnet** y una subred **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Cree una IP pública mediante [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). En el ejemplo siguiente se crea una IP pública denominada "**myPublicIP**" con el nombre DNS **mypublicdns**. (El nombre DNS debe ser único, así que proporcione un nombre único).

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Cree la NIC mediante [az network nic create](/cli/azure/network/nic#az_network_nic_create).
    En el ejemplo siguiente se crea una NIC **myNic** conectada a la subred **mySubnet**:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Paso 4: Creación de una máquina virtual

Ahora puede crear la máquina virtual mediante [az vm create](/cli/azure/vm#az_vm_create).

Especifique el disco administrado copiado que usará como disco del SO (--attach-os-disk), como se indica a continuación:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Pasos siguientes

Para aprender a usar la CLI de Azure para administrar la nueva máquina virtual, consulte [Comandos de la CLI de Azure en el modo de Azure Resource Manager](../azure-cli-arm-commands.md).
