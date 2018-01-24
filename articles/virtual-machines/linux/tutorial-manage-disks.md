---
title: "Administración de discos de Azure con la CLI de Azure | Microsoft Docs"
description: "Tutorial: Administración de discos de Azure con la CLI de Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 16cc0c5e38eb273fc2504a39497d00c76d666316
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Administración de discos de Azure con la CLI de Azure

Las máquinas virtuales de Azure usan discos para almacenar el sistema operativo, las aplicaciones y los datos de máquinas virtuales. Al crear una máquina virtual es importante elegir un tamaño de disco y la configuración adecuada para la carga de trabajo esperada. Este tutorial trata la implementación y administración de discos de máquina virtual. Aprenderá sobre los siguientes temas:

> [!div class="checklist"]
> * Discos del SO y temporales
> * Discos de datos
> * Discos Estándar y Premium
> * Rendimiento de disco
> * Conectar y preparar los discos de datos
> * Cambiar el tamaño de los discos
> * Instantáneas de disco


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Discos de Azure predeterminados

Cuando se crea una máquina virtual de Azure, se conectan dos discos automáticamente a la máquina virtual. 

**Disco del sistema operativo**: hospedan el sistema operativo de las máquinas virtuales. Se puede cambiar su tamaño hasta 1 terabyte. El disco del sistema operativo lleva de forma predeterminada la etiqueta */dev/sda* . La configuración de almacenamiento en caché del disco del sistema operativo está optimizada para el rendimiento del sistema operativo. Debido a esta configuración, el disco del sistema operativo **no debe** hospedar aplicaciones o datos. Para aplicaciones y datos, use discos de datos, que se detallan más adelante en este artículo. 

**Disco temporal**: los discos temporales usan una unidad de estado sólido que se encuentra en el mismo host de Azure que la máquina virtual. Los discos temporales son muy eficiente y se pueden usar para operaciones tales como el procesamiento temporal de los datos. Sin embargo, si la máquina virtual se mueve a un nuevo host, los datos almacenados en un disco temporal se eliminarán. El tamaño del disco temporal se determina por el tamaño de la máquina virtual. Los discos temporales llevan la etiqueta */dev/sdb* y tienen un punto de montaje de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamaños de disco temporal

| type | Tamaño de VM | Tamaño máximo de disco temporal (GB) |
|----|----|----|
| [Uso general](sizes-general.md) | Series A y D | 800 |
| [Proceso optimizado](sizes-compute.md) | Serie F | 800 |
| [Memoria optimizada](../virtual-machines-windows-sizes-memory.md) | Series D y G | 6144 |
| [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md) | Serie L | 5630 |
| [GPU](sizes-gpu.md) | Serie N | 1440 |
| [Alto rendimiento](sizes-hpc.md) | Series A y H | 2000 |

## <a name="azure-data-disks"></a>Discos de datos de Azure

Se pueden agregar discos de datos adicionales para instalar aplicaciones y almacenar datos. Los discos de datos deben usarse en cualquier situación donde desee un almacenamiento de datos duradero y con capacidad de respuesta. Cada disco de datos tiene una capacidad máxima de 1 terabyte. El tamaño de la máquina virtual determina cuántos discos de datos se pueden conectar a una máquina virtual. Para cada vCPU de la máquina virtual, se pueden asociar dos discos de datos. 

### <a name="max-data-disks-per-vm"></a>Discos de datos máximos por máquina virtual

| type | Tamaño de VM | Discos de datos máximos por máquina virtual |
|----|----|----|
| [Uso general](sizes-general.md) | Series A y D | 32 |
| [Proceso optimizado](sizes-compute.md) | Serie F | 32 |
| [Memoria optimizada](../virtual-machines-windows-sizes-memory.md) | Series D y G | 64 |
| [Almacenamiento optimizado](../virtual-machines-windows-sizes-storage.md) | Serie L | 64 |
| [GPU](sizes-gpu.md) | Serie N | 48 |
| [Alto rendimiento](sizes-hpc.md) | Series A y H | 32 |

## <a name="vm-disk-types"></a>Tipos de disco de máquina virtual

Azure proporciona dos tipos de disco.

### <a name="standard-disk"></a>Disco estándar

