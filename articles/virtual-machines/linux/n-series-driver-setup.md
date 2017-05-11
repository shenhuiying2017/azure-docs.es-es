---
title: "Instalación del controlador de la serie N de Azure para Linux | Microsoft Docs"
description: "Instalación de controladores de GPU de NVIDIA para máquinas virtuales de la serie N que se ejecutan en Linux en Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: es-es
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configuración de controladores de GPU para máquinas virtuales de la serie N con Linux

Para aprovechar las funcionalidades de GPU de las VM de la serie N de Azure que ejecutan Linux, debe instalar los controladores de gráficos de NVIDIA en cada VM. Este artículo proporciona pasos de instalación de controlador después de implementar una VM de la serie N. También está disponible la información de instalación del controlador para las [máquinas virtuales Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Para conocer las especificaciones de máquina virtual de la serie N, las capacidades de almacenamiento y los detalles del disco, vea [Tamaño de máquinas virtuales para GPU Linux](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="supported-distributions-and-drivers"></a>Distribuciones y controladores admitidos

> [!IMPORTANT]
> Actualmente, el controlador para GPU Linux solo está disponible en las VM de Azure NC. 

Se admiten las siguientes distribuciones de Azure Marketplace para ejecutar controladores de gráficos NVIDIA en VM de Linux de la serie N.

### <a name="nc-vms-tesla-k80-card"></a>VM de NC (tarjeta Tesla K80)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* Basado en CentOS 7.3 

**Controladores admitidos**: NVIDIA CUDA 8.0, rama de controlador R375. [Pasos de instalación](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> La instalación de software de terceros en productos de Red Hat puede afectar a los términos de soporte técnico de Red Hat. Vea el [artículo de Knowledgebase de Red Hat](https://access.redhat.com/articles/1067).
>




## <a name="install-cuda-drivers-for-nc-vms"></a>Instalación de controladores CUDA para VM de NC

Estos son los pasos para instalar controladores NVIDIA en máquinas virtuales Linux NC desde la versión 8.0 del kit de herramientas de NVIDIA CUDA. 

Los desarrolladores de C y C++, si lo desean, pueden instalar el kit de herramientas completo para crear aplicaciones aceleradas por GPU. Para obtener más información, consulte la [guía de instalación de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Los vínculos de descarga de controladores de CUDA que se proporcionan aquí están actualizados en el momento de la publicación. Para ver los controladores más recientes, visite el sitio web de [NVIDIA](http://www.nvidia.com/).
>

Para instalar el kit de herramientas de CUDA, realice una conexión SSH a cada máquina virtual. Para comprobar que el sistema dispone de una GPU compatible con CUDA, ejecute el siguiente comando:

```bash
lspci | grep -i NVIDIA
```
Verá un resultado similar al siguiente ejemplo (mostrando una tarjeta NVIDIA Tesla K80):

![Resultado del comando de Ispci](./media/n-series-driver-setup/lspci.png)

Luego, ejecute los comandos específicos de su distribución.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
La instalación puede tardar varios minutos.

Para instalar (opcional) el kit de herramientas CUDA completo, escriba:

```bash
sudo apt-get install cuda
```

Reinicie la máquina virtual y continúe para comprobar la instalación.

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 o Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> Debido a un problema conocido, se produce un error en la instalación del controlador de NVIDIA CUDA en VM NC24r que ejecutan CentOS 7.3 o Red Hat Enterprise Linux 7.3.
>

En primer lugar, obtenga actualizaciones. 

```bash
sudo yum update

sudo reboot
```

Vuelva a conectarse a la VM y continúe con la instalación con los siguientes comandos:

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

La instalación puede tardar varios minutos. Para instalar (opcional) el kit de herramientas CUDA completo, escriba:

```bash
sudo yum install cuda
```

Reinicie la máquina virtual y continúe para comprobar la instalación.


### <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador


Para consultar el estado del dispositivo de GPU, acceda mediante SSH a la máquina virtual y ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

Verá un resultado similar al siguiente:

![Estado del dispositivo de NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Actualizaciones de controladores CUDA

Se recomienda actualizar periódicamente los controladores CUDA después de la implementación.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Una vez finalizada la actualización, reinicie la máquina virtual.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 o Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

Una vez finalizada la actualización, reinicie la máquina virtual.



## <a name="troubleshooting"></a>Solución de problemas

* Hay un problema conocido con los controladores CUDA en las máquinas virtuales serie N de Azure que ejecutan el kernel de Linux 4.4.0-75 en Ubuntu 16.04 LTS. Para mantener el funcionamiento del controlador cuando se actualiza el kernel, actualice al menos a la versión 4.4.0-77 del kernel. 



## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)

* Para capturar una imagen de una VM Linux con los controladores de NVIDIA instalados, vea [Procedimiento para generalizar y capturar una máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
