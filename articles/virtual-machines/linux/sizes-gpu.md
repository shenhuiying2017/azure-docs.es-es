---
title: "Tamaños de las máquinas virtuales Linux en Azure: GPU | Microsoft Docs"
description: "Enumera los tamaños diferentes optimizados para GPU disponibles para las máquinas virtuales Linux en Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: 5c9bf89feba519147b07f2810fe4da882664e89e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="gpu-linux-vm-sizes"></a>Tamaño de máquinas virtuales optimizados para GPU Linux

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Para los pasos de instalación y verificación de controladores, consulte [el programa de instalación del controlador de N-series para Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* No se recomienda instalar X Server u otros sistemas que usan el controlador nouveau en máquinas virtuales Ubuntu NC. Antes de instalar controladores de GPU NVIDIA, debe deshabilitar al controlador nouveau.  

## <a name="other-sizes"></a>Otros tamaños
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [Proceso de alto rendimiento](sizes-hpc.md)

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.