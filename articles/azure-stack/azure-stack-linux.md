---
title: "Agregar imágenes de Linux a Azure Stack"
description: "Aprenda a agregar imágenes de Linux a Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Agregar imágenes de Linux a Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede implementar máquinas virtuales Linux en Azure Stack agregando una imagen basada en Linux a Marketplace de Azure Stack. La manera más fácil de agregar una imagen de Linux a Azure Stack es con la administración de Marketplace. Estas imágenes se prepararon y probaron para que sean compatibles con Azure Stack.

## <a name="marketplace-management"></a>Administración de Marketplace

Para descargar imágenes de Linux desde Azure Marketplace, aplique los procedimientos descritos en este artículo. Seleccione las imágenes de Linux que quiera ofrecer a los usuarios en Azure Stack.

[Descargue elementos de Marketplace de Azure en Azure Stack](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>Preparación de su propia imagen

Puede preparar su propia imagen de Linux con una de las siguientes instrucciones:

   * [Distribuciones basadas en CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES y openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Descargue e instale el [agente Linux de Azure](https://github.com/Azure/WALinuxAgent/).

    Se necesita la versión 2.2.2 o superior del agente Linux de Azure para aprovisionar la máquina virtual Linux en Azure Stack y algunas versiones no funcionan (por ejemplo, 2.2.12 y 2.2.13). Muchas de las distribuciones mencionadas anteriormente ya incluyen una versión del agente (suele denominarse `WALinuxAgent` o `walinuxagent`). Si crea una imagen personalizada, debe instalar el agente de forma manual. La versión instalada se puede determinar desde el nombre del paquete o mediante la ejecución de `/usr/sbin/waagent -version` en la máquina virtual.

    Siga las instrucciones siguientes para instalar al agente Linux de Azure manualmente:

   a. En primer lugar, descargue el agente Linux de Azure más reciente de [GitHub](https://github.com/Azure/WALinuxAgent/releases), por ejemplo:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Desempaquete el agente de Azure:

            # tar -vzxf v2.2.21.tar.gz
   c. Instale python-setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Instale el agente de Azure:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     Puede que sea necesario ejecutar el siguiente comando para aquellos sistemas con Python 2.x y 3.x instalados en paralelo:

         sudo python3 setup.py install --register-service
     Consulte el [archivo LÉAME del agente Linux de Azure](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para más información.
2. [Agregue la imagen a Marketplace](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro está establecido en `Linux`.
3. Después de agregar la imagen a Marketplace, se crea un elemento de Marketplace y los usuarios podrán implementar una máquina virtual Linux.

## <a name="next-steps"></a>pasos siguientes
[Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md)
