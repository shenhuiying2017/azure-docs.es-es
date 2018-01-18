---
title: "Introducción a las zonas de disponibilidad | Microsoft Docs"
description: "Este artículo ofrece información general sobre las zonas de disponibilidad en Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: c3a1758ab965d22eabfe44a410e9f72420e0148b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Introducción a las zonas de disponibilidad en Azure (versión preliminar)

Las zonas de disponibilidad ayudan a protegerse frente a errores en el nivel de centro de datos. Se encuentran dentro de una región de Azure y cada una de ellas tiene sus fuentes de alimentación, redes y sistemas de refrigeración propios e independientes. Para garantizar la resistencia, hay un mínimo de tres zonas independientes en todas las regiones habilitadas. La separación física y lógica de las zonas de disponibilidad dentro de una región protege las aplicaciones y los datos frente a errores en el nivel de zona. 

![vista conceptual de una zona fuera de servicio en una región](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regiones que admiten zonas de disponibilidad

- Este de EE. UU. 2
- Europa occidental
- Centro de Francia

## <a name="services-that-support-availability-zones"></a>Servicios que admiten zonas de disponibilidad

Los servicios de Azure que admiten zonas de disponibilidad son:

- Máquinas virtuales Linux
- Máquinas virtuales Windows
- Conjuntos de escalado de máquinas virtuales de zona
- Managed Disks
- Load Balancer
- Dirección IP pública

## <a name="get-started-with-the-availability-zones-preview"></a>Introducción a la versión preliminar de las zonas de disponibilidad

La versión preliminar de las zonas de disponibilidad está disponible en las regiones Este de EE. UU. 2, Europa Occidental y Centro de Francia para servicios específicos de Azure. 

1. [Registro para la versión preliminar de las zonas de disponibilidad](http://aka.ms/azenroll). 
2. Inicie sesión en la suscripción de Azure.
3. Elija una región que admita zonas de disponibilidad.
4. Utilice uno de los siguientes vínculos para comenzar a usar las zonas de disponibilidad con el servicio. 
    - [Creación de una máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Creación de un conjunto de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [Agregación de un disco administrado mediante PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Equilibrador de carga](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>pasos siguientes
- [Plantillas de inicio rápido](http://aka.ms/azqs)
