---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos y controladores compatibles

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>Serie NC, NCv2, NCv3 y ND: controladores de NVIDIA Tesla

| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> Los vínculos de descarga de controladores de Tesla están actualizados en el momento de la publicación. Para ver los controladores más recientes, visite el sitio web de [NVIDIA](http://www.nvidia.com/).
>

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: controladores de NVIDIA GRID

| SO | Controlador |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft redistribuye instaladores de controladores de NVIDIA GRID para máquinas virtuales de NV. Instale estos controladores de GRID solo en máquinas virtuales de Azure NV. Estos controladores incluyen licencias del software GRID Virtual GPU en Azure.
>