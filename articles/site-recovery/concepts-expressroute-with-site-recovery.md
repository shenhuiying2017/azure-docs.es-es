---
title: Azure ExpressRoute con Azure Site Recovery | Microsoft Docs
description: Describe cómo usar Azure ExpressRoute con Azure Site Recovery para la recuperación ante desastres y la migración
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 7cc4c84ebae7ade4169f8d85a2d5cc11f1df6f87
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071454"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute con Azure Site Recovery

Microsoft Azure ExpressRoute le permite ampliar sus redes locales en la nube de Microsoft a través de una conexión privada que facilita un proveedor de conectividad. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure, Office 365 y Dynamics 365.

En este artículo, se describe cómo puede usar Azure ExpressRoute con Azure Site Recovery para la recuperación ante desastres y la migración.

## <a name="expressroute-circuits"></a>Circuitos ExpressRoute

Un circuito ExpressRoute representa una conexión lógica entre la infraestructura local y los servicios en la nube de Microsoft a través de un proveedor de conectividad. Puede solicitar varios circuitos ExpressRoute. Cada circuito puede estar en la misma región o en diferentes, y puede estar conectado a su entorno local mediante distintos proveedores de conectividad. Obtenga más información sobre los circuitos ExpressRoute [aquí](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Dominios de enrutamiento de ExpressRoute

Un circuito ExpressRoute tiene asociados varios dominios de enrutamiento:
-   [Emparejamiento privado de Azure](../expressroute/expressroute-circuit-peerings.md#azure-private-peering): los servicios de proceso de Azure, concretamente las máquinas virtuales (IaaS) y los servicios en la nube (PaaS), que se implementan en una red virtual pueden estar conectados mediante el dominio de emparejamiento privado. El dominio de emparejamiento privado se considera una extensión confiable de la red principal en Microsoft Azure.
-   [Emparejamiento público de Azure](../expressroute/expressroute-circuit-peerings.md#azure-public-peering): se ofrecen servicios como Azure Storage, SQL Database y Websites en direcciones IP públicas. Puede conectarse de forma privada a servicios hospedados en direcciones IP públicas (incluida las VIP de servicios en la nube) a través del dominio de enrutamiento de emparejamiento público. El emparejamiento público está en desuso para las nuevas creaciones y el emparejamiento de Microsoft debe usarse en su lugar para los servicios de PaaS de Azure.
-   [Emparejamiento de Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering): la conectividad con los servicios en línea de Microsoft (Office 365, Dynamics 365 y servicios de PaaS de Azure) se lleva a cabo a través del emparejamiento de Microsoft. El emparejamiento de Microsoft es el dominio de enrutamiento recomendado para conectarse a los servicios de PaaS de Azure.

Obtenga más información y una comparación sobre los dominios de enrutamiento de ExpressRoute [aquí](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replicación desde un sitio local en Azure con ExpressRoute

Azure Site Recovery permite la recuperación ante desastres y la migración a Azure para las [máquinas virtuales de Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuales de VMware](vmware-azure-architecture.md) y [servidores físicos](physical-azure-architecture.md) locales. Para todos los escenarios de replicación de sitios locales a Azure, los datos de replicación se envían a una cuenta de Azure Storage y se almacenan en ella. Durante la replicación, no paga ningún cargo de las máquinas virtuales. Cuando se ejecuta una conmutación por error en Azure, Site Recovery crea automáticamente máquinas virtuales IaaS de Azure.

Site Recovery replica los datos en una cuenta de Azure Storage a través de un punto de conexión público. Para usar ExpressRoute para la replicación de Site Recovery, puede usar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) o el [emparejamiento de Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). El emparejamiento de Microsoft es el dominio de enrutamiento recomendado para la replicación. Una vez que las máquinas virtuales o los servidores conmutan por error en una red virtual de Azure, puede acceder a ellos a través del [emparejamiento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). La replicación no se puede realizar a través de un enrutamiento privado.

El escenario combinado se representa en el siguiente diagrama: ![Replicación de sitio local a Azure con ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replicación de Azure a Azure con ExpressRoute

Azure Site Recovery permite la recuperación ante desastres de las [máquinas virtuales de Azure](azure-to-azure-architecture.md). En función de si las máquinas virtuales de Azure usan [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), los datos de replicación se envían a una cuenta de Azure Storage o a una instancia de Managed Disks de réplica en la región de Azure de destino. De forma predeterminada, aunque los puntos de conexión de la replicación son públicos, el tráfico de replicación de las máquinas virtuales de Azure no atraviesa Internet, independientemente de la región de Azure en la que esté la red virtual de origen. Puede invalidar la ruta del sistema predeterminada de Azure para el prefijo de dirección 0.0.0.0/0 con una [ruta personalizada](../virtual-network/virtual-networks-udr-overview.md#custom-routes) y desviar el tráfico de la máquina virtual a una aplicación virtual de red (NVA) local, pero esta configuración no se recomienda para la replicación de Site Recovery. Si va a usar rutas personalizadas, debe [crear un punto de conexión de servicio de red virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) en su red virtual de "Storage" para que el tráfico de replicación no salga de los límites de Azure.

De forma predeterminada, para la recuperación ante desastres de máquinas virtuales de Azure no se necesita ExpressRoute para la replicación. Una vez que las máquinas virtuales se conmutan por error en la región de Azure de destino, puede acceder a ellas mediante el [emparejamiento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Si ya usa ExpressRoute para conectarse desde el centro de datos local a las máquinas virtuales de Azure en la región de origen, puede planear el restablecimiento de la conectividad de ExpressRoute en la región de destino de la conmutación por error. Puede usar el mismo circuito ExpressRoute para conectarse a la región de destino a través de una nueva conexión de red virtual o usar un circuito ExpressRoute independiente y una conexión para la recuperación ante desastres. [Aquí](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute) se describen los posibles escenarios.

Puede replicar máquinas virtuales de Azure en cualquier región de Azure del mismo clúster geográfico como se detalla [aquí](../site-recovery/azure-to-azure-support-matrix.md#region-support). Si la región de Azure de destino elegida no está en la misma región geopolítica que la de origen, es posible que tenga que habilitar ExpressRoute Premium. Para obtener más información, consulte [Ubicaciones de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) y [Precios de ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre los [circuitos ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Obtenga más información sobre los [dominios de enrutamiento de ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Obtenga más información sobre las [ubicaciones de ExpressRoute](../expressroute/expressroute-locations.md).
- Obtenga más información sobre la recuperación ante desastres de [máquinas virtuales de Azure con ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