Standard Storage está respaldado por unidades de disco duro y ofrece un almacenamiento rentable al mismo tiempo que tiene un rendimiento superior. Los discos estándar son ideales para cargas de trabajo de desarrollo y prueba rentables.

### <a name="premium-disk"></a>Disco Premium

Los discos Premium están respaldados por un disco de latencia reducida y alto rendimiento basado en SSD. Es perfecto para máquinas virtuales que ejecutan cargas de trabajo de producción. Premium Storage es compatible con las máquinas virtuales de las series DS, DSv2, GS y FS. Los discos Premium incluyen 3 tipos (P10, P20 y P30); el tamaño del disco determina el tipo de disco. Al seleccionar el tamaño de un disco, el valor se redondea al siguiente tipo. Por ejemplo, si el tamaño del disco es inferior a 128 GB, el tipo de disco es P10. Si el tamaño de disco está entre 129 y 512 GB, el tamaño es un P20. Cualquier tamaño por encima de 512 GB equivale a un tipo P30.

### <a name="premium-disk-performance"></a>Rendimiento del disco Premium

|Tipo de disco de Premium Storage | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamaño del disco (redondeo hacia arriba) | 128 GB | 512 GB | 1024 GB (1 TB) |
| Máximo de IOPS por disco | 500 | 2,300 | 5.000 |
Rendimiento de disco | 100 MB/s | 150 MB/s | 200 MB/s |

