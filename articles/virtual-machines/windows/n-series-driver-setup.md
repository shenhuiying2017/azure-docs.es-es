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
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Instalación de controladores de GPU para máquinas virtuales de la serie N con Windows Server
Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Windows Server 2016 o Windows Server 2012 R2, instale los controladores de gráficos de NVIDIA compatibles. Este artículo proporciona pasos de instalación de controlador después de implementar una VM de la serie N. También está disponible la información de instalación del controlador para las [máquinas virtuales Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para conocer las especificaciones básicas, las capacidades de almacenamiento y los detalles del disco, consulte [Tamaño de máquinas virtuales para GPU Windows](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>Instalación del controlador

1. Conéctese mediante Escritorio remoto a cada máquina virtual de la serie N.

2. Descargue, extraiga e instale el controlador compatible con su sistema operativo Windows.

En las máquinas virtuales de Azure NV, se requiere un reinicio después de la instalación del controlador. En máquinas virtuales NC, no se requiere un reinicio.

## <a name="verify-driver-installation"></a>Comprobación de la instalación del controlador

Puede comprobar la instalación del controlador en el Administrador de dispositivos. En el ejemplo siguiente se muestra una configuración correcta de la tarjeta Tesla K80 en una máquina virtual de Azure NC.

![Propiedades del controlador de GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar el estado del dispositivo de GPU, ejecute la utilidad de línea de comandos [smi nvidia](https://developer.nvidia.com/nvidia-system-management-interface) que se instala con el controlador.

1. Abra un símbolo del sistema y cambie al directorio **C:\Program Files\NVIDIA Corporation\NVSMI**.

2. Ejecute **nvidia-smi**. Si el controlador está instalado, obtendrá un resultado parecido al siguiente. Tenga en cuenta que **GPU-Util** muestra **0 %** a no ser que esté ejecutando actualmente una carga de trabajo de la GPU en la máquina virtual.

![Estado del dispositivo de NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Red RDMA para máquinas virtuales NC24r

La conectividad de red RDMA puede habilitarse en máquinas virtuales NC24r implementadas en el mismo conjunto de disponibilidad. En las máquinas virtuales compatibles con RDMA, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivos de red de Windows necesarios para la conectividad RDMA. Para agregar la extensión de máquina virtual a una máquina virtual NC24r, puede usar los cmdlets de [Azure PowerShell](/powershell/azure/overview) para Azure Resource Manager.

> [!NOTE]
> Actualmente, solo Windows Server 2012 R2 es compatible con la red RDMA en máquinas virtuales NC24r.
> 

Para instalar la versión más reciente de la extensión HpcVMDrivers 1.1 en una máquina virtual compatible con RDMA existente denominada "myVM" en la región de oeste de EE. UU.:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obtener más información, consulte [Características y extensiones de las máquinas virtuales para Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Ahora, la red RDMA admite el tráfico de interfaz de paso de mensajes (MPI) para aplicaciones que se ejecutan con [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) o Intel MPI 5.x. 


## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre las GPU de NVIDIA en las máquinas virtuales de la serie N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para máquinas virtuales de Azure NC)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para máquinas virtuales de Azure NV)

* Los desarrolladores que creen aplicaciones con aceleración por GPU para las GPU Tesla de NVIDIA también pueden descargar e instalar la CUDA Toolkit 8 para [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) o [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Para obtener más información, consulte la [guía de instalación de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


