---
title: "Rendimiento de la red de máquinas virtual de Azure | Microsoft Docs"
description: "Aprenda acerca del rendimiento de la red de máquinas virtuales de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 536a779d7de51180aa6410911dea2b6c47780c2f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="virtual-machine-network-throughput"></a>Rendimiento de red de máquinas virtuales

Azure ofrece una variedad de tamaños y tipos de máquinas virtuales, cada uno con una combinación diferente de funcionalidades de rendimiento. Entre estas funcionalidades de rendimiento es el rendimiento de red (o ancho de banda) medido en megabits por segundo (Mbps). Dado que las máquinas virtuales se hospedan en hardware compartido, la capacidad de red debe compartirse equitativamente entre las máquinas virtuales que compartan el mismo hardware. A las máquinas virtuales más grandes se les asigna relativamente más ancho de banda que las más pequeñas.
 
El ancho de banda de red asignado a cada máquina virtual se mide en el tráfico de salida de la máquina virtual. Todo el tráfico de red que deja la máquina virtual se cuenta para el límite asignado, independientemente del destino. Por ejemplo, si una máquina virtual tiene un límite de Mbps 1000, ese límite se aplica si el tráfico saliente está destinado a otra máquina virtual en la misma red virtual, o fuera de Azure.
 
La entrada no se mide o no está directamente limitada. Sin embargo, hay otros factores, como los límites de la CPU y de almacenamiento, que pueden afectar la capacidad de una máquina virtual de procesar los datos entrantes.

[Redes aceleradas](virtual-network-create-vm-accelerated-networking.md) es una característica diseñada para mejorar el rendimiento de red, incluido la utilización de la CPU, el rendimiento y la latencia. Mientras que las redes aceleradas pueden mejorar el rendimiento de una máquina virtual, puede hacerlo solo hasta el ancho de banda asignado de la máquina virtual.
 
Las máquinas virtuales de Azure deben tener una interfaz de red, pero pueden tener varias conectadas a ellas. El ancho de banda asignado a una máquina virtual es la suma de todo el tráfico saliente en todas las interfaces de red conectadas a una máquina virtual. En otras palabras, el ancho de banda asignado es por máquina virtual, independientemente de la cantidad de interfaces de red conectadas a la máquina virtual. Para obtener información sobre la cantidad de interfaces de red que admiten los distintos tamaños de máquinas virtuales de Azure, vea los tamaños de máquinas virtuales [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="expected-network-throughput"></a>Rendimiento esperado de la red

El rendimiento de salida esperado y el número de interfaces de red compatibles con cada tamaño de máquina virtual se detallan en los tamaños de máquinas virtuales [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Azure. Seleccione un tipo, como fin general, y luego seleccione una serie de tamaños en la página resultante, como la serie Dv2. Cada serie tiene una tabla con las especificaciones de red en la última columna con el nombre **N.º máx. NIC/rendimiento de red esperado (Mbps)**. 

El límite de rendimiento se aplica a la máquina virtual. El rendimiento no se ve afectado por los siguientes factores:
- **Número de interfaces de red**: el límite de ancho de banda es el cúmulo de todo el tráfico saliente de la máquina virtual.
- **Redes aceleradas**: aunque la característica puede ser útil para lograr el límite publicado, no cambia el límite.
- **Destino del tráfico**: todos los destinos se cuentan para el límite de salida.
- **Protocolo**: todo el tráfico saliente a través de todos los protocolos cuenta para el límite.

## <a name="next-steps"></a>Pasos siguientes

- [Optimización del rendimiento de red en un sistema operativo de máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Prueba de rendimiento de red](virtual-network-bandwidth-testing.md) para una máquina virtual.