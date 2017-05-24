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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Diferentes formas de crear una máquina virtual Linux
En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux, que incluyen la CLI de Azure 2.0. También puede ver las opciones de creación que se incluye en la [CLI de Azure 1.0](creation-choices-nodejs.md).

La [CLI de Azure 2.0](/cli/azure/install-az-cli2) se puede usar en distintas plataformas mediante un paquete de npm, paquetes proporcionados por la distribución o un contenedor de Docker. Instale la compilación más apropiada para su entorno e inicie sesión en una cuenta de Azure mediante [az login](/cli/azure/#login)

* [Creación de una máquina virtual Linux con la CLI de Azure 2.0](quick-create-cli.md)
  
  * Cree un grupo de recursos con [az group create](/cli/azure/group#create) llamado *myResourceGroup*: 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Cree una máquina virtual con [az vm create](/cli/azure/vm#create) llamada *myVM* mediante la imagen más reciente de *UbuntuLTS* y genere claves SSH si ya existen en *~/.ssh*:

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Creación de una máquina virtual Linux mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md)
  
  * En el ejemplo siguiente se usa [az group deployment create](/cli/azure/group/deployment#create) para crear una máquina virtual mediante una plantilla almacenada en GitHub:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Creación de una máquina virtual Linux y su personalización con cloud-init](tutorial-automate-vm-deployment.md)

* [Creación de una aplicación de equilibrio de carga y alta disponibilidad en varias máquinas virtuales Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Portal de Azure
[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### <a name="azure-images"></a>Imágenes de Azure
Los comandos de [az vm image](/cli/azure/vm/image) se usan para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```azurecli
az vm image list-publishers --location eastus
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Lista de todas las imágenes disponibles para una versión determinada:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](cli-ps-findimage.md)para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

El comando [az vm create](/cli/azure/vm#create) tiene alias que se pueden usar para acceder rápidamente a las distribuciones más comunes y a sus versiones más recientes. Usar alias es más fácil que tener que especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

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
Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente; para ello, capture esa máquina virtual. También puede cargar una imagen creada de forma local. Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

* [Distribuciones aprobadas por Azure](endorsed-distros.md)
* [Información para las distribuciones no aprobadas](create-upload-generic.md)
* [Creación de una imagen a partir de una máquina virtual de Azure existente](tutorial-custom-images.md)
  
  * Comandos de ejemplo de inicio rápido para crear una imagen a partir de una máquina virtual de Azure existente:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Pasos siguientes
* Cree una máquina virtual Linux desde la [CLI](quick-create-cli.md), desde el [portal](quick-create-portal.md) o mediante una [plantilla de Azure Resource Manager](../windows/cli-deploy-templates.md).
* Después de crear una máquina virtual Linux, [aprenda sobre los discos y el almacenamiento de Azure](tutorial-manage-disks.md).
* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](using-vmaccess-extension.md)

