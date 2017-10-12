---
title: "Agregar imágenes de Linux a Azure Stack"
description: "Aprenda a agregar imágenes de Linux a Azure Stack."
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>Agregar imágenes de Linux a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit* 

Puede implementar máquinas virtuales Linux en Azure Stack agregando una imagen basada en Linux a Marketplace de Azure Stack. La manera más fácil de agregar una imagen de Linux a Azure Stack es con la administración de Marketplace.

## <a name="marketplace-management"></a>Administración de Marketplace

Para descargar imágenes de Linux desde Azure Marketplace, aplique los procedimientos descritos en este artículo. Seleccione las imágenes de Linux que quiera ofrecer a los usuarios en Azure Stack.

[Descargue elementos de Marketplace de Azure en Azure Stack](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Descarga de una imagen

Puede descargar y extraer imágenes de Linux compatibles con Azure Stack mediante los vínculos siguientes:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Extraiga el VHD de imagen si es necesario y [agregue la imagen a Marketplace](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro está establecido en `Linux`.
2. Después de agregar la imagen a Marketplace, se crea un elemento de Marketplace y los usuarios podrán implementar una máquina virtual Linux.

## <a name="prepare-your-own-image"></a>Preparación de su propia imagen

Puede preparar su propia imagen de Linux con una de las siguientes instrucciones:
   
   * [Distribuciones basadas en CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES y openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Descargue e instale el [agente Linux de Azure](https://github.com/Azure/WALinuxAgent/).
   
    Se necesita la versión 2.1.3 o superior del agente Linux de Azure para aprovisionar la máquina virtual Linux en Azure Stack. Muchas de las distribuciones mencionadas anteriormente ya incluyen esta versión u otra posterior del agente en forma de paquete en sus repositorios (suele denominarse `WALinuxAgent` o `walinuxagent`). Pero si la versión del paquete del agente de Azure es anterior a la 2.1.3 (por ejemplo, 2.0.18 o anterior), tendrá que instalar el agente manualmente. La versión instalada se puede determinar desde el nombre del paquete o mediante la ejecución de `/usr/sbin/waagent -version` en la máquina virtual.
   
    Siga las instrucciones siguientes para instalar al agente Linux de Azure manualmente:
   
   a. En primer lugar, descargue el agente Linux de Azure más reciente de [GitHub](https://github.com/Azure/WALinuxAgent/releases), por ejemplo:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Desempaquete el agente de Azure:
     
            # tar -vzxf v2.2.16.tar.gz
   c. Instale python-setuptools
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Instale el agente de Azure:
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     Puede que sea necesario ejecutar el siguiente comando para aquellos sistemas con Python 2.x y 3.x instalados en paralelo:
     
         sudo python3 setup.py install --register-service
     Consulte el [archivo LÉAME del agente Linux de Azure](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para más información.
2. [Agregue la imagen a Marketplace](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro está establecido en `Linux`.
3. Después de agregar la imagen a Marketplace, se crea un elemento de Marketplace y los usuarios podrán implementar una máquina virtual Linux.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md)

