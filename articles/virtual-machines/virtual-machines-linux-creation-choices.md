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
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Diferentes maneras de crear una máquina virtual Linux, incluida la CLI de Azure 2.0 (versión preliminar)
En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux.

## <a name="azure-cli"></a>Azure CLI

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- CLI de Azure 1.0: la CLI para los modelos de implementación clásico y con Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](../xplat-cli-install.md): la CLI de última generación para el modelo de implementación de Resource Manager

La CLI de Azure 2.0 (versión preliminar) está disponible para varias plataformas mediante un paquete de npm, los paquetes proporcionados por la distribución o un contenedor de Docker. Asegúrese de que ha iniciado sesión mediante **az login**.

Los siguientes tutoriales proporcionan ejemplos del uso de la CLI de Azure 2.0 (versión preliminar). Lea cada artículo para más información sobre los comandos que se muestran:

* [Creación de una máquina virtual Linux con la CLI de Azure 2.0 (versión preliminar)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En este ejemplo se crea un grupo de recursos denominado myResourceGroup: 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * En este ejemplo se crea una máquina virtual en el nuevo grupo de recursos mediante la imagen de Debian más reciente con una clave pública denominada `id_rsa.pub`:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se crea una máquina virtual mediante una plantilla almacenada en GitHub:
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Creación de un entorno de Linux completo mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Incluye la creación de un equilibrador de carga y varias máquinas virtuales en un conjunto de disponibilidad.

* [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se agrega un disco de 5 GB a una máquina virtual existente llamada `myVM`:
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Portal de Azure
[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Conexión de un disco mediante el Portal de Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### <a name="azure-images"></a>Imágenes de Azure
Puede usar los comandos de la CLI `az vm image` para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```azurecli
az vm image list-publishers -l WestUS
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Lista de todas las imágenes disponibles para una versión determinada:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

El comando `az vm create` tiene alias que se pueden usar para acceder rápidamente a las distribuciones más comunes y sus versiones más recientes. Usar alias es más fácil que tener que especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

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
  
  * Comandos de ejemplo para capturar una máquina virtual existente:
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Pasos siguientes
* Cree una máquina virtual Linux desde el [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con la [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o mediante una [plantilla de Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Después de crear una máquina virtual Linux, [agregue un disco de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


