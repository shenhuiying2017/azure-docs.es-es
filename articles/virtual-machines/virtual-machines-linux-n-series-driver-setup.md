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
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Configuración de controladores de GPU para máquinas virtuales de la serie N
Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan una distribución de Linux compatible, debe instalar los controladores de gráficos de NVIDIA en cada máquina virtual después de la implementación. Este artículo también está disponible para [máquinas virtuales con Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para conocer las especificaciones de máquina virtual de la serie N, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



## <a name="supported-gpu-drivers"></a>Controladores de GPU admitidos


> [!NOTE]
> Actualmente, la compatibilidad con la GPU de Linux solo está disponible en las máquinas virtuales de Azure NC que ejecutan Ubuntu Server 16.04 LTS.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Controladores de NVIDIA Tesla para máquinas virtuales NC

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (instalador autoextraíble .run)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Instalación del controlador Tesla en Ubuntu 16.04 LTS

1. Realice una conexión SSH a la máquina virtual de la serie N de Azure.

2. Para comprobar que el sistema dispone de una GPU compatible con CUDA, ejecute el siguiente comando:

    ```bash
    lspci | grep -i NVIDIA
    ```
    Verá un resultado similar al siguiente ejemplo (mostrando una tarjeta NVIDIA Tesla K80):

    ![Resultado del comando de Ispci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. Descargue el archivo .run para el controlador para su distribución. El siguiente comando de ejemplo descarga el controlador Ubuntu 16.04 LTS Tesla en el directorio /tmp:

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. Si necesita instalar `gcc` y `make` en el sistema (necesario para los controladores de Tesla), escriba lo siguiente:

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. Cambie al directorio que contiene el instalador del controlador y ejecute comandos similares al siguiente:

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador


Para consultar el estado del dispositivo de GPU, ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

![Estado del dispositivo de NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Instalación opcional del Kit de herramientas de NVIDIA CUDA en Ubuntu 16.04 LTS

Si lo desea, puede instalar el Kit de herramientas de NVIDIA CUDA 8.0 en máquinas virtuales de NC con Ubuntu 16.04 LTS. Además de los controladores GPU, el Kit de herramientas proporciona un entorno de desarrollo completo para desarrolladores C y C++ que crean aplicaciones con aceleración mediante GPU.

Para instalar el Kit de herramientas de CUDA, ejecute comandos similares al siguiente:

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

La instalación puede tardar varios minutos.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)




<!--HONumber=Dec16_HO2-->


