---
title: "Diferentes formas de crear una máquina virtual Linux | Microsoft Docs"
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
ms.date: 09/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 8835427415e8e01e851796eaf323bce7d1918c8c
ms.openlocfilehash: 8c7ea2e7131f69bc43f2e82b816efdfbda59e85d


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diferentes formas de crear una máquina virtual Linux en Azure
En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux.

## <a name="azure-cli"></a>Azure CLI
La CLI de Azure está disponible entre plataformas mediante un paquete de npm, paquetes proporcionados por la distribución o un contenedor de Docker. Puede leer más sobre [cómo instalar y configurar la CLI de Azure](../xplat-cli-install.md). Los siguientes tutoriales proporcionan ejemplos sobre el uso de la CLI de Azure. Lea cada artículo para más información sobre los comandos de inicio rápido de la CLI mostrados:

* [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se crea una máquina virtual de CoreOS con una clave pública llamada `azure_id_rsa.pub`:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se crea una máquina virtual mediante una plantilla almacenada en GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Creación de un entorno de Linux completo mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Incluye la creación de un equilibrador de carga y varias máquinas virtuales en un conjunto de disponibilidad.
* [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * En el ejemplo siguiente se agrega un disco de 5 GB a una máquina virtual existente llamada `TestVM`:
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal de Azure
[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Conexión de un disco mediante el Portal de Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### <a name="azure-images"></a>Imágenes de Azure
Puede usar los comandos de la CLI `azure vm image` para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```azurecli
azure vm image list-publishers --location WestUS
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Lista de todas las imágenes disponibles para una versión determinada:

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

Los comandos `azure vm quick-create` y `azure vm create` tienen alias que se pueden usar para acceder rápidamente a las distribuciones más comunes y sus versiones más recientes. Usar alias es más fácil que tener que especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

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
* [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Comandos de ejemplo para capturar una máquina virtual existente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Pasos siguientes
* Cree una máquina virtual Linux desde el [portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con la [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o mediante una [plantilla de Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Después de crear una máquina virtual Linux, [agregue un disco de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


