---
title: Introducción a las zonas de disponibilidad de Azure | Microsoft Docs
description: En este artículo se proporciona información general sobre cómo usar las zonas de disponibilidad para crear aplicaciones resistentes y de alta disponibilidad en Azure
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: a4133779538e412a19a11de678b1527fb8023a87
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="overview-of-availability-zones-in-azure"></a>Introducción a las zonas de disponibilidad en Azure
Las zonas de disponibilidad son una oferta que protege las aplicaciones y datos de los errores del centro de datos. Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. Para garantizar la resistencia, hay tres zonas independientes como mínimo en todas las regiones habilitadas. La separación física de las zonas de disponibilidad dentro de una región protege las aplicaciones y los datos frente a los errores del centro de datos. Los servicios de redundancia de zona replican las aplicaciones y los datos entre zonas de disponibilidad para protegerlos frente a puntos de error únicos. Con las zonas de disponibilidad, Azure ofrece el mejor Acuerdo de Nivel de Servicio del sector de tiempo de actividad de máquina virtual, con un 99,99 %. En el [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo se explica la disponibilidad garantizada de Azure como un conjunto.

Coloque sus recursos de proceso, almacenamiento, red y datos dentro de una zona y replíquelos en otras para conseguir una alta disponibilidad en la arquitectura de sus aplicaciones. Los servicios de Azure que admiten zonas de disponibilidad se dividen en dos categorías:

- **Servicios de zona**: ancle el recurso a una zona específica (por ejemplo, máquinas virtuales, discos administrados, direcciones IP)
- **Servicios de redundancia de zona**: la plataforma se replica automáticamente entre zonas (por ejemplo, almacenamiento con redundancia de zona, SQL Database).

Para lograr una continuidad del negocio integral en Azure, cree la arquitectura de aplicación mediante la combinación de zonas de disponibilidad y pares de regiones de Azure. Puede replicar de forma sincrónica las aplicaciones y los datos mediante zonas de disponibilidad dentro de una región de Azure para conseguir alta disponibilidad, y replicar de forma asincrónica entre regiones de Azure para la protección de recuperación ante desastres.
 
![vista conceptual de una zona fuera de servicio en una región](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regiones que admiten zonas de disponibilidad

- Centro de EE. UU.
- Centro de Francia
- Este de EE. UU. 2 (versión preliminar)
- Europa Occidental (versión preliminar)
- Sudeste Asiático (versión preliminar)


## <a name="services-that-support-availability-zones"></a>Servicios que admiten zonas de disponibilidad
Los servicios de Azure que admiten zonas de disponibilidad son:

- Máquinas virtuales Linux
- Máquinas virtuales Windows
- Conjuntos de escalado de máquina virtual
- Managed Disks
- Load Balancer
- Dirección IP pública
- Almacenamiento con redundancia de zona
- SQL Database


## <a name="pricing"></a>Precios
No hay ningún costo adicional asociado a las máquinas virtuales implementadas en una zona de disponibilidad. Cuando dos o más máquinas virtuales se implementan en dos o más zonas de disponibilidad dentro de una región de Azure, se ofrece un Acuerdo de Nivel de Servicio de tiempo de actividad de máquina virtual del 99,99 %. Habrá gastos adicionales de transferencia de datos de máquina virtual a máquina virtual entre zonas de disponibilidad. Para más información, revise la página [Precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introducción a las zonas de disponibilidad
- [Creación de una máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Agregación de un disco administrado mediante PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creación de un conjunto de escalado de máquinas virtuales con redundancia de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Equilibrio de carga de máquinas virtuales en distintas zonas con un equilibrador de carga estándar con un front-end con redundancia de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Equilibrio de carga de máquinas virtuales dentro de una zona con un equilibrador de carga estándar con un front-end de zona](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Almacenamiento con redundancia de zona](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>Pasos siguientes
- [Plantillas de inicio rápido](http://aka.ms/azqs)
