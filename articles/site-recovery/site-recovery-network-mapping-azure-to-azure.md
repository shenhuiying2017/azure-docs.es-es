---
title: "Asignación de red entre dos regiones de Azure en Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Obtenga información acerca de la conmutación por error en Azure o en un centro de datos secundario."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: 9d6a806ec533259797080fbfee2c38f918ebd8a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Asignación de red entre dos regiones de Azure


En este artículo se explica cómo asignar redes virtuales de Azure de dos regiones de Azure entre sí. La asignación de red garantiza que al crear la máquina virtual replicada en la región de Azure de destino, se cree en la red virtual asignada a una red virtual de la máquina virtual de origen.  

## <a name="prerequisites"></a>Requisitos previos
Antes de asignar redes, asegúrese de haber creado [redes virtuales de Azure](../virtual-network/virtual-networks-overview.md) en las regiones de Azure de origen y destino.

## <a name="map-networks"></a>Asignar redes

Para asignar una red virtual de Azure de una región de Azure a otra red virtual de otra región, vaya a Infraestructura de Site Recovery -> Asignación de red (para máquinas virtuales de Azure) y cree una asignación de red.

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


En el ejemplo siguiente la máquina virtual se está ejecutando en la región de Asia Oriental y se está replicando en Asia Suroriental.

Seleccione la red de origen y de destino y luego haga clic en Aceptar para crear una asignación de red de Asia Oriental a Asia Suroriental.

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Haga lo mismo para crear una asignación de red de Asia Suroriental a Asia Oriental.  
![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Asignación de red al habilitar la replicación

Si la asignación de red no se hace al replicar una máquina virtual por primera vez desde una región de Azure en otra, puede elegir la red de destino como parte del mismo proceso. Site Recovery crea asignaciones de red desde la región de origen a la región de destino y desde la región de destino a la región de origen en función de esta selección.   

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

De forma predeterminada, Site Recovery crea una red en la región de destino que es idéntica a la red de origen y agrega '-asr' como sufijo al nombre de la red de origen. Puede elegir una red ya creada si hace clic en Personalizar.

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Si la asignación de red ya se ha hecho, no puede cambiar la red virtual de destino al habilitar la replicación. Para cambiarla, modifique la asignación de red existente.  

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Asignación de red](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Si modifica una asignación de red de la región 1 a la 2, asegúrese de modificar también la asignación de red de la región 2 a la 1.
>
>


## <a name="subnet-selection"></a>Selección de subred
La subred de la máquina virtual de destino se selecciona en función del nombre de la subred de la máquina virtual de origen. Si hay una subred con el mismo nombre que el de la máquina virtual de origen disponible en la red de destino, se selecciona esa para la máquina virtual de destino. Si no hay ninguna subred con el mismo nombre en la red de destino, se elige la primera red en orden alfabético como subred de destino. Puede modificar esta subred si va a la configuración de Proceso y red de la máquina virtual.

![Modificar la subred](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Dirección IP

La dirección IP de cada una de las interfaces de red de la máquina virtual de destino se elige como se indica a continuación:

### <a name="dhcp"></a>DHCP
Si la interfaz de red de la máquina virtual de origen usa DHCP, la interfaz de red de la máquina virtual de destino también se establece como DHCP.

### <a name="static-ip"></a>IP estática
Si la interfaz de red de la máquina virtual de origen usa IP estática, la interfaz de red de la máquina virtual de destino también se establece en IP estática. La IP estática se elige como se indica a continuación:

#### <a name="same-address-space"></a>Mismo espacio de direcciones

Si la subred de origen y la de destino tienen el mismo espacio de direcciones, la dirección IP de destino se establece igual que la dirección IP de la interfaz de red de la máquina virtual de origen. Si la misma dirección IP no está disponible, alguna otra dirección IP disponible se establece como dirección IP de destino.

#### <a name="different-address-space"></a>Distinto espacio de direcciones

Si la subred de origen y la de destino tienen distintos espacios de direcciones, la dirección IP de destino se establece como cualquier dirección IP disponible en la subred de destino.

Puede modificar la dirección IP de destino en cada interfaz de red si va a la configuración de Proceso y red de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Networking guidance for replicating Azure VMs](site-recovery-azure-to-azure-networking-guidance.md) (Instrucciones de redes para la replicación de máquinas virtuales de Azure).