Aunque la tabla anterior identifica las IOPS máximas por disco, se puede obtener un mayor nivel de rendimiento dividiendo varios discos de datos. Por ejemplo, una máquina virtual Standard_GS5 puede conseguir 80 000 IOPS como máximo. Para más información sobre el número máximo de IOPS por máquina virtual, consulte los [tamaños de máquinas virtuales Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Creación y conexión de discos

Los discos de datos se pueden crear y conectar en el momento de creación de la máquina virtual o a una máquina virtual existente.

### <a name="attach-disk-at-vm-creation"></a>Conexión del disco en el momento de creación de la máquina virtual

Cree un grupo de recursos con el comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Cree una máquina virtual mediante el comando [az vm create]( /cli/azure/vm#create). El argumento `--datadisk-sizes-gb` se usa para especificar que se debe crear y conectar un disco adicional a la máquina virtual. Para crear y conectar más de un disco, use una lista delimitada por espacios de valores de tamaño de disco. En el ejemplo siguiente, se crea una máquina virtual con dos discos de datos, ambos de 128 GB. Dado que los tamaños de disco son de 128 GB, estos discos se configuran ambos como P10s, que proporcionan el número máximo de 500 IOPS por disco.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Conexión del disco a máquina virtual existente

Para crear y conectar un nuevo disco a una máquina virtual existente, use el comando [az vm disk attach](/cli/azure/vm/disk#attach). En el ejemplo siguiente se crea un disco Premium con un tamaño de 128 gigabytes y se conecta a la máquina virtual que creó en el último paso.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Preparación de los discos de datos

Después de que se ha conectado un disco a la máquina virtual, es necesario configurar el sistema operativo para usar el disco. En el ejemplo siguiente se muestra cómo configurar manualmente un disco. Este proceso también se puede automatizar mediante cloud-init, que se trata en un [tutorial posterior](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuración manual

Cree una conexión SSH con la máquina virtual. Reemplace la dirección IP de ejemplo por la dirección IP pública de la máquina virtual:

```azurecli-interactive 
ssh 52.174.34.95
```

Cree particiones del disco con `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Escriba un sistema de archivos en la partición con el comando `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte el disco nuevo para que sea accesible en el sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Ahora se puede acceder al disco mediante el punto de montaje *datadrive*, que se puede comprobar con el comando `df -h`. 

```bash
df -h
```

El resultado muestra la nueva unidad montada en */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para asegurarse de que la unidad se vuelve a montar tras un reinicio, se debe agregar al archivo */etc/fstab*. Para ello, obtenga el UUID del disco con la utilidad `blkid`.

```bash
sudo -i blkid
```

El resultado muestra el UUID de la unidad, en este caso `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Agregue una línea similar a la siguiente al archivo */etc/fstab*. Tenga en cuenta también que las barreras de escritura puede deshabilitarse mediante *barrier=0*; esta configuración puede mejorar el rendimiento del disco. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Ahora que se ha configurado el disco, cierre la sesión de SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Cambio del tamaño de disco de una máquina virtual

Una vez que se ha implementado una máquina virtual, se puede aumentar el tamaño del disco del sistema operativo o de los discos de datos conectados. Aumentar el tamaño del disco resulta beneficioso cuando se necesita más espacio de almacenamiento o un nivel más alto de rendimiento (P10, P20, P30). Cabe mencionar que no se puede reducir el tamaño de los discos.

Antes de aumentar el tamaño del disco, se necesita el identificador o el nombre del disco. Use el comando [az disk list](/cli/azure/disk#az_disk_list) para devolver todos los discos de un grupo de recursos. Anote el nombre del disco que quiere cambiar de tamaño.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

También se debe desasignar la máquina virtual. Use el comando [az vm deallocate]( /cli/azure/vm#deallocate) para detener y desasignar la máquina virtual.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Use el comando [az disk update](/cli/azure/vm/disk#update) para cambiar el tamaño del disco. En este ejemplo se cambia el tamaño de un disco llamado *myDataDisk* a 1 terabyte.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Cuando se haya completado la operación de cambio de tamaño, inicie la máquina virtual.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Si ha cambiado el tamaño del disco del sistema operativo, la partición se expande automáticamente. Si ha cambiado el tamaño de un disco de datos, todas las particiones actuales se deben expandir en el sistema operativo de las máquinas virtuales.

## <a name="snapshot-azure-disks"></a>Captura de instantáneas de discos de Azure

Cuando se toma una instantánea de disco, se crea una copia de solo lectura y de un momento dado del disco. Las instantáneas de máquina virtual de Azure resultan útiles para guardar rápidamente el estado de una máquina virtual antes de realizar cambios en la configuración. En caso de que los cambios de configuración demostraran no ser los deseados, se puede restaurar el estado de la máquina virtual mediante una instantánea. Cuando una máquina virtual tiene más de un disco, se toma una instantánea de cada uno con independencia de los demás. Para realizar copias de seguridad coherentes con la aplicación, considere la posibilidad de detener la máquina virtual antes de tomar instantáneas de disco. Como alternativa, use el [servicio Azure Backup](/azure/backup/), que permite realizar copias de seguridad automatizadas mientras se ejecuta la máquina virtual.

### <a name="create-snapshot"></a>Creación de una instantánea

Antes de crear una instantánea de disco de máquina virtual, se necesita el identificador o el nombre del disco. Use el comando [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) para devolver el identificador de disco. En este ejemplo, el identificador del disco se almacena en una variable para que se pueda usar en un paso posterior.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Ahora que tiene el identificador del disco de máquina virtual, el siguiente comando crea una instantánea del disco.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Creación del disco a partir de la instantánea

Esta instantánea se puede convertir en un disco, que se puede usar para volver a crear la máquina virtual.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauración de la máquina virtual a partir de la instantánea

Para demostrar la recuperación de la máquina virtual, elimine la máquina virtual existente. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Cree una nueva máquina virtual en el disco de instantáneas.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Reconexión de un disco de datos

Todos los discos de datos se deben volver a conectar a la máquina virtual.

En primer lugar, busque el nombre del disco de datos mediante el comando [az disk list](https://docs.microsoft.com/cli/azure/disk#az_disk_list). En este ejemplo se coloca el nombre del disco en una variable denominada *datadisk*, que se usa en el paso siguiente.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Use el comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) para adjuntar el disco.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido sobre temas relacionados con los discos de máquina virtual; por ejemplo:

> [!div class="checklist"]
> * Discos del SO y temporales
> * Discos de datos
> * Discos Estándar y Premium
> * Rendimiento de disco
> * Conectar y preparar los discos de datos
> * Cambiar el tamaño de los discos
> * Instantáneas de disco

Siga con el siguiente tutorial para aprender sobre la automatización de la configuración de la máquina virtual.

> [!div class="nextstepaction"]
> [Automatización de la configuración de máquinas virtuales](./tutorial-automate-vm-deployment.md)
