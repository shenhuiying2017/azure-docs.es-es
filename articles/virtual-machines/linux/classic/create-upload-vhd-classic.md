---
title: "Creación y carga de un VHD de Linux en Azure | Microsoft Docs"
description: "Cree y cargue un disco duro virtual de Azure (VHD) que contenga el sistema operativo Linux con el modelo de implementación clásica."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.openlocfilehash: 49cf4f1718e4dce1e86aa3c8921eaa8af5f16192
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Creación y carga de un disco duro virtual que contiene el sistema operativo Linux
> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. También puede [cargar una imagen de disco personalizada mediante Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que podrá utilizar como imagen propia para crear máquinas virtuales en Azure. Aprenda a preparar el sistema operativo que podrá utilizar para crear máquinas virtuales basadas en esa imagen. 


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

* **Sistema operativo Linux instalado en un archivo .vhd**: ha instalado una [distribución de Linux aprobada por Azure](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (o consulte la [información para distribuciones no aprobadas](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
  * Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert`.
  * También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.

* **Interfaz de la línea de comandos de Azure** : instale la [interfaz de la línea de comandos de Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) más reciente para cargar el archivo VHD.

<a id="prepimage"></a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Paso 1: Preparación de la imagen que se va a cargar
Azure admite varias distribuciones Linux (consulte [Distribuciones aprobadas](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure. Tras completar los pasos en las siguientes guías, vuelva aquí en cuanto tenga un archivo VHD listo para cargarse en Azure:

* **[Distribuciones basadas en CentOS](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES y openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Otras distribuciones no aprobadas](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> El Acuerdo de Nivel de Servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección "Versiones admitidas" en [Linux en distribuciones aprobadas por Azure](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Todas las distribuciones de Linux en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.
> 
> 

Consulte también las **[notas de instalación de Linux](../create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

<a id="connect"></a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Paso 2: preparación de la conexión a Azure
Asegúrese de estar utilizando la CLI de Azure en el modelo de implementación clásica (`azure config mode asm`) y luego inicie sesión en su cuenta:

```azurecli
azure login
```


<a id="upload"></a>

## <a name="step-3-upload-the-image-to-azure"></a>Paso 3: cargar la imagen en Azure
Tiene que cargar el archivo VHD a una cuenta de almacenamiento. Puede seleccionar una cuenta de almacenamiento existente o [crear una nueva](../../../storage/common/storage-create-storage-account.md).

Use la CLI de Azure para cargar la imagen con el siguiente comando:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

En el ejemplo anterior:

* **BlobStorageURL** es la dirección URL de la cuenta de almacenamiento que pretende usar
* **SuCarpetaDeImágenes** es el contenedor de Almacenamiento de blobs en el que desea almacenar las imágenes
* **VHDName** es la etiqueta que aparece en el portal para identificar el disco duro virtual.
* **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd de su máquina.

El siguiente comando muestra un ejemplo completo:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Paso 4: Crear una VM desde la imagen
Cree una VM con `azure vm create` de la misma forma que una VM normal. Especifique el nombre que asignó a la imagen en el paso anterior. En el ejemplo siguiente, se utiliza el nombre de imagen **myImage** determinado en el paso anterior:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Para crear sus propias VM, proporcione su propio nombre de usuario + contraseña, la ubicación, el nombre DNS y el nombre de imagen.

## <a name="next-steps"></a>pasos siguientes
Para obtener más información, consulte [Referencia de la CLI de Azure para el modelo de implementación clásica de Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
