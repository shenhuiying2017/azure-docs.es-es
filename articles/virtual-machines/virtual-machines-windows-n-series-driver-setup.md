---
title: "Instalación del controlador de la serie N de Azure para Windows | Microsoft Docs"
description: "Instalación de controladores de GPU de NVIDIA para máquinas virtuales de la serie N que se ejecutan en Windows en Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 57d7475db8183cfaad017fc934210d0481868d5f
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-windows-vms"></a>Configuración de controladores de GPU para máquinas virtuales de la serie N
Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Windows Server, debe instalar los controladores de gráficos de NVIDIA en cada máquina virtual después de la implementación. Este artículo también está disponible para [máquinas virtuales Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de las máquinas virtuales](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




## <a name="supported-gpu-drivers"></a>Controladores de GPU admitidos

Conéctese mediante Escritorio remoto a cada máquina virtual de la serie N. Descargue, extraiga e instale el controlador compatible con su sistema operativo Windows. 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>Controladores de NVIDIA GRID para máquinas virtuales NV

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Controladores de NVIDIA Tesla para máquinas virtuales NC

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador

En las máquinas virtuales de Azure NV, se requiere un reinicio después de la instalación del controlador. En máquinas virtuales NC, no se requiere un reinicio.

Puede comprobar la instalación del controlador en el Administrador de dispositivos. En el ejemplo siguiente se muestra una configuración correcta de la tarjeta K80 en una máquina virtual de Azure NC.

![Propiedades del controlador de GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Para consultar el estado del dispositivo de GPU, ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador. 

![Estado del dispositivo de NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)

* Los desarrolladores que creen aplicaciones con aceleración por GPU para las GPU Tesla de NVIDIA también pueden descargar e instalar la [CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads).



