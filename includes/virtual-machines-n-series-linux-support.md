---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Distribuciones y controladores admitidos

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Serie NC, NCv2, NCv3 y ND: controladores de NVIDIA CUDA
| Distribución | Controlador |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 o 7.4<br/><br/> CentOS 7.3 o 7.4 | NVIDIA CUDA 9.1, rama de controlador R390 |

> [!IMPORTANT]
> Asegúrese de instalar o actualizar los controladores más recientes de CUDA para su distribución. Los controladores con una versión anterior a la R390 pueden tener problemas con los kernels de Linux actualizados.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: controladores de NVIDIA GRID

| Distribución | Controlador |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>Basado en CentOS 7.3 | NVIDIA GRID 5.2, rama de controlador R384|

> [!NOTE]
> Microsoft redistribuye instaladores de controladores de NVIDIA GRID para máquinas virtuales de NV. Instale estos controladores de GRID solo en máquinas virtuales de Azure NV. Estos controladores incluyen licencias del software GRID Virtual GPU en Azure.
>

> [!WARNING] 
> La instalación de software de terceros en productos de Red Hat puede afectar a los términos de soporte técnico de Red Hat. Vea el [artículo de Knowledgebase de Red Hat](https://access.redhat.com/articles/1067).
>
