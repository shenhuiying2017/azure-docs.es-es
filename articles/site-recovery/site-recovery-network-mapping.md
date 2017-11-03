---
title: "Planear la asignación de red para la replicación de máquinas virtuales de Hyper-V con Site Recovery | Microsoft Docs"
description: "Configure la asignación de red para la replicación de máquinas virtuales de Hyper-V desde un centro de datos local a Azure, o a un sitio secundario."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: tysonn
ms.assetid: fcaa2f52-489d-4c1c-865f-9e78e000b351
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 91d6d0466789daa662162c60bc3c97ba6115e7eb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="plan-network-mapping-for-hyper-v-vm-replication-with-site-recovery"></a>Planear la asignación de red para la replicación de máquinas virtuales de Hyper-V con Site Recovery



Este artículo le ayuda a entender y planear la asignación de red durante la replicación de máquinas virtuales de Hyper-V en Azure, o en un sitio secundario, mediante el [servicio Azure Site Recovery](site-recovery-overview.md).

Cuando haya terminado de leer este artículo, publique cualquier comentario en la parte inferior de este artículo, o bien realice preguntas técnicas en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-for-replication-to-azure"></a>Asignación de redes para la replicación en Azure

La asignación de red se usa cuando se replican máquinas virtuales de Hyper-V (administradas en VMM) en Azure. La asignación de red se produce entre redes de máquinas virtuales en un servidor VMM de origen y redes de Azure de destino. La asignación hace lo siguiente:

- **Conexión de red**: garantiza que las máquinas virtuales replicadas de Azure se conecten a la red asignada. Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, aunque lo hagan en planes de recuperación diferentes.
- **Puerta de enlace de red**: si se configura una puerta de enlace de red en la red de Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.

Observe lo siguiente:

- Una red de máquinas virtuales de VMM de origen se asigna a una red virtual de Azure.
- Después de la conmutación por error, las máquinas virtuales de Azure en la red de origen se conectarán a la red virtual de destino asignada.
- Las nuevas máquinas virtuales agregadas a la red de máquinas virtuales de origen se conectarán a la red de Azure asignada cuando se produzca la replicación.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error.
- Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>Asignación de red para la replicación en un centro de datos secundario

La asignación de red se usa al replicar máquinas virtuales de Hyper-V (administradas en System Center Virtual Machine Manager (VMM)) en un centro de datos secundario. La asignación de red se produce entre redes de máquinas virtuales en un servidor VMM de origen y redes de máquinas virtuales en un servidor VMM de destino. La asignación hace lo siguiente:

- **Conexión de red**: las máquinas virtuales se conectan a las redes adecuadas después de la conmutación por error. La máquina virtual de réplica se conectará a la red de destino que se asigna a la red de origen.
- **Ubicación óptima**: coloca las máquinas virtuales de réplica en los servidores host de Hyper-V de forma óptima. Las máquinas virtuales de réplica se colocarán en los hosts que pueden tener acceso a las redes de máquinas virtuales asignadas.
- **Sin asignación de redes**: si no configura la asignación de redes, las máquinas virtuales de réplica no se conectarán a ninguna red de máquinas virtuales después de la conmutación por error.

Observe lo siguiente:

- La asignación de redes se puede configurar entre redes de máquinas virtuales en dos servidores VMM o en un único servidor VMM si el mismo servidor administra dos sitios.
- Cuando se configura correctamente la asignación y se habilita la replicación, se conectará una máquina virtual de la ubicación principal a una red y su réplica de la ubicación de destino se conectará a la red asignada.
-
- Si las redes se han configurado correctamente en VMM, al seleccionar una red de máquinas virtuales de destino durante la asignación de redes, se mostrará las nubes VMM de origen que utilizan la red de máquinas virtuales de origen, junto con las redes de máquinas virtuales de destino disponibles en las nubes de destino que se usan para la protección.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.



### <a name="example"></a>Ejemplo

Este es un ejemplo para ilustrar este mecanismo. Vamos a utilizar una organización con dos ubicaciones en Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Redes de máquinas virtuales** | **Asignado a**
---|---|---|---
Nueva York | VMM-NewYork| VMNetwork1-NewYork | Asignado a VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | No asignado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Asignado a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | No asignado

En este ejemplo:

- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual que está conectada a VMNetwork1-NewYork, se conectará a VMNetwork1-Chicago.
- Cuando se crea una máquina virtual de réplica para VMNetwork2-NewYork o VMNetwork2-Chicago, no se conectará a ninguna red.

Aquí es cómo se configuran las nubes de VMM en nuestra organización de ejemplo y las redes lógicas asociadas a las nubes.

#### <a name="cloud-protection-settings"></a>Configuración de la protección de la nube

**Nube protegida** | **Proteger nube** | **Red lógica (Nueva York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Configuración de la red lógica y máquinas virtuales

**Ubicación** | **Red lógica** | **Red de VM asociada**
---|---|---
Nueva York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Configuración de red de destino

Según esta configuración, cuando selecciona la red de máquinas virtuales de destino, en la siguiente tabla se muestran las opciones que estarán disponibles.

**Selección** | **Nube protegida** | **Proteger nube** | **Red de destino disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | No disponible
 | GoldCloud1 | GoldCloud2 | Disponible


Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


#### <a name="failback-behavior"></a>Comportamiento de conmutación por recuperación

Para ver lo que ocurre en el caso de conmutación por recuperación (replicación inversa), supongamos que VMNetwork1-NewYork se asigna a VMNetwork1-Chicago, con la siguiente configuración.


**Máquina virtual** | **Conectada a la red de VM**
---|---
VM1 | VMNetwork1-Network
VM2 (réplica de VM1) | VMNetwork1-Chicago

Con esta configuración, revisemos lo que ocurre en un par de escenarios posibles.

**Escenario** | **Resultado**
---|---
Sin cambios en las propiedades de red de VM-2 después de la conmutación por error. | VM-1 sigue estando conectada a la red de origen
Las propiedades de red de VM-2 cambian después de la conmutación por error y está desconectada. | VM-1 se desconecta.
Las propiedades de red de VM-2 cambian después de la conmutación por error y está conectada a VMNetwork2-Chicago. | Si no está asignada VMNetwork2-Chicago, se desconectará VM-1.
Se cambia la asignación de redes de VMNetwork1-Chicago. | VM-1 se conectará a la red ahora asignada a VMNetwork1-Chicago.



## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la [planificación de la infraestructura de red](site-recovery-network-design.md).
