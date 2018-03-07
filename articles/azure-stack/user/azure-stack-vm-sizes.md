---
title: "Tamaños de máquinas virtuales admitidos en Azure Stack | Microsoft Docs"
description: "Referencia para los tamaños de máquinas virtuales admitidos en Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: brenduns
ms.openlocfilehash: b1a95d2510640887c6edf8e04cd7b1c7fff25275
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Tamaños de máquinas virtuales admitidos en Azure Stack

En este artículo se incluyen los tamaños de máquinas virtuales que son compatibles con Azure Stack. 


## <a name="general-purpose"></a>Uso general

### <a name="basic-a"></a>A básico
|Tamaño - Tamaño\nombre |vCPU     |Memoria | Tamaño máximo del disco temporal | Rendimiento de discos del SO máx.: (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Rendimiento de discos de datos máx. (IOPS) |
|-----------------|-----|---------|---------|-----|------|-----------|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2x300  |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4x300  |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |6 / 16X300 |

### <a name="standard-a"></a>Estándar A 
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0,768 |20   |500 |500 |1x500  |2 / 100  |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2 x 500  |2 / 500  |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4x500  |2 / 500  |
|**Standard_A3** |4 |7     |285 |500 |500 |8x500  |2 / 1000 |
|**Standard_A4** |8 |14    |605 |500 |500 |16x500 |4 / 2000 |
|**Standard_A5** |2 |14    |135 |500 |500 |4x500  |2 / 500  |
|**Standard_A6** |4 |28    |285 |500 |500 |8x500  |2 / 1000 |
|**Standard_A7** |8 |56    |605 |500 |500 |16x500 |4 / 2000 |


### <a name="d-series"></a>Serie D
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3,5 |50   |500 |3000  |4 / 4x500   |2 / 500  |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 / 1000 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16x500 |4 / 2000 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32x500 |8 / 4000 |


### <a name="ds-series"></a>Serie DS
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1000 |4000  |4 / 4x2300   |2 / 500  |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8x2300   |2 / 1000 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16x2300 |4 / 2000 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32x2300 |8 / 4000 |

### <a name="dv2-series"></a>Serie Dv2
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4x500   |2 / 750  |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 / 1500 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 / 3000 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 / 6000 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 / 6000 - 12000 |

### <a name="dsv2-series"></a>DSv2-series
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4 / 4x2300   |2 / 750  |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 / 1500 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 / 3000 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 / 6000 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 / 6000 - 12000 |


## <a name="memory-optimized"></a>Memoria optimizada

### <a name="mo-d"></a>Serie D
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8x500   |2 / 1000 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16x500 |4 / 2000 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32x500 |8 / 4000|
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 / 6000 - 8000 |

### <a name="mo-ds"></a>Series DS
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8x2300   |2 / 1000 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16x2300 |4 / 2000 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32x2300 |8 / 4000 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 / 6000 - 12000 |

### <a name="mo-dv2"></a>Serie Dv2
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 / 1500 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 / 3000 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 / 6000 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 / 6000 - 12000 |


### <a name="mo-dsv2"></a>Serie DSv2
|Tamaño     |vCPU     |Memoria (GiB) | Almacenamiento temporal (GiB)  | Rendimiento de discos del SO máx. (IOPS) | Rendimiento máximo de almacenamiento temporal (IOPS) | Discos de datos máx. / rendimiento (IOPS) | Nº máx. de NIC / ancho de banda de red esperado (Mbps) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |8 / 8x2300    |2 / 1500 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |16 / 16x2300  |2 / 3000 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |32 / 32x2300  |4 / 6000 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |64 / 64x2300  |8 / 12 000 |


## <a name="next-steps"></a>pasos siguientes

[Consideraciones sobre máquinas virtuales en Azure Stack](azure-stack-vm-considerations.md)
