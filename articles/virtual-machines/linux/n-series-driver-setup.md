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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configuración de controladores de GPU para máquinas virtuales de la serie N con Linux

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan una distribución de Linux compatible, debe instalar los controladores de gráficos de NVIDIA en cada máquina virtual después de la implementación. También está disponible la información de instalación del controlador para las [máquinas virtuales Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> Actualmente, la compatibilidad con la GPU de Linux solo está disponible en las máquinas virtuales de Azure NC que ejecutan Ubuntu Server 16.04 LTS.
> 

Para conocer las especificaciones de máquina virtual de la serie N, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vea también [Consideraciones generales para máquinas virtuales de serie N](#general-considerations-for-n-series-vms).



## <a name="install-nvidia-cuda-drivers"></a>Instalación de controladores NVIDIA CUDA

Estos son los pasos para instalar controladores NVIDIA en máquinas virtuales Linux NC desde la versión 8.0 del kit de herramientas de NVIDIA CUDA. Los desarrolladores de C y C++, si lo desean, pueden instalar el kit de herramientas completo para crear aplicaciones aceleradas por GPU. Para obtener más información, consulte la [guía de instalación de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Los vínculos de descarga de controladores que se proporcionan aquí están actualizados en el momento de la publicación. Para ver los controladores más recientes, visite el sitio web de [NVIDIA](http://www.nvidia.com/).

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

## <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador


Para consultar el estado del dispositivo de GPU, acceda mediante SSH a la máquina virtual y ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

![Estado del dispositivo de NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>Actualizaciones de controladores CUDA

Se recomienda actualizar periódicamente los controladores CUDA después de la implementación.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Una vez finalizada la actualización, reinicie la máquina virtual.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* No se recomienda instalar X Server u otros sistemas que usan el controlador nouveau en máquinas virtuales Ubuntu NC. Antes de instalar controladores de GPU NVIDIA, debe deshabilitar al controlador nouveau.  

* Si desea capturar una imagen de una máquina virtual Linux en la que ha instalado controladores NVIDIA, consulte [Procedimiento para generalizar y capturar una máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)


