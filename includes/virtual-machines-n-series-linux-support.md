---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: e925dba3805ec8994aeba730e325c407468a5c87
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2018
---
## <a name="supported-distributions-and-drivers"></a>Distribuciones y controladores admitidos

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Serie NC, NCv2, NCv3 y ND: controladores de NVIDIA CUDA

La información sobre los controladores de CUDA de la tabla siguiente está actualizada en el momento de su publicación. Para ver los controladores de CUDA más recientes, visite el sitio web de [NVIDIA](https://developer.nvidia.com/cuda-zone). Asegúrese de instalar o actualizar los controladores más recientes de CUDA para su distribución. 

> [!TIP]
> Como alternativa a la instalación manual de controladores de CUDA en una máquina virtual Linux, puede implementar una imagen de [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) de Azure. Las ediciones de DSVM para Ubuntu 16.04 LTS o CentOS 7.4 preinstalan los controladores NVIDIA CUDA, la biblioteca CUDA Deep Neural Network Library y otras herramientas.

| Distribución | Controlador |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 o 7.4<br/><br/> CentOS 7.3 o 7.4, HPC basado en CentOS 7.4 | NVIDIA CUDA 9.1, rama de controlador R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: controladores de NVIDIA GRID

Microsoft redistribuye instaladores de controladores de NVIDIA GRID para máquinas virtuales de NV. Instale estos controladores de GRID solo en máquinas virtuales de Azure NV. Estos controladores incluyen licencias del software GRID Virtual GPU en Azure.

| Distribución | Controlador |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Basado en CentOS 7.3 | NVIDIA GRID 6.0, rama de controlador R390|



> [!WARNING] 
> La instalación de software de terceros en productos de Red Hat puede afectar a los términos de soporte técnico de Red Hat. Vea el [artículo de Knowledgebase de Red Hat](https://access.redhat.com/articles/1067).
>
