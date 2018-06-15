---
title: Puntuaciones de pruebas comparativas de proceso para máquinas virtuales Linux de Azure | Microsoft Docs
description: Comparación de puntuaciones de pruebas comparativas de proceso de CoreMark para máquinas virtuales de Azure con Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 07e313404b1d57fe4c851ab182823bcea454cbec
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31425602"
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Puntuaciones de pruebas comparativas de proceso para máquinas virtuales Linux
Las siguientes puntuaciones de pruebas comparativas CoreMark muestran el rendimiento de proceso para la alineación de máquinas virtuales de alto rendimiento de Azure con Ubuntu. Las puntuaciones de pruebas comparativas de proceso también están disponibles para las [máquinas virtuales de Windows](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="av2---general-compute"></a>Av2: proceso general
(3/23/2018 7:32:49 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 1 | 1 | 1.9 | 6514 | 56 | 0,86 % | 119 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 1.9 | 6162 | 195 | 3,17 % | 70 |
| Standard_A1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 1 | 1 | 1.9 | 6505 | 425 | 6,53 % | 63 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 3.9 | 13 061 | 282 | 2,16 % | 112 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 3.9 | 12 270 | 390 | 3,18 % | 56 |
| Standard_A2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 3.9 | 13 406 | 793 | 5,91 % | 35 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 16.0 | 13 148 | 130 | 0,98 % | 84 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 16.0 | 12 450 | 563 | 4,52 % | 35 |
| Standard_A2m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 16.0 | 12 929 | 988 | 7,64 % | 56 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 8.0 | 26 037 | 356 | 1,37 % | 70 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 8.0 | 24 728 | 594 | 2,40 % | 77 |
| Standard_A4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 8.0 | 26 549 | 1375 | 5,18 % | 42 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 32,1 | 25 865 | 672 | 2,60 % | 70 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 32,1 | 24 646 | 1104 | 4,48 % | 63 |
| Standard_A4m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 32,1 | 25 058 | 1292 | 5,15 % | 35 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 16.0 | 52 963 | 694 | 1,31 % | 98 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 16.0 | 49 908 | 970 | 1,94 % | 35 |
| Standard_A8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 16.0 | 48 584 | 742 | 1,53 % | 77 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 2 | 64,4 | 52 900 | 815 | 1,54 % | 133 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 64,4 | 49 733 | 861 | 1,73 % | 84 |
| Standard_A8m_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 64,4 | 48 494 | 501 | 1,03 % | 49 |

## <a name="a0-7-standard-general-compute"></a>A0-7: proceso general estándar
(3/23/2018 9:06:07 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 | Procesador AMD Opteron(tm) 4171 HE | 1 | 1 | 0.6 | 3556 | 14 | 0,39 % | 21 |
| Standard_A0 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 1 | 1 | 0.6 | 3137 | 16 | 0,51 % | 70 |
| Standard_A0 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 0.6 | 3146 | 134 | 4,27 % | 63 |
| Standard_A1 | Procesador AMD Opteron(tm) 4171 HE | 1 | 1 | 1.7 | 6862 | 169 | 2,47 % | 42 |
| Standard_A1 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 1 | 1 | 1.7 | 6471 | 104 | 1,61 % | 98 |
| Standard_A1 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 1.7 | 6211 | 262 | 4,22 % | 98 |
| Standard_A2 | Procesador AMD Opteron(tm) 4171 HE | 2 | 1 | 3.4 | 13 950 | 415 | 2,98 % | 35 |
| Standard_A2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 3.4 | 13 205 | 187 | 1,41 % | 112 |
| Standard_A2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 3.4 | 12 563 | 639 | 5,09 % | 84 |
| Standard_A3 | Procesador AMD Opteron(tm) 4171 HE | 4 | 1 | 6.9 | 28 069 | 679 | 2,42 % | 28 |
| Standard_A3 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 6.9 | 26 238 | 236 | 0,90 % | 98 |
| Standard_A3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 6.9 | 25 195 | 827 | 3,28 % | 77 |
| Standard_A4 | Procesador AMD Opteron(tm) 4171 HE | 8 | 2 | 14,0 | 56 604 | 305 | 0,54 % | 7 |
| Standard_A4 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 14,0 | 53 271 | 577 | 1,08 % | 63 |
| Standard_A4 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 14,0 | 49 744 | 1118 | 2,25 % | 147 |
| Standard_A5 | Procesador AMD Opteron(tm) 4171 HE | 2 | 1 | 14,0 | 14 164 | 273 | 1,93 % | 21 |
| Standard_A5 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 14,0 | 13 136 | 151 | 1,15 % | 98 |
| Standard_A5 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 14,0 | 12 510 | 615 | 4,92 % | 84 |
| Standard_A6 | Procesador AMD Opteron(tm) 4171 HE | 4 | 1 | 28,1 | 28 272 | 392 | 1,39 % | 28 |
| Standard_A6 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 28,1 | 26 165 | 294 | 1,13 % | 105 |
| Standard_A6 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 28,1 | 25 178 | 1009 | 4,01 % | 70 |
| Standard_A7 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 56,3 | 52 949 | 1114 | 2,10 % | 112 |
| Standard_A7 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 56,3 | 49 677 | 894 | 1,80 % | 133 |

## <a name="dsv3---general-compute--premium-storage"></a>DSv3: proceso general y almacenamiento Premium
(3/23/2018 7:28:44 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 8.0 | 20 259 | 729 | 3,60 % | 140 |
| Standard_D2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 8.0 | 20 364 | 1007 | 4,94 % | 70 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 16.0 | 39 662 | 1757 | 4,43 % | 182 |
| Standard_D4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 16.0 | 40 632 | 2422 | 5,96 % | 168 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 32,1 | 80 055 | 1022 | 1,28 % | 133 |
| Standard_D8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 32,1 | 80 639 | 2844 | 3,53 % | 56 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 64,4 | 158 407 | 3267 | 2,06 % | 119 |
| Standard_D16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 64,4 | 154 146 | 4432 | 2,88 % | 63 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 32 | 2 | 128,9 | 307 408 | 3203 | 1,04 % | 70 |
| Standard_D32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 1 | 128,9 | 309 183 | 3132 | 1,01 % | 56 |
| Standard_D32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 128,9 | 80 160 | 3912 | 4,88 % | 84 |
| Standard_D32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 128,9 | 84 406 | 1939 | 2,30 % | 70 |
| Standard_D32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 32 | 2 | 128,9 | 157 154 | 2152 | 1,37 % | 84 |
| Standard_D32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 1 | 128,9 | 155 460 | 3663 | 2,36 % | 112 |
| Standard_D64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 257,9 | 615 873 | 7266 | 1,18 % | 140 |
| Standard_D64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 2 | 257,9 | 170 309 | 2169 | 1,27 % | 98 |
| Standard_D64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 257,9 | 306 578 | 4095 | 1,34 % | 98 |

## <a name="dv3---general-compute"></a>Dv3: proceso general
(3/23/2018 7:32:37 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 8.0 | 20 791 | 1531 | 7,36 % | 175 |
| Standard_D2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 8.0 | 21 326 | 1622 | 7,61 % | 84 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 16.0 | 39 978 | 1853 | 4,63 % | 98 |
| Standard_D4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 16.0 | 41 842 | 2798 | 6,69 % | 77 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 32,1 | 80 559 | 1990 | 2,47 % | 154 |
| Standard_D8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 32,1 | 79 711 | 4368 | 5,48 % | 63 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 64,4 | 160 309 | 3371 | 2,10 % | 133 |
| Standard_D16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 64,4 | 155 447 | 3426 | 2,20 % | 56 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 32 | 2 | 128,9 | 309 021 | 4128 | 1,34 % | 105 |
| Standard_D32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 1 | 128,9 | 311 375 | 3714 | 1,19 % | 49 |
| Standard_D64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 257,9 | 613 424 | 19 225 | 3,13 % | 84 |

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3: optimizada para memoria y almacenamiento Premium
(3/23/2018 7:31:01 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_E2s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 16.0 | 21 015 | 1112 | 5,29 % | 210 |
| Standard_E4s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 32,1 | 40 691 | 1928 | 4,74 % | 287 |
| Standard_E8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 64,4 | 79 841 | 2856 | 3,58 % | 161 |
| Standard_E16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 128,9 | 155 976 | 2666 | 1,71 % | 161 |
| Standard_E32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 2 | 257,9 | 297 695 | 8535 | 2,87 % | 140 |
| Standard_E32-8s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 2 | 257,9 | 86 375 | 7300 | 8,45 % | 140 |
| Standard_E32-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 2 | 257,9 | 158 842 | 10 809 | 6,81 % | 189 |
| Standard_E64s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 435,3 | 613 160 | 8637 | 1,41 % | 63 |
| Standard_E64-16s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 2 | 435,3 | 170 343 | 2052 | 1,20 % | 126 |
| Standard_E64-32s_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 435,3 | 307 110 | 3759 | 1,22 % | 126 |
| Standard_E64is_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 435,3 | 613 892 | 7763 | 1,26 % | 140 |

## <a name="ev3---memory-optimized"></a>Ev3: optimizada para memoria
(3/23/2018 7:29:35 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_E2_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 16.0 | 23 318 | 2734 | 11,72 % | 245 |
| Standard_E4_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 32,1 | 42 612 | 3834 | 9,00 % | 154 |
| Standard_E8_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 64,4 | 83 488 | 4888 | 5,85 % | 189 |
| Standard_E16_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 128,9 | 159 537 | 4999 | 3,13 % | 175 |
| Standard_E32_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 32 | 2 | 257,9 | 306 311 | 8547 | 2,79 % | 196 |
| Standard_E64_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 435,3 | 605 599 | 36 245 | 5,98 % | 168 |
| Standard_E64i_v3 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 64 | 2 | 435,3 | 618 198 | 10 022 | 1,62 % | 154 |

## <a name="dsv2---storage-optimized"></a>DSv2: optimizada para almacenamiento
(3/23/2018 7:30:03 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 3.4 | 14 691 | 626 | 4,26 % | 182 |
| Standard_DS1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 1 | 1 | 3.4 | 14 577 | 1120 | 7,68 % | 63 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 6.9 | 29 156 | 1095 | 3,76 % | 91 |
| Standard_DS2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 6.9 | 29 157 | 1945 | 6,67 % | 56 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 14,0 | 55 981 | 1625 | 2,90 % | 98 |
| Standard_DS3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 14,0 | 57 921 | 2628 | 4,54 % | 77 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 28,1 | 115 636 | 1963 | 1,70 % | 161 |
| Standard_DS4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 28,1 | 111 527 | 494 | 0,44 % | 14 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 56,3 | 220 333 | 4856 | 2,20 % | 91 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 56,3 | 217 812 | 5320 | 2,44 % | 35 |
| Standard_DS5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 56,3 | 222 116 | 4445 | 2,00 % | 56 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 14,0 | 28 364 | 929 | 3,28 % | 105 |
| Standard_DS11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 14,0 | 28 772 | 1351 | 4,69 % | 70 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 28,1 | 56 130 | 2174 | 3,87 % | 119 |
| Standard_DS12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 28,1 | 58 490 | 2670 | 4,56 % | 63 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 56,3 | 115 107 | 2525 | 2,19 % | 126 |
| Standard_DS13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 56,3 | 111 429 | 1111 | 1,00 % | 35 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 56,3 | 28 933 | 1176 | 4,06 % | 154 |
| Standard_DS13-2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 56,3 | 30 336 | 1635 | 5,39 % | 98 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 56,3 | 57 644 | 1893 | 3,28 % | 126 |
| Standard_DS13-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 56,3 | 57 927 | 1306 | 2,26 % | 84 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 112,8 | 218 363 | 3866 | 1,77 % | 154 |
| Standard_DS14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 112,8 | 224 791 | 4363 | 1,94 % | 77 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 2 | 112,8 | 56 398 | 2675 | 4,74 % | 126 |
| Standard_DS14-4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 112,8 | 61 709 | 1468 | 2,38 % | 49 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 2 | 112,8 | 112 282 | 4368 | 3,89 % | 147 |
| Standard_DS14-8_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 112,8 | 111 469 | 3747 | 3,36 % | 49 |
| Standard_DS15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 20 | 2 | 141,0 | 271 529 | 3749 | 1,38 % | 189 |

## <a name="dv2---general-compute"></a>Dv2: proceso general
(3/23/2018 7:27:28 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 3.4 | 15 027 | 963 | 6,41 % | 105 |
| Standard_D1_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 1 | 1 | 3.4 | 15 296 | 1696 | 11,09 % | 77 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 6.9 | 29 060 | 1405 | 4,83 % | 133 |
| Standard_D2_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 6.9 | 31 552 | 2315 | 7,34 % | 63 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 14,0 | 57 666 | 1559 | 2,70 % | 168 |
| Standard_D3_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 14,0 | 58 312 | 3640 | 6,24 % | 77 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 28,1 | 114 708 | 2019 | 1,76 % | 147 |
| Standard_D4_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 28,1 | 114 971 | 5415 | 4,71 % | 42 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 56,3 | 221 546 | 5093 | 2,30 % | 112 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 56,3 | 214 933 | 3741 | 1,74 % | 21 |
| Standard_D5_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 56,3 | 225 410 | 5421 | 2,41 % | 77 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 14,0 | 29 125 | 1267 | 4,35 % | 154 |
| Standard_D11_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 14,0 | 31 315 | 1762 | 5,63 % | 63 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 28,1 | 57 386 | 1712 | 2,98 % | 168 |
| Standard_D12_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 28,1 | 57 903 | 3322 | 5,74 % | 112 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 56,3 | 114 319 | 2482 | 2,17 % | 126 |
| Standard_D13_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 56,3 | 114 933 | 3486 | 3,03 % | 49 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 112,8 | 219 594 | 5752 | 2,62 % | 119 |
| Standard_D14_v2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 112,8 | 225 880 | 3699 | 1,64 % | 77 |
| Standard_D15_v2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 20 | 2 | 141,0 | 275 417 | 6457 | 2,34 % | 154 |

## <a name="d---general-compute"></a>D: proceso general
(3/23/2018 7:28:16 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 1 | 1 | 3.4 | 10 331 | 303 | 2,93 % | 112 |
| Standard_D1 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 3.4 | 14 547 | 916 | 6,30 % | 14 |
| Standard_D2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 6.9 | 21 112 | 383 | 1,81 % | 119 |
| Standard_D2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 6.9 | 27 545 | 173 | 0,63 % | 7 |
| Standard_D3 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 14,0 | 41 910 | 974 | 2,32 % | 133 |
| Standard_D3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 14,0 | 57 567 | 2166 | 3,76 % | 21 |
| Standard_D4 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 28,1 | 85 255 | 971 | 1,14 % | 119 |
| Standard_D4 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 28,1 | 112 324 | 1367 | 1,22 % | 14 |
| Standard_D11 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 14,0 | 21 172 | 479 | 2,26 % | 140 |
| Standard_D11 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 14,0 | 29 935 | 1702 | 5,69 % | 14 |
| Standard_D12 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 28,1 | 41 788 | 1185 | 2,84 % | 98 |
| Standard_D12 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 28,1 | 57 105 | 761 | 1,33 % | 28 |
| Standard_D13 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 56,3 | 85 180 | 1395 | 1,64 % | 119 |
| Standard_D13 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 56,3 | 113 421 | 1695 | 1,49 % | 28 |
| Standard_D14 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 16 | 2 | 112,8 | 165 497 | 3376 | 2,04 % | 105 |
| Standard_D14 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 112,8 | 217 813 | 4869 | 2,24 % | 35 |

## <a name="ds---storage-optimized"></a>DS: optimizada para almacenamiento
(3/23/2018 7:34:52 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 1 | 1 | 3.4 | 10 666 | 134 | 1,25 % | 126 |
| Standard_DS11 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 14,0 | 21 330 | 268 | 1,26 % | 49 |
| Standard_DS12 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 28,1 | 42 225 | 555 | 1,31 % | 126 |
| Standard_DS12 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 28,1 | 56 288 | 976 | 1,73 % | 14 |
| Standard_DS13 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 56,3 | 85 292 | 1303 | 1,53 % | 98 |
| Standard_DS14 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 16 | 2 | 112,8 | 162 996 | 3911 | 2,40 % | 70 |
| Standard_DS14 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 112,8 | 217 038 | 3540 | 1,63 % | 28 |
| Standard_DS2 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 2 | 1 | 6.9 | 21 398 | 193 | 0,90 % | 119 |
| Standard_DS3 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 4 | 1 | 14,0 | 42 470 | 559 | 1,32 % | 126 |
| Standard_DS3 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 14,0 | 55 664 | 385 | 0,69 % | 7 |
| Standard_DS4 | Intel(R) Xeon(R) CPU E5-2660 0 a 2,20 GHz | 8 | 1 | 28,1 | 84 956 | 1087 | 1,28 % | 105 |

## <a name="fsv2---compute--storage-optimized"></a>Fsv2: proceso y optimizada para almacenamiento
(3/23/2018 7:33:11 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F2s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 2 | 1 | 3.9 | 25 392 | 157 | 0,62 % | 49 |
| Standard_F4s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 4 | 1 | 8.0 | 48 065 | 656 | 1,36 % | 42 |
| Standard_F8s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 8 | 1 | 16.0 | 95 026 | 1205 | 1,27 % | 49 |
| Standard_F16s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 16 | 1 | 32,1 | 187 039 | 2681 | 1,43 % | 42 |
| Standard_F32s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 32 | 1 | 64,4 | 375 828 | 5180 | 1,38 % | 70 |
| Standard_F64s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 64 | 2 | 128,9 | 741 859 | 5954 | 0,80 % | 49 |
| Standard_F72s_v2 | Intel(R) Xeon(R) Platinum 8168 CPU a 2,70 GHz | 72 | 2 | 145,0 | 831 616 | 4867 | 0,59 % | 42 |

## <a name="f---compute-optimized"></a>F: optimizada para proceso
(3/23/2018 7:28:54 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 1.9 | 14 762 | 815 | 5,52 % | 175 |
| Standard_F1 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 1 | 1 | 1.9 | 15 756 | 1653 | 10,49 % | 91 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 3.9 | 29 265 | 1593 | 5,44 % | 182 |
| Standard_F2 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 3.9 | 32 105 | 3072 | 9,57 % | 70 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 8.0 | 57 174 | 2009 | 3,51 % | 182 |
| Standard_F4 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 8.0 | 57 613 | 3076 | 5,34 % | 91 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 16.0 | 114 851 | 1983 | 1,73 % | 182 |
| Standard_F8 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 16.0 | 113 706 | 2797 | 2,46 % | 105 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 32,1 | 220 641 | 5114 | 2,32 % | 140 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 32,1 | 217 174 | 4952 | 2,28 % | 28 |
| Standard_F16 | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 32,1 | 226 457 | 5507 | 2,43 % | 49 |

## <a name="fs---compute-and-storage-optimized"></a>Fs: proceso y optimizada para almacenamiento
(3/23/2018 7:30:14 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 1 | 1 | 1.9 | 14 630 | 678 | 4,64 % | 203 |
| Standard_F1s | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 1 | 1 | 1.9 | 15 247 | 801 | 5,25 % | 91 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 2 | 1 | 3.9 | 28 931 | 1105 | 3,82 % | 133 |
| Standard_F2s | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 2 | 1 | 3.9 | 29 079 | 1454 | 5,00 % | 77 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 4 | 1 | 8.0 | 56 674 | 1518 | 2,68 % | 189 |
| Standard_F4s | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 4 | 1 | 8.0 | 57 051 | 2872 | 5,03 % | 91 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 8 | 1 | 16.0 | 113 709 | 11 105 | 9,77 % | 196 |
| Standard_F8s | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 8 | 1 | 16.0 | 111 477 | 2531 | 2,27 % | 70 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 1 | 32,1 | 222 637 | 4405 | 1,98 % | 140 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v3 a 2,40 GHz | 16 | 2 | 32,1 | 218 297 | 4284 | 1,96 % | 14 |
| Standard_F16s | Intel(R) Xeon(R) CPU E5-2673 v4 a 2,30 GHz | 16 | 1 | 32,1 | 225 001 | 3033 | 1,35 % | 98 |

## <a name="g---compute-optimized"></a>G: optimizada para proceso
(3/23/2018 7:27:25 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 2 | 1 | 28,1 | 32 071 | 4239 | 13,22 % | 182 |
| Standard_G2 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 4 | 1 | 56,3 | 60 598 | 6048 | 9,98 % | 175 |
| Standard_G3 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 8 | 1 | 112,8 | 111 058 | 6536 | 5,89 % | 161 |
| Standard_G4 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 16 | 1 | 225,7 | 200 516 | 1833 | 0,91 % | 154 |
| Standard_G5 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 32 | 2 | 451,5 | 395 591 | 5192 | 1,31 % | 154 |

## <a name="gs---storage-optimized"></a>GS: optimizada para almacenamiento
(3/23/2018 7:25:12 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 2 | 1 | 28,1 | 28 771 | 2006 | 6,97 % | 231 |
| Standard_GS2 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 4 | 1 | 56,3 | 54 947 | 3699 | 6,73 % | 203 |
| Standard_GS3 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 8 | 1 | 112,8 | 105 054 | 4441 | 4,23 % | 182 |
| Standard_GS4 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 16 | 1 | 225,7 | 199 189 | 12 092 | 6,07 % | 168 |
| Standard_GS4-4 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 4 | 1 | 225,7 | 59 066 | 2935 | 4,97 % | 196 |
| Standard_GS4-8 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 8 | 1 | 225,7 | 107 076 | 3209 | 3,00 % | 168 |
| Standard_GS5 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 32 | 2 | 451,5 | 386 803 | 9303 | 2,41 % | 161 |
| Standard_GS5-8 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 8 | 2 | 451,5 | 116 094 | 1935 | 1,67 % | 42 |
| Standard_GS5-16 | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 16 | 2 | 451,5 | 209 500 | 2622 | 1,25 % | 63 |

## <a name="h---high-performance-compute-hpc"></a>H: Proceso de alto rendimiento (HPC)
(3/23/2018 7:27:16 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 8 | 1 | 56,3 | 140 445 | 2840 | 2,02 % | 147 |
| Standard_H8m | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 8 | 1 | 112,8 | 141 086 | 2209 | 1,57 % | 147 |
| Standard_H16 | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 16 | 2 | 112,8 | 270 129 | 6502 | 2,41 % | 56 |
| Standard_H16m | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 16 | 2 | 225,7 | 272 667 | 6686 | 2,45 % | 112 |
| Standard_H16mr | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 16 | 2 | 225,7 | 272 683 | 6484 | 2,38 % | 70 |
| Standard_H16r | Intel(R) Xeon(R) CPU E5-2667 v3 a 3,20 GHz | 16 | 2 | 112,8 | 271 822 | 6126 | 2,25 % | 98 |

## <a name="hpc---a8-11"></a>Proceso de alto rendimiento: A8-11
(3/23/2018 7:35:10 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 | Intel(R) Xeon(R) CPU E5-2670 0 a 2,60 GHz | 8 | 1 | 56,3 | 117 148 | 1877 | 1,60 % | 189 |
| Standard_A9 | Intel(R) Xeon(R) CPU E5-2670 0 a 2,60 GHz | 16 | 2 | 112,8 | 225 608 | 7532 | 3,34 % | 147 |
| Standard_A10 | Intel(R) Xeon(R) CPU E5-2670 0 a 2,60 GHz | 8 | 1 | 56,3 | 117 638 | 1988 | 1,69 % | 168 |
| Standard_A11 | Intel(R) Xeon(R) CPU E5-2670 0 a 2,60 GHz | 16 | 2 | 112,8 | 225 980 | 7067 | 3,13 % | 161 |

## <a name="ls---storage-optimized"></a>Ls: optimizada para almacenamiento
(3/23/2018 7:58:51 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_L4s | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 4 | 1 | 32,1 | 55 962 | 3567 | 6,37 % | 154 |
| Standard_L8s | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 8 | 1 | 64,4 | 106 482 | 3178 | 2,98 % | 168 |
| Standard_L16s | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 16 | 1 | 128,9 | 197 906 | 12 605 | 6,37 % | 168 |
| Standard_L32s | Intel(R) Xeon(R) CPU E5-2698B v3 a 2,00 GHz | 32 | 2 | 257,9 | 388 652 | 6274 | 1,61 % | 126 |

## <a name="m---memory-optimized"></a>M: optimizada para memoria
(3/23/2018 8:57:07 PM pbi 2050259)

| Tamaño de VM | CPU | vCPU | Nodos NUMA | Memoria (GiB) | Puntuación media | StdDev | % Devest | N. º de ejecuciones |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_M64-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 64 | 2 | 1806,2 | 339 412 | 4655 | 1,37 % | 21 |
| Standard_M64ms | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 64 | 2 | 1806,2 | 662 070 | 16 539 | 2,50 % | 70 |
| Standard_M64s | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 64 | 2 | 1032,1 | 659 757 | 22 439 | 3,40 % | 21 |
| Standard_M128-32ms | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 64 | 4 | 3923,0 | 332 861 | 6061 | 1,82 % | 42 |
| Standard_M128-64ms | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 128 | 4 | 3923,0 | 656 788 | 16 341 | 2,49 % | 35 |
| Standard_M128ms | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 128 | 4 | 3923,0 | 1 275 328 | 16 544 | 1,30 % | 70 |
| Standard_M128s | Intel(R) Xeon(R) CPU E7-8890 v3 a 2,50 GHz | 128 | 4 | 2064,3 | 1 275 445 | 19 510 | 1,53 % | 42 |

## <a name="about-coremark"></a>Acerca de CoreMark
Los números de Linux se procesaron ejecutando [CoreMark](http://www.eembc.org/coremark/faq.php) en Ubuntu. CoreMark se configuró con el número de subprocesos establecido en el número de CPU virtuales y la simultaneidad establecida en PThreads. El número objetivo de iteraciones se ajustó basándose en el rendimiento esperado para proporcionar un tiempo de ejecución de al menos 20 segundos (suele ser mucho mayor). La puntuación final representa el número de iteraciones completadas dividido entre el número de segundos que tardó en ejecutarse la prueba. Cada prueba se ejecutó al menos siete veces en cada máquina virtual. Las fechas de la serie de pruebas aparecen más arriba. La prueba se ejecuta en varias máquinas virtuales de las regiones públicas de Azure donde eran compatibles en la fecha de ejecución. Las series A y B (ampliables) no aparecen porque el rendimiento es variable. La serie N no aparece porque se basan en la GPU y Coremark no mide el rendimiento de la GPU.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las capacidades de almacenamiento, los detalles del disco y consideraciones adicionales para seleccionar tamaños de máquinas virtuales, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para ejecutar los scripts de CoreMark en máquinas virtuales Linux, descargue el [paquete de scripts de CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

