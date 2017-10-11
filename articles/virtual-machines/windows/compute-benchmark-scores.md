---
title: "Puntuaciones de pruebas comparativas de proceso para máquinas virtuales Windows | Microsoft Docs"
description: "Comparación de puntuaciones de pruebas comparativas de proceso de SPECint para máquinas virtuales de Azure con Windows Server"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.openlocfilehash: dc715a9bf4e5366e1719de7c649feb06b0c4bee7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Puntuaciones de pruebas comparativas de proceso para máquinas virtuales Windows
Las siguientes puntuaciones de pruebas comparativas SPECInt muestran el rendimiento de proceso para la alineación de máquinas virtuales de alto rendimiento de Azure con Windows Server. Las puntuaciones de pruebas comparativas de proceso también están disponibles para las [máquinas virtuales Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="a-series---compute-intensive"></a>Serie A: de proceso intensivo
| Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 a 2,6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 a 2,6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 a 2,6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 a 2,6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Serie Dv2
| Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |130.5 |4.4. |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 a 2,4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 a 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>Serie G, serie GS
| Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 a 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 a 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 a 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 a 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 a 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>Serie H
| Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |297,4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |575,8 |6,8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |297,0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |572,2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |5 |573,2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 a 3,2 GHz |7 |569,6 |2.8 |

## <a name="about-specint"></a>Acerca de SPECint
Las cifras de Windows se calcularon mediante la ejecución de [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) en Windows Server. SPECint se ejecutó con la opción de tasa base (SPECint_rate2006), con una copia por núcleo. SPECint consta de 12 pruebas independientes y cada una se ejecuta tres veces, tomando el valor medio de cada prueba y ponderándolo para formar una puntuación compuesta. Se muestran las pruebas que se ejecutaron en varias máquinas virtuales para proporcionar las puntuaciones promedio.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las capacidades de almacenamiento, los detalles del disco y consideraciones adicionales para seleccionar tamaños de máquinas virtuales, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

