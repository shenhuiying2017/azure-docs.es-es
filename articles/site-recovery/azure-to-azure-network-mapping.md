---
title: "Asignación de redes virtuales entre dos regiones de Azure en Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Obtenga información sobre la conmutación por error en Azure o en un centro de datos secundario."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: d7dd35a8382f4a99ababbe804c5c71b29148c44a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Asignación de redes virtuales en regiones diferentes de Azure


En este artículo se describe cómo asignar entre sí dos instancias de Azure Virtual Network ubicadas en regiones de Azure diferentes. La asignación de red garantiza que al crear la máquina virtual replicada en la región de Azure de destino, también se cree la máquina virtual en la red virtual asignada a la red virtual de la máquina virtual de origen.  

## <a name="prerequisites"></a>requisitos previos
Antes de asignar redes, asegúrese de haber creado una [red virtual de Azure](../virtual-network/virtual-networks-overview.md) en las regiones de Azure de origen y destino.

## <a name="map-virtual-networks"></a>Asignación de redes virtuales

Para asignar una red virtual de Azure ubicada en una región de Azure (red de origen) a una red virtual ubicada en otra región (red de destino), en Azure Virtual Machines, vaya a **Infraestructura de Site Recovery** > **Asignación de red**. Cree una asignación de red.

![Ventana de asignaciones de red: creación de una asignación de red](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


En el ejemplo siguiente, la máquina virtual se ejecuta en la región de Asia Oriental. La máquina virtual se replica en la región de Asia Suroriental.

Para crear una asignación de red desde la región de Asia Oriental a la región de Asia Suroriental, seleccione la ubicación de la red de origen y la ubicación de la red de destino. Después, seleccione **Aceptar**.

![Ventana para agregar asignaciones de red: selección de las ubicaciones de origen y destino de la red de origen](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Repita el proceso anterior para crear una asignación de red desde la región de Asia Suroriental a la región de Asia Oriental.

![Panel para agregar asignaciones de red: selección de las ubicaciones de origen y destino de la red de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Asignación de una red al habilitar la replicación

Cuando se replica una máquina virtual desde una región de Azure a otra por primera vez, si no existe ninguna asignación de red, puede establecer la red de destino al configurar la replicación. En función de esta configuración, Azure Site Recovery crea asignaciones de red desde la región de origen a la región de destino y desde la región de destino a la región de origen.   

![Panel de configuración: elección de la ubicación de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

De forma predeterminada, Site Recovery crea una red en la región de destino que es idéntica a la red de origen. Site Recovery crea una red con la adición de **-asr** como sufijo al nombre de la red de origen. Para elegir una red ya creada, seleccione **Personalizar**.

![Panel para personalizar la configuración de destino: establecer el nombre del grupo de recursos de destino y de la red virtual de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Si ya se ha realizado la asignación de red, no puede modificar la red virtual de destino al habilitar la replicación. En este caso, para cambiar la red virtual de destino, modifique la asignación de red existente.  

![Panel para personalizar la configuración de destino: establecer el nombre del grupo de recursos de destino](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Panel para modificar la asignación de red: modificar el nombre de una red virtual de destino existente](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Si modifica la asignación de red de la región A a la región B, asegúrese de modificar también la asignación de red de la región B a la A.
>
>


## <a name="subnet-selection"></a>Selección de subred
La subred de la máquina virtual de destino se selecciona en función del nombre de la subred de la máquina virtual de origen. Si hay una subred con el mismo nombre que el de la máquina virtual de origen disponible en la red de destino, se define esa subred para la máquina virtual de destino. Si no hay ninguna subred con el mismo nombre en la red de destino, se establece la primera red en orden alfabético como subred de destino. 

Para modificar la subred, vaya a la opción **Proceso y red** de la máquina virtual.

![Ventana Propiedades de Compute de Proceso y red](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Dirección IP

La dirección IP de cada interfaz de red de la máquina virtual de destino se define como se describe en las secciones siguientes.

### <a name="dhcp"></a>DHCP
Si la interfaz de red de la máquina virtual de origen usa DHCP, la interfaz de red de la máquina virtual de destino también se configura para que use DHCP.

### <a name="static-ip-address"></a>Dirección IP estática
Si la interfaz de red de la máquina virtual de origen usa una dirección IP estática, la interfaz de red de la máquina virtual de destino también se configura para que use una dirección IP estática. En las secciones siguientes se describe cómo configurar una dirección IP estática.

#### <a name="same-address-space"></a>Mismo espacio de direcciones

Si la subred de origen y la de destino tienen el mismo espacio de direcciones, la dirección IP de la interfaz de red de la máquina virtual de origen se establece como dirección IP de destino. Si la misma dirección IP no está disponible, la siguiente dirección IP disponible se establece como la dirección IP de destino.

#### <a name="different-address-spaces"></a>Distintos espacios de direcciones

Si la subred de origen y la de destino tienen distintos espacios de direcciones, la siguiente dirección IP disponible en la subred de destino se establece como la dirección IP de destino.

Para modificar la dirección IP de destino de cada interfaz de red, vaya a la configuración de **Proceso y red** de la máquina virtual.

## <a name="next-steps"></a>pasos siguientes

* Consulte la [Guía de redes para la replicación de máquinas virtuales de Azure](site-recovery-azure-to-azure-networking-guidance.md).
