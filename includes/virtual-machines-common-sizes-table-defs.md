---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 30f6feb920d78c9c325c5556f5530ac4c8d9459b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/24/2018
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definiciones de tabla de tamaño

- La capacidad de almacenamiento se muestra en unidades de GiB o 1024^3 bytes. Cuando compare discos que se miden en GB (1000^3 bytes) con discos que se miden en GiB (1024^3), recuerde que los números que representan la capacidad en GiB pueden parecer más pequeños. Por ejemplo, 1023 GiB = 1098.4 GB
- Se midió el rendimiento de disco en operaciones de entrada/salida por segundo (E/S por segundo) y Mbps, donde Mbps = 10^6 bytes/s.
- Los discos de datos pueden funcionar en modo en caché o en modo no en caché. En el caso de la operación de disco de datos en caché, el modo de caché del host está establecido en **ReadOnly** o **ReadWrite**.  En el caso de la operación de disco de datos no en caché, el modo de caché del host está definido en **None**.
-   Si desea obtener el mejor rendimiento para las VM, debe limitar el número de discos de datos a 2 discos por vCPU.
- **El ancho de banda de red esperado** es el [ancho de banda agregado máximo asignado por tipo de máquina virtual](../articles/virtual-network/virtual-machine-network-throughput.md) en todas las NIC y para todos los destinos. Los límites superiores no están garantizados pero están diseñados para proporcionar una guía a la hora de seleccionar el tipo correcto de máquina virtual para la aplicación deseada. El rendimiento de red real dependerá de diversos factores (como, por ejemplo, la congestión de la red, las cargas de la aplicación y la configuración de red). Para más información acerca de cómo optimizar el rendimiento de red, consulte [Optimización del rendimiento de red para Windows y Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para lograr el rendimiento de red esperado en Linux o Windows, puede que sea necesario seleccionar una versión específica u optimizar la máquina virtual. Para más información, consulte [Pruebas confiables para el rendimiento de máquinas virtuales](../articles/virtual-network/virtual-network-bandwidth-testing.md).



