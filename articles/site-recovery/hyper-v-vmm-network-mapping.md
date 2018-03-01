---
title: "Asignación de red para la replicación de máquinas virtuales de Hyper-V en nubes de VMM con Site Recovery | Microsoft Docs"
description: "Describe la configuración de la asignación de red para la replicación de máquinas virtuales administradas de Hyper-V en nubes de VMM con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: 5b8ebf3bd118a7b082949b3f3c6ef60a07641ba1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="about-network-mapping-for-hyper-v-vm-with-vmm-replication"></a>Asignación de red para máquinas virtuales de Hyper-V con replicación de VMM 


Este artículo le ayuda a entender y planear la asignación de red durante la replicación de máquinas virtuales de Hyper-V en nubes de System Center Virtual Machine Manager (VMM) mediante el [servicio Azure Site Recovery](site-recovery-overview.md).

La asignación de red se usa cuando se replican máquinas virtuales administradas de Hyper-V en una nube de VMM, en una nube secundaria de VMM o en Azure.

## <a name="prepare-network-mapping-for-replication-to-azure"></a>Preparación de la asignación de red para la replicación en Azure

Cuando se replica en Azure, la asignación de red se produce entre redes de máquinas virtuales en un servidor VMM de origen y redes virtuales de Azure de destino. La asignación hace lo siguiente:
    -  **Conexión de red**: garantiza que las máquinas virtuales replicadas de Azure se conecten a la red asignada. Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, aunque lo hagan en planes de recuperación diferentes.
    - **Puerta de enlace de red**: si se configura una puerta de enlace de red en la red de Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.

La asignación de red funciona del siguiente modo:

- Una red de máquinas virtuales de VMM de origen se asigna a una red virtual de Azure.
- Después de la conmutación por error, las máquinas virtuales de Azure en la red de origen se conectarán a la red virtual de destino asignada.
- Las nuevas máquinas virtuales agregadas a la red de máquinas virtuales de origen se conectarán a la red de Azure asignada cuando se produzca la replicación.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error.
- Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Preparación de la asignación de red para la replicación en un sitio secundario

Cuando se replica en un sitio secundario, la asignación de red se produce entre redes de máquinas virtuales en un servidor VMM de origen y redes de máquinas virtuales en un servidor VMM de destino. La asignación hace lo siguiente:

- **Conexión de red**: las máquinas virtuales se conectan a las redes adecuadas después de la conmutación por error. La máquina virtual de réplica se conectará a la red de destino que se asigna a la red de origen.
- **Ubicación óptima de las máquinas virtuales**: las máquinas virtuales de réplica se ubican en los servidores host de Hyper-V de forma óptima. Las máquinas virtuales de réplica se colocarán en los hosts que pueden tener acceso a las redes de máquinas virtuales asignadas.
- **Sin asignación de redes**: si no configura la asignación de redes, las máquinas virtuales de réplica no se conectarán a ninguna red de máquinas virtuales después de la conmutación por error.

La asignación de red funciona del siguiente modo:

- La asignación de redes se puede configurar entre redes de máquinas virtuales en dos servidores VMM o en un único servidor VMM si el mismo servidor administra dos sitios.
- Cuando se configura correctamente la asignación y se habilita la replicación, se conectará una máquina virtual de la ubicación principal a una red y su réplica de la ubicación de destino se conectará a la red asignada.
- Al seleccionar una red de máquinas virtuales de destino durante la asignación de red en Site Recovery, se mostrarán las nubes de VMM de origen que utilizan la red de máquinas virtuales de origen, junto con las redes de máquinas virtuales de destino disponibles en las nubes de destino que se usan para la protección.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## <a name="example"></a>Ejemplo

Este es un ejemplo para ilustrar este mecanismo. Vamos a utilizar una organización con dos ubicaciones en Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Redes de máquinas virtuales** | **Asignado a**
---|---|---|---
Nueva York | VMM-NewYork| VMNetwork1-NewYork | Asignado a VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | No asignado
Chicago | VMM-Chicago| VMNetwork1-Chicago | Asignado a VMNetwork1-NewYork
 | | VMNetwork1-Chicago | No asignado

En este ejemplo:

- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual conectada a VMNetwork1-NewYork, se conectará a VMNetwork1-Chicago.
- Cuando se crea una máquina virtual de réplica para VMNetwork2-NewYork o VMNetwork2-Chicago, no se conectará a ninguna red.

Aquí es cómo se configuran las nubes de VMM en nuestra organización de ejemplo y las redes lógicas asociadas a las nubes.

### <a name="cloud-protection-settings"></a>Configuración de la protección de la nube

**Nube protegida** | **Proteger nube** | **Red lógica (Nueva York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>N/D</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Configuración de la red lógica y máquinas virtuales

**Ubicación** | **Red lógica** | **Red de VM asociada**
---|---|---
Nueva York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Configuración de red de destino

Según esta configuración, cuando selecciona la red de máquinas virtuales de destino, en la siguiente tabla se muestran las opciones que estarán disponibles.

**Selección** | **Nube protegida** | **Proteger nube** | **Red de destino disponible**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Disponible
 | GoldCloud1 | GoldCloud2 | Disponible
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | No disponible
 | GoldCloud1 | GoldCloud2 | Disponible


Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.


### <a name="failback-behavior"></a>Comportamiento de conmutación por recuperación

Para ver lo que ocurre en el caso de conmutación por recuperación (replicación inversa), supongamos que VMNetwork1-NewYork se asigna a VMNetwork1-Chicago, con la siguiente configuración.


**VM** | **Conectada a la red de VM**
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



## <a name="next-steps"></a>pasos siguientes

[Información sobre](hyper-v-vmm-networking.md) el direccionamiento IP tras la conmutación por error a un sitio de VMM secundario.
