---
title: "Carga o copia de una máquina virtual Linux personalizada con la CLI de Azure 2.0 | Microsoft Docs"
description: "Cargue o copie una máquina virtual personalizada mediante el modelo de implementación de Resource Manager y la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: 7c297725c26ea6c44403a10ecdcc3542f89f10b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Creación de una máquina virtual Linux a partir de un disco personalizado mediante la CLI de Azure 2.0

<!-- rename to create-vm-specialized -->

Este artículo muestra cómo cargar un disco duro virtual (VHD) personalizado o copiar un VHD existente en Azure y crear nuevas máquinas virtuales Linux desde el disco personalizado. Puede instalar y configurar una distribución de Linux para sus requisitos y después usar ese VHD para crear rápidamente una nueva máquina virtual de Azure.

Si quiere crear varias máquinas virtuales desde el disco personalizado, debe crear una imagen de la máquina virtual o el VHD. Para más información, vea [Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI](tutorial-custom-images.md).

Tiene dos opciones:
* [Carga de un disco duro virtual](#option-1-upload-a-specialized-vhd)
* [Copia de una máquina virtual de Azure existente](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Comandos rápidos

Al crear una nueva máquina virtual mediante [az vm create](/cli/azure/vm#create) desde un disco personalizado o especializado, se **adjunta** el disco (--attach-os-disk) en lugar de especificar una imagen personalizada o de Marketplace (--imagen). En el ejemplo siguiente se crea una máquina virtual denominada *myVM* mediante el disco administrado llamado *myManagedDisk* creado a partir del VHD personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisitos
Para completar los pasos siguientes, necesita:

* Una máquina virtual Linux que se ha preparado para su uso en Azure. En la sección [Preparación de la VM](#prepare-the-vm) de este artículo se explica cómo encontrar información de distribución sobre la instalación del agente Linux de Azure (waagent) necesario para que la máquina virtual funcione correctamente en Azure y para que se pueda conectar a ella mediante SSH.
* El archivo VHD de una [distribución de Linux aprobada por Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) existente (o vea [Información para las distribuciones no aprobadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
  * Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con **qemu-img convert**.
  * También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX en VHD mediante [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.
> 
> 


* Asegúrese de que ha instalado la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *mystorageaccount* y *mydisks*.

<a id="prepimage"></a>

## <a name="prepare-the-vm"></a>Preparación de la VM

Azure admite varias distribuciones Linux (consulte [Distribuciones aprobadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

* [Distribuciones basadas en CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES y openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Otras distribuciones no aprobadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vea también las [notas de instalación de Linux](create-upload-generic.md#general-linux-installation-notes) para obtener más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

> [!NOTE]
> El [Acuerdo de Nivel de Servicio de la plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a las máquinas virtuales que ejecutan Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección "Versiones admitidas" en [Linux en distribuciones aprobadas por Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opción 1: Cargar un disco duro virtual

Puede cargar un VHD personalizado que se esté ejecutando en un equipo local o que se haya exportado desde otra nube. Para usar el VHD para crear una nueva máquina virtual de Azure, debe cargarlo en una cuenta de almacenamiento y crear un disco administrado a partir del VHD. 

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de cargar el disco personalizado y crear máquinas virtuales, es preciso crear un grupo de recursos con [az group create](/cli/azure/group#create).

En el ejemplo siguiente se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*: [Introducción a Azure Managed Disks](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Cree una cuenta de almacenamiento para el disco personalizado y las máquinas virtuales con [az storage account create](/cli/azure/storage/account#create). 

En el ejemplo siguiente se crea una cuenta de almacenamiento denominada *mystorageaccount* en el grupo de recursos creado anteriormente:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Enumerar claves de cuentas de almacenamiento
Azure genera dos claves de acceso de 512 bits para cada cuenta de almacenamiento. Estas claves de acceso se usan al autenticar en la cuenta de almacenamiento, por ejemplo para realizar operaciones de escritura. Lea más sobre [administración del acceso al almacenamiento aquí](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Vea las teclas de acceso con [az storage account keys list](/cli/azure/storage/account/keys#list).

Consulte las claves de acceso de la cuenta de almacenamiento que ha creado:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

La salida es parecida a esta:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Tome nota de **key1**, pues se usará para interactuar con la cuenta de almacenamiento en los siguientes pasos.

### <a name="create-a-storage-container"></a>Creación de un contenedor de almacenamiento
De la misma manera que crea directorios distintos para organizar de forma lógica el sistema de archivos local, crea contenedores dentro de una cuenta de almacenamiento para organizar los discos. Una cuenta de almacenamiento puede contener un número cualquiera de contenedores. Cree un contenedor con [az storage container create](/cli/azure/storage/container#create).

En el siguiente ejemplo se crea un contenedor denominado *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Carga del disco duro virtual
Ahora puede cargar su disco personalizado con [az storage blob upload](/cli/azure/storage/blob#upload). Cargue y almacene el disco personalizado como un blob en páginas.

Especifique la clave de acceso, el contenedor que creó en el paso anterior y, a continuación, la ruta de acceso al disco personalizado del equipo local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
La carga del VHD puede llevar un rato.

### <a name="create-a-managed-disk"></a>Creación de un disco administrado


Cree un disco administrado a partir del VHD mediante [az disk create](/cli/azure/disk#create). En el ejemplo siguiente se crea un disco administrado denominado *myManagedDisk* a partir del VHD cargado en la cuenta de almacenamiento y el contenedor con nombre:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opción 2: Copiar una máquina virtual existente

También se puede crear la máquina virtual personalizada en Azure y luego copiar el disco del sistema operativo y asociarlo a una nueva máquina virtual para crear otra copia. Esto es adecuado para realizar pruebas, pero si quiere usar una máquina virtual existente de Azure como modelo para varias máquinas virtuales nuevas, en realidad debe crear una **imagen**. Para más información sobre cómo crear una imagen a partir de una máquina virtual existente de Azure, vea [Crear una imagen personalizada de una máquina virtual de Azure mediante la CLI](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Crear una instantánea

En este ejemplo se crea una instantánea de una máquina virtual denominada *myVM* en el grupo de recursos *myResourceGroup* y se crea una instantánea denominada *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Creación del disco administrado

Cree un nuevo disco administrado a partir de la instantánea.

Obtenga el identificador de la instantánea. En este ejemplo, la instantánea se denomina *osDiskSnapshot* y se encuentra en el grupo de recursos *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Cree el disco administrado. En este ejemplo se va a crear un disco administrado denominado *myManagedDisk* a partir de la instantánea, que tiene un tamaño de 128 GB de almacenamiento estándar.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Creación de la máquina virtual

Ahora cree la máquina virtual con [az vm create](/cli/azure/vm#create) y asocie (--attach-os-disk) el disco administrado como disco del sistema operativo. En el ejemplo siguiente se crea una máquina virtual denominada *myNewVM* mediante el disco administrado creado a partir del VHD cargado:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Debe ser capaz de iniciar sesión con SSH en la máquina virtual mediante las credenciales de la máquina virtual de origen. 

## <a name="next-steps"></a>Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre el [uso de Resource Manager y las plantillas](../../azure-resource-manager/resource-group-overview.md). También es posible que quiera [agregar un disco de datos](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a las nuevas máquinas virtuales. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite tener acceso, asegúrese de [abrir puertos y puntos de conexión](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

