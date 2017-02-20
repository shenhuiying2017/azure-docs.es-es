---
title: "Diferentes formas de crear una máquina virtual Linux en Azure | Microsoft Azure"
description: "Conozca las distintas formas de crear una máquina virtual Linux en Azure, y obtenga vínculos a herramientas y tutoriales para cada método."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 23862762fcf0939ce84859fdae0274421c0bb5fe


---
# <a name="different-ways-to-create-a-linux-vm-including-the-azure-cli-20-preview"></a>Distintas maneras de crear una máquina virtual Linux, incluida la CLI de Azure 2.0 (versión preliminar)
En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux.

## <a name="azure-cli"></a>CLI de Azure
En Azure se pueden crear máquinas virtuales con cualquiera de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md): la CLI para los modelos de implementación clásico y de Resource Manager
- CLI de Azure 2.0 (versión preliminar): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)

La [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) está disponible para varias plataformas mediante un paquete de npm, los paquetes proporcionados por la distribución o un contenedor de Docker. Instale la compilación más apropiada para su entorno e inicie sesión en una cuenta de Azure mediante [az login](/cli/azure/#login)

Los ejemplos siguientes usan la CLI de Azure 2.0 (versión preliminar). Para más información acerca de los distintos comandos que se muestran, lea cada uno de los artículos. También puede encontrar ejemplos de las opciones de creación de Linux mediante la [CLI de Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md).

* [Creación de una máquina virtual Linux con la CLI de Azure 2.0 (versión preliminar)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En este ejemplo se utiliza [az group create](/cli/azure/group#create) para crear un grupo de recursos denominado `myResourceGroup`: 
    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * En este ejemplo, se usa [az vm create](/cli/azure/vm#create) para crear una máquina virtual llamada `myVM` con la imagen de Debian más reciente con Azure Managed Disks y una clave pública llamada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Si desea usar discos no administrados, agregue el indicador `--use-unmanaged-disks` al comando anterior. Se crea una cuenta de almacenamiento. Para más información, consulte [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Información general sobre Azure Managed Disks).

* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se utiliza [az group deployment create](/cli/azure/group/deployment#create) para crear una máquina virtual mediante una plantilla almacenada en GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Creación de un entorno de Linux completo mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Incluye la creación de un equilibrador de carga y varias máquinas virtuales en un conjunto de disponibilidad.

* [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se utiliza [az vm disk attach-new](/cli/azure/vm/disk#attach-new) para agregar un disco no administrado de 5 GB llamado `myDataDisk.vhd` a una máquina virtual existente llamada `myVM`:
  
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://mystorageaccount.blob.core.windows.net/vhds/myDataDisk.vhd
    ```

## <a name="azure-portal"></a>Portal de Azure
[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Conexión de un disco mediante el Portal de Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### <a name="azure-images"></a>Imágenes de Azure
Los comandos de [az vm image](/cli/azure/vm/image) se usan para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```azurecli
az vm image list-publishers --location WestUS
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```azurecli
az vm image list-offers --publisher-name Canonical --location WestUS
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer --location WestUS
```

Lista de todas las imágenes disponibles para una versión determinada:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

El comando **az vm create** tiene alias que se pueden usar para acceder rápidamente a las distribuciones más comunes y a sus versiones más recientes. Usar alias es más fácil que tener que especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

| Alias | Publicador | Oferta | SKU | Versión |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |más reciente |
| CoreOS |CoreOS |CoreOS |Stable |más reciente |
| Debian |credativ |Debian |8 |más reciente |
| openSUSE |SUSE |openSUSE |13.2 |más reciente |
| RHEL |Redhat |RHEL |7,2 |más reciente |
| SLES |SLES |SLES |12-SP1 |más reciente |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |más reciente |

### <a name="use-your-own-image"></a>Uso de su propia imagen
Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente; para ello, *capture* esa máquina virtual. También puede cargar una imagen creada de forma local. Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

* [Distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de ejemplo de **az vm** de inicio rápido para capturar una máquina virtual existente mediante discos no administrados:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Pasos siguientes
* Cree una máquina virtual Linux desde el [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con la [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o mediante una [plantilla de Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Después de crear una máquina virtual Linux, [agregue un disco de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO2-->


