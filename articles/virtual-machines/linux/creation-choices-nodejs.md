---
title: "Diferentes maneras de crear una máquina virtual Linux con la CLI de Azure 1.0 | Documentos de Microsoft"
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
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756141fda3148726af0030a010465ee14e8ce6b6
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# Diferentes formas de crear una máquina virtual Linux en Azure
<a id="different-ways-to-create-a-linux-virtual-machine-in-azure" class="xliff"></a>
En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux.

## CLI de Azure
<a id="azure-cli" class="xliff"></a>
En Azure se pueden crear máquinas virtuales con cualquiera de las siguientes versiones de la CLI:

- CLI de Azure 1.0: la CLI para los modelos de implementación clásico y de Resource Manager (este artículo)
- [CLI de Azure 2.0 ](../windows/creation-choices.md): la CLI de última generación para el modelo de implementación de administración de recursos.

La CLI de Azure 1.0 se puede usar en distintas plataformas mediante un paquete de npm, paquetes proporcionados por la distribución o un contenedor de Docker. Puede leer más sobre [cómo instalar y configurar la CLI de Azure](../../cli-install-nodejs.md). Los siguientes tutoriales proporcionan ejemplos sobre el uso de la CLI de Azure 1.0. Lea cada artículo para más información sobre los comandos de inicio rápido de la CLI mostrados:

* [Creación de una máquina virtual con Linux en Azure mediante la CLI](quick-create-cli-nodejs.md)
  
  * En el ejemplo siguiente se crea una máquina virtual de CoreOS con una clave pública llamada *azure_id_rsa.pub*:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Creación de una VM de Linux protegida mediante una plantilla de Azure](create-ssh-secured-vm-from-template.md)
  
  * En el ejemplo siguiente se crea una máquina virtual mediante una plantilla almacenada en GitHub:
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Creación de un entorno de Linux completo mediante la CLI de Azure](create-cli-complete-nodejs.md)
  
  * Incluye la creación de un equilibrador de carga y varias máquinas virtuales en un conjunto de disponibilidad.
* [Adición de un disco a una máquina virtual de Linux](add-disk.md)
  
  * En el ejemplo siguiente se agrega un disco de *5* GB a una máquina virtual existente llamada *myVM*:
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## Portal de Azure
<a id="azure-portal" class="xliff"></a>
[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](quick-create-portal.md) 

## Sistema operativo y opciones de imagen
<a id="operating-system-and-image-choices" class="xliff"></a>
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### Imágenes de Azure
<a id="azure-images" class="xliff"></a>
Puede usar los comandos de la CLI `azure vm image` para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```azurecli
azure vm image list-publishers --location eastus
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

Lista de todas las imágenes disponibles para una versión determinada:

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](cli-ps-findimage.md#use-azure-cli-10)para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

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

### Uso de su propia imagen
<a id="use-your-own-image" class="xliff"></a>
Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente; para ello, *capture* esa máquina virtual. También puede cargar una imagen creada de forma local. Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

* [Distribuciones aprobadas por Azure](endorsed-distros.md)
* [Información para las distribuciones no aprobadas](create-upload-generic.md)
* [Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada](upload-vhd.md)
* [Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager](capture-image.md).
  
  * Comandos de ejemplo para capturar una máquina virtual existente:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## Pasos siguientes
<a id="next-steps" class="xliff"></a>
* Cree una máquina virtual Linux desde el [portal](quick-create-portal.md), con la [CLI](quick-create-cli.md) o mediante una [plantilla de Azure Resource Manager](../windows/cli-deploy-templates.md).
* Después de crear una máquina virtual Linux, [agregue un disco de datos](add-disk.md).
* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](using-vmaccess-extension.md)


